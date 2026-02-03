# Rails Performance Optimization

*Comprehensive guide for optimizing Rails 8 applications. Use as a checklist and reference for coding agents.*

## Philosophy (DHH / Rails Way)

> "Disks have gotten fast enough that we don't need RAM for as many tasks."

Rails 8's performance philosophy is **conceptual compression** — reduce dependencies, reduce complexity:

- **Solid Cache over Redis** — SSD-backed caching is the Rails 8 default. No Redis needed for most apps.
- **Solid Queue over Sidekiq** — Database-backed jobs. No Redis dependency.
- **Solid Cable over Redis** — WebSocket pubsub via database polling.
- **Thruster over Nginx** — Rails 8 ships with Thruster (Go-based HTTP/2 proxy) built into the Dockerfile. Handles TLS, gzip, asset caching, X-Sendfile. No Nginx needed.
- **Hotwire over SPAs** — Turbo + Stimulus eliminates the need for React/Vue/heavy JS. This IS the performance strategy — not "optimize your SPA" but "don't build one."
- **Kamal 2 over PaaS** — Deploy anywhere with `kamal setup`. No Heroku/Render tax.

**The Rails 8 performance stack: Puma + Thruster + Solid Cache + Solid Queue + Hotwire. That's it.**

Before reaching for Redis, Nginx, or external services — ask if Rails 8's built-in tools already solve it.

---

## ⚡ Quick Reference

| Problem | Solution | Tool |
|---------|----------|------|
| N+1 queries | `includes(:association)` | Bullet gem |
| Slow queries | Add indexes, optimize joins | EXPLAIN ANALYZE |
| Memory bloat | `find_each`, `pluck`, `select` | memory_profiler |
| Slow pages | Fragment caching | rack-mini-profiler |
| Heavy requests | Move to background jobs | Solid Queue |
| Object allocation | Freeze strings, reduce objects | stackprof |
| Slow server | Tune Puma workers/threads | `WEB_CONCURRENCY` |
| Large payloads | Turbo Frames, lazy loading | Hotwire |

---

## 1. Database Optimization

### 1.1 N+1 Query Detection (Bullet Gem)

**Setup:**
```ruby
# Gemfile
group :development, :test do
  gem 'bullet'
end

# config/environments/development.rb
config.after_initialize do
  Bullet.enable = true
  Bullet.alert = true          # Browser popup
  Bullet.bullet_logger = true  # log/bullet.log
  Bullet.console = true        # Browser console
  Bullet.rails_logger = true   # Rails log
  Bullet.add_footer = true     # Page footer
end

# config/environments/test.rb
config.after_initialize do
  Bullet.enable = true
  Bullet.raise = true  # Fail tests on N+1
end
```

**RSpec integration:**
```ruby
# spec/rails_helper.rb
if Bullet.enable?
  config.before(:each) { Bullet.start_request }
  config.after(:each) do
    Bullet.perform_out_of_channel_notifications if Bullet.notification?
    Bullet.end_request
  end
end
```

**Checklist:**
- [ ] Bullet gem installed and configured for dev + test
- [ ] `Bullet.raise = true` in test env (fails tests on N+1)
- [ ] Check `log/bullet.log` regularly
- [ ] Browser footer shows no warnings

### 1.2 Eager Loading

**Three methods — when to use each:**

```ruby
# includes — Rails auto-picks preload or eager_load
# USE: Most cases. Rails optimizes for you.
Event.includes(:venue).each { |e| e.venue.name }
# => 2 queries: SELECT events; SELECT venues WHERE id IN (...)

# preload — Always separate queries
# USE: When you DON'T need to filter/order by association
Event.preload(:venue, :organizer)
# => 3 queries (one per table)

# eager_load — Always LEFT OUTER JOIN
# USE: When you need to filter/order by association columns
Event.eager_load(:venue).where(venues: { city: 'London' })
# => 1 query with JOIN
```

**Comparison table:**

| Method | Strategy | Filter by assoc? | Use when |
|--------|----------|-----------------|----------|
| `includes` | Auto | Sometimes | Default choice |
| `preload` | Separate queries | No | Multiple unrelated assocs |
| `eager_load` | LEFT JOIN | Yes | Filtering/ordering by assoc |

**Nested eager loading:**
```ruby
# Multiple associations
Event.includes(:venue, :organizer, :category)

# Nested
Event.includes(venue: :address, vendors: :category)

# Deep nesting
Event.includes(
  :venue,
  vendors: [:category, { reviews: :user }],
  comments: :user
)
```

**Strict loading (prevent lazy loads entirely):**
```ruby
# Per-query
events = Event.strict_loading.all
events.first.venue  # => ActiveRecord::StrictLoadingViolationError

# Per-model
class Event < ApplicationRecord
  self.strict_loading_by_default = true
end

# Per-association
has_many :comments, strict_loading: true
```

### 1.3 Indexes

**Rules:**
- Every `belongs_to` foreign key needs an index
- Every column in `WHERE`, `ORDER BY`, or `JOIN` needs an index
- Composite indexes: put the most selective column first
- Partial indexes for common filtered queries

```ruby
# Migration examples

# Basic index
add_index :expenses, :category_id

# Composite index (column order matters!)
add_index :expenses, [:user_id, :created_at]

# Unique index
add_index :users, :email, unique: true

# Partial index (only index rows matching condition)
add_index :expenses, :status, where: "status = 'pending'"
add_index :invoices, :due_date, where: "paid = false"

# Covering index (includes extra columns to avoid table lookup)
add_index :expenses, [:category_id, :amount], 
          include: [:description]
```

**Check for missing indexes:**
```bash
# In Rails console
ActiveRecord::Base.connection.tables.each do |table|
  columns = ActiveRecord::Base.connection.columns(table)
  indexes = ActiveRecord::Base.connection.indexes(table)
  indexed_cols = indexes.flat_map(&:columns)
  
  columns.select { |c| c.name.end_with?('_id') }.each do |col|
    unless indexed_cols.include?(col.name)
      puts "⚠️  Missing index: #{table}.#{col.name}"
    end
  end
end
```

### 1.4 Counter Caches

Avoid `COUNT(*)` queries for association counts:

```ruby
# Migration
add_column :categories, :expenses_count, :integer, default: 0, null: false

# Reset existing counts
Category.find_each do |cat|
  Category.reset_counters(cat.id, :expenses)
end

# Model
class Expense < ApplicationRecord
  belongs_to :category, counter_cache: true
end

# Now this uses the cached column (no query):
category.expenses_count  # => 42
# Instead of:
category.expenses.count  # => SELECT COUNT(*) ...
```

### 1.5 Query Optimization

```ruby
# BAD: Loads all columns into Ruby objects
User.all.map(&:email)

# GOOD: Returns flat array, no AR objects
User.pluck(:email)

# BAD: Loads all records into memory
Expense.all.each { |e| process(e) }

# GOOD: Loads in batches of 1000
Expense.find_each(batch_size: 1000) { |e| process(e) }

# BAD: Loads record to check existence
User.find_by(email: email).present?

# GOOD: Single EXISTS query
User.exists?(email: email)

# BAD: Selects all columns
Expense.where(status: 'pending')

# GOOD: Select only needed columns
Expense.where(status: 'pending').select(:id, :amount, :vendor)

# BAD: Ruby-side filtering
Expense.all.select { |e| e.amount > 100 }

# GOOD: Database-side filtering
Expense.where('amount > ?', 100)

# Aggregations — let the DB do the math
Expense.sum(:amount)
Expense.group(:category_id).sum(:amount)
Expense.group(:category_id).count
```

### 1.6 Connection Pooling

```yaml
# config/database.yml
production:
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  checkout_timeout: 5
  idle_timeout: 300
  reaping_frequency: 10
```

**Rule: `pool` >= Puma threads per worker**

```ruby
# Monitor pool usage
ActiveRecord::Base.connection_pool.stat
# => {size: 5, connections: 3, busy: 1, dead: 0, idle: 2, waiting: 0, checkout_timeout: 5}
```

---

## 2. Caching Strategies

### 2.1 Fragment Caching

```erb
<%# Cache expensive view fragments %>
<% cache @expense do %>
  <div class="expense-card">
    <%= render @expense %>
  </div>
<% end %>

<%# Cache with explicit key %>
<% cache [:v2, @expense] do %>
  ...
<% end %>

<%# Collection caching %>
<%= render partial: 'expense', collection: @expenses, cached: true %>
```

### 2.2 Russian Doll Caching

```ruby
# Models — touch parent when child changes
class Expense < ApplicationRecord
  belongs_to :category, touch: true
end

class Category < ApplicationRecord
  has_many :expenses
end
```

```erb
<%# Outer cache busts when any expense in category changes %>
<% cache @category do %>
  <h2><%= @category.name %></h2>
  <% @category.expenses.each do |expense| %>
    <%# Inner cache busts only for changed expense %>
    <% cache expense do %>
      <%= render expense %>
    <% end %>
  <% end %>
<% end %>
```

### 2.3 Low-Level Caching

```ruby
# Basic fetch (reads cache or executes block)
Rails.cache.fetch("dashboard_stats", expires_in: 15.minutes) do
  {
    total_expenses: Expense.sum(:amount),
    pending_count: Expense.pending.count,
    monthly_total: Expense.where(date: Date.current.all_month).sum(:amount)
  }
end

# Fetch multi (batch cache reads)
Rails.cache.fetch_multi(*categories, expires_in: 1.hour) do |category|
  category.expenses.sum(:amount)
end

# Manual write/read/delete
Rails.cache.write("key", value, expires_in: 1.hour)
Rails.cache.read("key")
Rails.cache.delete("key")
Rails.cache.delete_matched("expenses/*")  # Pattern delete

# Increment/decrement (atomic)
Rails.cache.increment("page_views/#{@page.id}")
```

### 2.4 HTTP Caching

```ruby
class ExpensesController < ApplicationController
  def index
    @expenses = Expense.recent
    
    # ETag — returns 304 Not Modified if unchanged
    if stale?(@expenses)
      respond_to do |format|
        format.html
        format.json { render json: @expenses }
      end
    end
  end

  def show
    @expense = Expense.find(params[:id])
    
    # Time-based expiry
    expires_in 5.minutes, public: true
    
    # Or ETag-based
    fresh_when(@expense)
  end
end
```

### 2.5 Cache Store Comparison

| Store | Speed | Persistence | Shared | Use when |
|-------|-------|-------------|--------|----------|
| **Solid Cache** | **Fast (SSD)** | **Yes** | **Yes** | **Rails 8 default. Use this first.** |
| Memory | Fastest | No | No | Dev, single process |
| Redis | Fast | Yes | Yes | Already have Redis, need sub-ms latency |
| Memcached | Fast | No | Yes | Legacy apps, simple keys |

> DHH: "You're now usually better off keeping a huge cache on disk rather than a small cache in memory."

```ruby
# config/environments/production.rb

# Solid Cache (Rails 8 default — USE THIS)
# Backed by SSD via database. Huge cache, no Redis needed.
config.cache_store = :solid_cache_store

# Install: bin/rails solid_cache:install
# Configure: config/cache.yml + config/database.yml (cache db)
# Run: bin/rails db:prepare

# Redis (only if you already have Redis infrastructure)
config.cache_store = :redis_cache_store, {
  url: ENV['REDIS_URL'],
  expires_in: 1.hour,
  race_condition_ttl: 10.seconds
}
```

---

## 3. Background Jobs (Solid Queue)

### 3.1 Setup

```ruby
# Gemfile (included in Rails 8 by default)
gem "solid_queue"

# config/solid_queue.yml
production:
  dispatchers:
    - polling_interval: 1
      batch_size: 500
  workers:
    - queues: "critical,default,low"
      threads: 5
      processes: 2
      polling_interval: 0.1
```

### 3.2 Job Patterns

```ruby
# Move slow work out of requests
class ExpenseCategorizeJob < ApplicationJob
  queue_as :default
  retry_on StandardError, wait: :polynomially_longer, attempts: 3
  discard_on ActiveRecord::RecordNotFound

  def perform(expense_id)
    expense = Expense.find(expense_id)
    category = AiCategorizer.classify(expense.description)
    expense.update!(category: category)
  end
end

# Enqueue
ExpenseCategorizeJob.perform_later(expense.id)

# Scheduled
ExpenseCategorizeJob.set(wait: 5.minutes).perform_later(expense.id)

# Queue priority
class CriticalNotificationJob < ApplicationJob
  queue_as :critical
end
```

### 3.3 When to Background

- [ ] API calls to external services (Xero, Stripe, etc.)
- [ ] Email sending
- [ ] File processing (CSV imports, PDF generation)
- [ ] AI/ML inference
- [ ] Complex calculations or reports
- [ ] Anything taking >200ms in a request

---

## 4. Asset Optimization

### 4.1 Image Optimization

```erb
<%# Lazy loading (native browser) %>
<%= image_tag "photo.jpg", loading: "lazy" %>

<%# Responsive images %>
<%= image_tag "photo.jpg", 
    srcset: "photo-400.jpg 400w, photo-800.jpg 800w",
    sizes: "(max-width: 600px) 400px, 800px" %>
```

### 4.2 Compression

```ruby
# config/environments/production.rb
config.middleware.use Rack::Deflater  # gzip responses

# Nginx (better — handles compression at proxy level)
# gzip on;
# gzip_types text/html text/css application/javascript application/json;
# gzip_min_length 1000;
```

### 4.3 CDN

```ruby
# config/environments/production.rb
config.asset_host = ENV['CDN_HOST']  # e.g., "https://cdn.example.com"
config.action_controller.asset_host = ENV['CDN_HOST']
```

---

## 5. Ruby & Rails Optimization

### 5.1 Frozen String Literals

```ruby
# Add to top of EVERY Ruby file
# frozen_string_literal: true

# This prevents string mutation and reduces object allocation
# Without it, every string literal creates a new object each time
```

### 5.2 YJIT (Ruby 3.2+)

```bash
# Enable YJIT (15-25% faster for Rails)
export RUBY_YJIT_ENABLE=1

# Or in config/boot.rb
RubyVM::YJIT.enable if defined?(RubyVM::YJIT)

# Verify it's running
RubyVM::YJIT.enabled?  # => true
RubyVM::YJIT.runtime_stats  # => {inline_code_size: ..., ...}
```

### 5.3 Object Allocation Reduction

```ruby
# BAD: Creates new array every call
def statuses
  ['pending', 'approved', 'rejected']
end

# GOOD: Frozen constant, allocated once
STATUSES = %w[pending approved rejected].freeze
def statuses
  STATUSES
end

# BAD: String concatenation creates intermediaries
name = first_name + ' ' + last_name

# GOOD: Interpolation or buffer
name = "#{first_name} #{last_name}"

# BAD: Each iteration creates a new hash
users.map { |u| { id: u.id, name: u.name } }

# GOOD: Use pluck to avoid AR object creation entirely
User.pluck(:id, :name).map { |id, name| { id: id, name: name } }
```

### 5.4 GC Tuning

```bash
# Environment variables for production
export RUBY_GC_HEAP_INIT_SLOTS=600000
export RUBY_GC_HEAP_FREE_SLOTS=200000
export RUBY_GC_HEAP_GROWTH_FACTOR=1.25
export RUBY_GC_HEAP_GROWTH_MAX_SLOTS=300000
export RUBY_GC_MALLOC_LIMIT=64000000
export RUBY_GC_OLDMALLOC_LIMIT=64000000
```

---

## 6. Profiling Tools

### 6.1 rack-mini-profiler

```ruby
# Gemfile
group :development do
  gem 'rack-mini-profiler'
  gem 'stackprof'       # For flamegraphs
  gem 'memory_profiler'  # For memory profiling
end

# Visit any page — badge appears in top-left
# Append to URL:
# ?pp=flamegraph        — CPU flamegraph
# ?pp=profile-memory    — Memory allocation breakdown
# ?pp=profile-gc        — GC stats
# ?pp=analyze-memory    — Object allocation details
```

### 6.2 memory_profiler

```ruby
require 'memory_profiler'

report = MemoryProfiler.report do
  # Code to profile
  100.times { Expense.where(status: 'pending').to_a }
end

report.pretty_print(to_file: 'memory_report.txt')
# Shows: allocated objects, retained objects, by gem, by file, by location
```

### 6.3 stackprof

```ruby
StackProf.run(mode: :cpu, out: 'tmp/stackprof.dump') do
  # Code to profile
end

# View results
# $ stackprof tmp/stackprof.dump --text
# $ stackprof tmp/stackprof.dump --flamegraph > tmp/flamegraph.json
```

### 6.4 derailed_benchmarks

```bash
# Gemfile
gem 'derailed_benchmarks', group: :development

# Boot time
$ bundle exec derailed exec perf:allocated_objects
$ bundle exec derailed bundle:mem    # Memory per gem at boot
$ bundle exec derailed bundle:objects # Objects per gem at boot

# Request benchmarking
$ PATH_TO_HIT=/expenses bundle exec derailed exec perf:mem
$ PATH_TO_HIT=/expenses bundle exec derailed exec perf:ips
```

### 6.5 benchmark-ips

```ruby
require 'benchmark/ips'

Benchmark.ips do |x|
  x.report("find_each") { Expense.find_each { |e| e.id } }
  x.report("each")      { Expense.all.each { |e| e.id } }
  x.compare!
end
```

---

## 7. Server Tuning

### 7.0 Thruster (Rails 8 Default Proxy)

Thruster is a Go-based HTTP/2 proxy that ships with Rails 8. It replaces Nginx for most deployments.

```ruby
# Gemfile (included in Rails 8 by default)
gem 'thruster'

# Run with Thruster wrapping Puma
$ thrust bin/rails server

# With automatic TLS via Let's Encrypt
$ TLS_DOMAIN=myapp.example.com thrust bin/rails server
```

**What Thruster provides (zero config):**
- HTTP/2 support
- Automatic TLS certificates (Let's Encrypt)
- Gzip compression
- X-Sendfile (efficient static file serving)
- Asset caching (64MB default)

**Environment variables:**
| Variable | Default | Purpose |
|----------|---------|---------|
| `TLS_DOMAIN` | none | Domain for auto TLS |
| `TARGET_PORT` | 3000 | Puma port |
| `CACHE_SIZE` | 64MB | HTTP cache size |
| `MAX_CACHE_ITEM_SIZE` | 1MB | Max single cached item |

**When you DON'T need Nginx:** Most Rails 8 apps. Thruster handles TLS, compression, and caching.

**When you still need Nginx:** Multiple apps on one server with complex routing, advanced load balancing, or specific Nginx modules.

### 7.1 Puma Configuration

```ruby
# config/puma.rb
workers_count = ENV.fetch("WEB_CONCURRENCY") { Concurrent.processor_count }
threads_count = ENV.fetch("RAILS_MAX_THREADS") { 5 }

workers workers_count
threads threads_count, threads_count

preload_app!

on_worker_boot do
  ActiveRecord::Base.establish_connection
end

# Restart workers that consume too much memory
plugin :solid_queue if defined?(SolidQueue)
```

**Formulas:**
- **Workers** = CPU cores (for CPU-bound) or 1.5x cores (for IO-bound)
- **Threads** = 5 is a good default; increase if IO-heavy
- **DB pool** >= threads per worker
- **RAM** = workers × (per-worker memory ~256-512MB)

### 7.2 Nginx Tuning

```nginx
upstream rails {
  server 127.0.0.1:3000;
  keepalive 16;  # Persistent connections to Puma
}

server {
  listen 80;
  
  # Gzip
  gzip on;
  gzip_types text/html text/css application/javascript application/json image/svg+xml;
  gzip_min_length 1000;
  
  # Static assets (bypass Rails)
  location /assets/ {
    root /app/public;
    expires max;
    add_header Cache-Control public;
  }

  # Proxy to Rails
  location / {
    proxy_pass http://rails;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $host;
    proxy_http_version 1.1;
    proxy_set_header Connection "";  # Enable keepalive
  }
}
```

---

## 8. Frontend Optimization (Hotwire)

### 8.1 Turbo Frames (Partial Page Updates)

```erb
<%# Only this frame reloads, not the whole page %>
<%= turbo_frame_tag "expenses_list" do %>
  <%= render @expenses %>
  <%= link_to "Next", expenses_path(page: @page + 1) %>
<% end %>
```

### 8.2 Lazy-Loaded Frames

```erb
<%# Frame loads its content after page renders %>
<%= turbo_frame_tag "dashboard_stats", src: dashboard_stats_path, loading: :lazy do %>
  <div class="animate-pulse bg-gray-700 h-20 rounded"></div>
<% end %>
```

### 8.3 Infinite Scroll with Turbo

```erb
<%# app/views/expenses/index.html.erb %>
<%= turbo_frame_tag "expenses" do %>
  <% @expenses.each do |expense| %>
    <%= render expense %>
  <% end %>
  
  <% if @expenses.next_page? %>
    <%= turbo_frame_tag "expenses", 
        src: expenses_path(page: @expenses.next_page),
        loading: :lazy do %>
      <div class="text-center py-4">Loading more...</div>
    <% end %>
  <% end %>
<% end %>
```

### 8.4 Turbo Streams (Real-Time Updates)

```ruby
# Controller — respond with stream
def create
  @expense = Expense.new(expense_params)
  if @expense.save
    respond_to do |format|
      format.turbo_stream
      format.html { redirect_to expenses_path }
    end
  end
end
```

```erb
<%# app/views/expenses/create.turbo_stream.erb %>
<%= turbo_stream.prepend "expenses_list" do %>
  <%= render @expense %>
<% end %>

<%= turbo_stream.update "expenses_count" do %>
  <%= @expenses_count %>
<% end %>
```

### 8.5 Stimulus (Minimal JS)

```javascript
// app/javascript/controllers/lazy_load_controller.js
import { Controller } from "@hotwired/stimulus"

export default class extends Controller {
  static targets = ["content"]
  
  connect() {
    // Only load when element enters viewport
    const observer = new IntersectionObserver((entries) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          this.load()
          observer.unobserve(entry.target)
        }
      })
    })
    observer.observe(this.element)
  }
  
  async load() {
    const response = await fetch(this.element.dataset.url)
    this.contentTarget.innerHTML = await response.text()
  }
}
```

---

## 9. PostgreSQL Optimization

### 9.1 EXPLAIN ANALYZE

```sql
-- Always use ANALYZE (actually runs the query)
EXPLAIN ANALYZE SELECT * FROM expenses WHERE category_id = 5 AND status = 'pending';

-- What to look for:
-- ✅ Index Scan / Index Only Scan — using index
-- ⚠️ Seq Scan — full table scan (add an index!)
-- ⚠️ Nested Loop — N+1 at DB level
-- ❌ Sort (external) — sorting on disk (add index or increase work_mem)
```

**Red flags in EXPLAIN output:**
- `Seq Scan` on large tables (>10k rows)
- `actual time` much higher than `estimated`
- `rows=X` in plan vs `actual rows=Y` differ wildly (stale statistics → run ANALYZE)
- `Sort Method: external merge Disk` (increase `work_mem`)

### 9.2 Partial Indexes

```ruby
# Only index the rows you actually query
# Much smaller index, much faster lookups

# Only pending expenses (if you mostly query pending)
add_index :expenses, :created_at, where: "status = 'pending'", 
          name: 'idx_expenses_pending_created'

# Only unpaid invoices
add_index :invoices, :due_date, where: "paid = false",
          name: 'idx_invoices_unpaid_due'

# Only active users
add_index :users, :email, where: "active = true",
          name: 'idx_users_active_email'
```

### 9.3 Materialized Views

```ruby
# For expensive aggregation queries (dashboards, reports)

# Migration
class CreateExpenseSummaryView < ActiveRecord::Migration[8.0]
  def up
    execute <<-SQL
      CREATE MATERIALIZED VIEW expense_summaries AS
      SELECT 
        category_id,
        date_trunc('month', date) AS month,
        COUNT(*) AS count,
        SUM(amount) AS total,
        AVG(amount) AS average
      FROM expenses
      GROUP BY category_id, date_trunc('month', date);
      
      CREATE UNIQUE INDEX idx_expense_summaries 
        ON expense_summaries (category_id, month);
    SQL
  end

  def down
    execute "DROP MATERIALIZED VIEW IF EXISTS expense_summaries"
  end
end

# Refresh (run in background job, e.g., nightly)
class RefreshMaterializedViewsJob < ApplicationJob
  def perform
    ActiveRecord::Base.connection.execute(
      "REFRESH MATERIALIZED VIEW CONCURRENTLY expense_summaries"
    )
  end
end

# Model
class ExpenseSummary < ApplicationRecord
  self.table_name = 'expense_summaries'
  def readonly?; true; end
end
```

### 9.4 VACUUM and Maintenance

```sql
-- Check bloat
SELECT schemaname, tablename, 
       pg_size_pretty(pg_total_relation_size(schemaname||'.'||tablename)) AS size,
       n_dead_tup AS dead_rows,
       n_live_tup AS live_rows,
       round(n_dead_tup::numeric / greatest(n_live_tup, 1) * 100, 1) AS dead_pct
FROM pg_stat_user_tables
ORDER BY n_dead_tup DESC
LIMIT 20;

-- Manual vacuum (usually autovacuum handles this)
VACUUM ANALYZE expenses;

-- Update statistics (helps query planner)
ANALYZE expenses;
```

**PostgreSQL config tuning:**
```
# postgresql.conf
shared_buffers = 256MB          # 25% of RAM
effective_cache_size = 768MB    # 75% of RAM
work_mem = 16MB                 # Per-operation sort memory
maintenance_work_mem = 128MB    # For VACUUM, CREATE INDEX
random_page_cost = 1.1          # SSD (default 4.0 is for HDD)
```

---

## Quick Wins (Do These First)

1. **Enable Bullet gem** — find N+1 queries immediately
2. **Add `loading: "lazy"` to images** — instant page speed improvement
3. **Add missing foreign key indexes** — run the check script in §1.3
4. **Enable YJIT** — 15-25% faster, one env var
5. **Add `Rack::Deflater`** — gzip all responses
6. **Use `find_each` instead of `each`** — prevents memory spikes
7. **Add `# frozen_string_literal: true`** — to all Ruby files
8. **Fragment cache expensive partials** — biggest render wins
9. **Move external API calls to background jobs** — faster responses
10. **Run `EXPLAIN ANALYZE`** — on your slowest queries

---

## Complete Optimization Checklist

### Database
- [ ] Bullet gem enabled (dev + test)
- [ ] No N+1 queries in test suite
- [ ] All foreign keys indexed
- [ ] Composite indexes for common query patterns
- [ ] Counter caches for frequently counted associations
- [ ] `find_each` for batch processing
- [ ] `pluck` / `select` instead of full AR objects where possible
- [ ] Connection pool sized correctly

### Caching
- [ ] Fragment caching on expensive views
- [ ] Russian doll caching with `touch: true`
- [ ] Low-level caching for expensive computations
- [ ] HTTP caching (ETags / expires) on read-heavy endpoints
- [ ] Cache store configured for production

### Background Jobs
- [ ] External API calls in background jobs
- [ ] Email sending in background jobs
- [ ] File processing in background jobs
- [ ] Job retry and error handling configured
- [ ] Queue priorities set (critical > default > low)

### Ruby / Rails
- [ ] `# frozen_string_literal: true` on all files
- [ ] YJIT enabled
- [ ] Constants frozen (arrays, hashes, strings)
- [ ] No unnecessary object creation in hot paths

### Server
- [ ] Puma workers = CPU cores
- [ ] Puma threads tuned for workload
- [ ] DB pool >= threads
- [ ] Gzip/Brotli compression enabled
- [ ] Static assets served by Nginx/CDN

### Frontend
- [ ] Turbo Frames for partial page updates
- [ ] Lazy-loaded frames for below-fold content
- [ ] Images lazy-loaded
- [ ] Minimal JavaScript (Stimulus over custom JS)

### PostgreSQL
- [ ] EXPLAIN ANALYZE on slow queries
- [ ] No sequential scans on large tables
- [ ] Partial indexes for filtered queries
- [ ] Materialized views for dashboard aggregations
- [ ] Autovacuum running properly
- [ ] Statistics up to date

### Profiling (Regular)
- [ ] rack-mini-profiler in development
- [ ] Flamegraph review for slow pages
- [ ] Memory profiling for leaks
- [ ] Boot time check with derailed_benchmarks
- [ ] Load testing before major releases

---

## Automated Performance Testing

```ruby
# spec/performance/response_time_spec.rb
require 'rails_helper'

RSpec.describe 'Response times', type: :request do
  it 'loads dashboard under 500ms' do
    start = Time.now
    get root_path
    elapsed = Time.now - start
    expect(elapsed).to be < 0.5
  end

  it 'loads expenses index under 500ms' do
    create_list(:expense, 100)
    start = Time.now
    get expenses_path
    elapsed = Time.now - start
    expect(elapsed).to be < 0.5
  end
end
```

```bash
# Quick CLI performance check
time curl -s -o /dev/null -w "%{time_total}" http://localhost:3000/
# Should be < 0.5s for most pages
```
