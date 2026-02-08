# UK-TAX-ACCOUNTING.md — Comprehensive UK Ltd Company Tax Reference

*Clark's definitive guide for UK Ltd company accounting. Sources: gov.uk, HMRC guidance.*

---

## The Companies

| Company | Purpose | Director |
|---------|---------|----------|
| Super Veloce Ltd | Racing driver career | David Perel |
| Speed Capital Ltd | Coach Dave Delta, SimGrid, tech | David Perel |

---

## Corporation Tax

### Rates (2025/26)

| Profit Level | Rate | Notes |
|--------------|------|-------|
| £0 - £50,000 | 19% | Small profits rate |
| £50,001 - £250,000 | 19-25% | Marginal Relief applies |
| Over £250,000 | 25% | Main rate |

**Marginal Relief Formula:**
```
Tax = Profits × 25% - (£250,000 - Profits) × 3/200
```

**Thresholds reduced by:**
- Short accounting periods (pro-rata)
- Number of associated companies

### Payment Deadlines
- **Small companies:** 9 months + 1 day after accounting period end
- **Large companies:** Quarterly instalments
- **CT600 filing:** 12 months after accounting period end

---

## Director's Loan Account (DLA)

### What is a Director's Loan?
Money taken from company that is NOT:
- Salary or dividend
- Expense repayment
- Repayment of money you previously loaned to company

### Section 455 Tax

If director owes company money at year-end and doesn't repay within **9 months + 1 day**:

| Loan Date | Tax Rate |
|-----------|----------|
| Before 6 April 2022 | 32.5% |
| On or after 6 April 2022 | **33.75%** |

**Reclaiming S455:**
- Can reclaim after loan repaid (9 months + 1 day after repayment period ends)
- Cannot reclaim interest paid on the tax
- Claim within 4 years

### Benefit in Kind Threshold

If DLA > **£10,000** at any point in tax year:
- Treat as benefit in kind
- Report on P11D
- Deduct Class 1 NI
- Director pays tax on official interest rate (check [current HMRC rate](https://www.gov.uk/government/publications/rates-and-allowances-beneficial-loan-arrangements-hmrc-official-rates))

### Bed & Breakfasting Rules (Anti-Avoidance)

If loan > £15,000 and you repay then borrow again within 30 days:
- S455 tax still applies on original loan
- Cannot avoid by quick repay/re-borrow

### DLA Management Best Practices

**Monthly:**
- Reconcile all personal expenses paid by company
- Update DLA balance in accounting software
- Flag if approaching £10,000 (BIK threshold)

**Before Year-End:**
- Review total DLA position
- Plan repayment strategy:
  - Vote dividend to offset (if reserves available)
  - Actual cash repayment
  - Include in salary (creates tax/NI but clears DLA)
- If can't clear: budget for S455 tax

**Documentation:**
- Board minutes for any loans formally approved
- Clear categorization in accounts (drawings vs loans vs expenses)
- Dividend vouchers if using dividends to offset

**Warning Signs:**
- DLA growing faster than dividends can cover
- Using company as "personal piggy bank"
- Mixing business/personal cards regularly
- No monthly reconciliation

**The £10,000 Threshold:**
Once DLA exceeds £10,000 at ANY point in tax year:
- Entire beneficial loan becomes BIK
- Report on P11D
- Pay Class 1A NI (15%)
- Director pays income tax on official interest rate benefit
- This applies even if you repay before year-end

---

## Allowable Business Expenses (Deductible)

### Fully Deductible
- **Office costs:** Stationery, phone bills, postage
- **Travel:** Fuel, parking, train/bus fares (business journeys only)
- **Staff costs:** Salaries, NI contributions, pension contributions
- **Financial costs:** Insurance, bank charges, accountancy fees
- **Premises:** Rent, rates, utilities, repairs
- **Marketing:** Advertising, website costs
- **Professional subscriptions:** Trade bodies, professional memberships
- **Training:** Courses directly related to business

### Partially Deductible (Apportion)
- **Mixed-use phone:** Claim business % only
- **Home office:** Proportion based on rooms/time used
- **Vehicle:** Business mileage only

### NOT Deductible (Disallowable)
- Personal expenses (drawings)
- Entertainment (client entertaining)
- Fines and penalties
- Political donations
- Non-trade clothing
- Personal portion of mixed expenses

---

## Home Office Deductions

### For Ltd Companies (Actual Costs Only)
Ltd company directors **cannot** use simplified flat rates — those are for self-employed only.

Calculate proportion based on:
- Rooms used for business ÷ total rooms
- × Time spent working from home

**Claimable costs (apportioned):**
- Heating
- Electricity
- Council Tax (proportion)
- Mortgage interest/rent (proportion)
- Internet and phone (proportion)

**Method:** Company pays director a rent for use of home office. Must be:
- Reasonable amount
- Documented (licence agreement recommended)
- Director declares as rental income (but can offset expenses)

---

## Capital Allowances

### Annual Investment Allowance (AIA)
- **100% deduction** up to **£1,000,000** per year
- Applies to most plant and machinery
- Shared between associated companies

### Full Expensing (from 1 April 2023)
- **100%** first-year relief on qualifying plant and machinery
- Permanent for companies

### Writing Down Allowances (WDA)
When AIA limit exceeded or assets don't qualify:

| Pool Type | Rate |
|-----------|------|
| Main pool | 18% per year |
| Special rate pool | 6% per year |

**Special rate pool items:**
- Long-life assets (25+ years)
- Integral features (electrical, heating, lifts)
- High-emission cars (CO2 > 50g/km)

### Cars

| CO2 Emissions | Allowance |
|---------------|-----------|
| 0g/km (electric) | 100% first year |
| 1-50g/km | 18% WDA (main pool) |
| 51g/km+ | 6% WDA (special rate) |

---

## Mileage Allowance Payments (MAPs)

### HMRC Approved Rates

| Vehicle | First 10,000 miles | Over 10,000 miles |
|---------|-------------------|-------------------|
| Cars and vans | **45p/mile** | **25p/mile** |
| Motorcycles | 24p/mile | 24p/mile |
| Bicycles | 20p/mile | 20p/mile |

**Passenger payments:** Additional 5p/mile for carrying colleagues

- Pay **up to** these rates tax-free
- Pay **over** = report on P11D, taxable
- Pay **under** = employee can claim MAR (Mileage Allowance Relief)

---

## VAT

### Registration
- **Mandatory:** Taxable turnover > **£90,000** (rolling 12 months)
- **Voluntary:** Can register below threshold

### Standard Rates

| Type | Rate |
|------|------|
| Standard | 20% |
| Reduced | 5% |
| Zero | 0% |

---

## UK VAT Invoice Requirements (HMRC Compliant)

### Mandatory Elements for Full VAT Invoices (over £250)

A valid UK VAT invoice **must** include:

| Element | Description | Example |
|---------|-------------|---------|
| **Unique invoice number** | Sequential, no gaps | INV-2026-0001 |
| **Invoice date** | Date invoice issued | 8 February 2026 |
| **Supply date (tax point)** | When goods/services provided | 5 February 2026 |
| **Seller name & address** | Full legal trading name + registered address | Super Veloce Ltd, 123 High Street... |
| **Seller VAT number** | GB followed by 9 digits | GB 123 456 789 |
| **Buyer name & address** | Customer's full details | Alberto Duran, Carrer de... |
| **Buyer VAT number** | Required for B2B if VAT registered | ES B12345678 (if applicable) |
| **Description of goods/services** | Clear itemized list | "Racing driver coaching - Barcelona" |
| **Quantity & unit price** | Per line item | 1 x £2,500.00 |
| **VAT rate per item** | Must show rate applied | 20% |
| **Total excluding VAT** | Subtotal before tax | £2,950.00 |
| **VAT amount** | Broken down by rate | VAT @ 20%: £590.00 |
| **Total including VAT** | Grand total | £3,540.00 |
| **Currency** | Must be clear (GBP for UK) | £ symbol or "GBP" |

### Simplified VAT Invoices (under £250)

For invoices under £250, you can use a simplified invoice with:
- Seller name, address, VAT number
- Invoice date
- Description of goods/services
- Total amount including VAT
- VAT rate applied

### Additional Best Practices

**Payment Information:**
- Bank name, sort code, account number
- Payment terms (e.g., "Due within 30 days")
- Accepted payment methods

**Company Registration:**
- Include Companies House registration number
- Required if trading as a limited company

**Pro Forma Invoices:**
- Clearly mark as "PRO FORMA" — not a VAT invoice
- Cannot be used to reclaim VAT
- Use for quotes/advance payment requests

### Common Mistakes to Avoid

❌ Missing supply date (different from invoice date)
❌ VAT number without "GB" prefix
❌ No breakdown of VAT by rate
❌ Missing sequential invoice numbers
❌ Vague descriptions ("Services rendered")
❌ No company registration number

### Invoice Number Format

Recommended format: `INV-YYYY-NNNN`
- YYYY = Year
- NNNN = Sequential number (zero-padded)
- Example: INV-2026-0001, INV-2026-0002...

Must be:
- Unique within your business
- Sequential (no gaps)
- Easy to reference

### Tax Point (Supply Date) Rules

The tax point determines which VAT period the transaction falls into:

| Scenario | Tax Point |
|----------|-----------|
| Goods collected | Date of collection |
| Goods delivered | Date of delivery |
| Services completed | Date service completed |
| Invoice issued before delivery | Invoice date |
| Payment received before delivery | Date of payment |

**14-day rule:** If you issue an invoice within 14 days of the basic tax point, the invoice date becomes the tax point.

### Credit Notes

When issuing a credit note (refund/correction):
- Reference original invoice number
- Use same format as invoices
- Negative amounts or clearly marked "Credit"
- Must include all mandatory VAT invoice elements

---

### VAT Schemes

**Flat Rate Scheme:**
- Pay fixed % of turnover (varies by trade)
- Simpler record-keeping
- Can't reclaim VAT on purchases (except capital assets > £2,000)

**Cash Accounting:**
- Pay VAT when paid, not when invoiced
- Good for cash flow

**Annual Accounting:**
- One return per year
- Monthly/quarterly payments on account

### Returns & Payment
- **Quarterly** returns (unless annual scheme)
- Payment due **1 month + 7 days** after period end
- Making Tax Digital: Submit via compatible software

---

## PAYE & National Insurance

### Income Tax Bands 2025/26

| Band | Taxable Income | Rate |
|------|----------------|------|
| Personal Allowance | £0 - £12,570 | 0% |
| Basic Rate | £12,571 - £50,270 | 20% |
| Higher Rate | £50,271 - £125,140 | 40% |
| Additional Rate | Over £125,140 | 45% |

### National Insurance 2025/26

**Employee (Class 1):**
| Earnings | Rate |
|----------|------|
| Up to £12,584/year (£242/week) | 0% |
| £12,584 - £50,270 | 8% |
| Over £50,270 | 2% |

**Employer (Secondary):** ⚠️ **MAJOR CHANGE FOR 2025/26**
| Earnings | Rate |
|----------|------|
| Over **£5,004/year** (£96/week) | **15%** |

*Employer threshold dropped from £9,100 to £5,004, rate increased from 13.8% to 15%*

### Optimal Director Salary Strategy (2025/26)
The employer NI changes affect optimal salary calculations. Common approaches:

**Option 1:** Salary at **Lower Earnings Limit** (~£6,500)
- Preserves state pension credits
- Minimal employer NI exposure

**Option 2:** Salary at **employee threshold** (~£12,584)
- No employee NI
- Some employer NI (on ~£7,580 above employer threshold)

**Option 3:** Salary at **personal allowance** (£12,570)
- Uses full tax-free allowance
- Employer pays 15% NI on excess over £5,004

*Higher employer NI makes dividends relatively more attractive. Consult accountant for optimal split.*

---

## Dividends

### Rates 2025/26

| Band | Rate |
|------|------|
| Basic Rate (within £50,270) | 8.75% |
| Higher Rate (£50,271-£125,140) | 33.75% |
| Additional Rate (over £125,140) | 39.35% |

### Dividend Allowance
- **£500** tax-free dividend income per year
- Was £1,000 in 2023/24, £2,000 in 2022/23

### Process
1. Check company has sufficient **distributable reserves** (retained profits)
2. Board meeting/resolution to declare dividend
3. Issue dividend voucher
4. Director reports on Self Assessment

---

## P11D: Benefits in Kind

### What to Report
- Company cars
- Private medical insurance
- Interest-free loans > £10,000
- Living accommodation
- Assets provided for private use

### Deadlines
- **P11D submission:** 6 July after tax year end
- **P11D(b) Class 1A NI payment:** 22 July (19 July if posting cheque)

### Class 1A NI
Employer pays **15%** on value of benefits (increased from 13.8% in 2025/26)

---

## R&D Tax Credits

### What Qualifies
Projects seeking advance in **science or technology** by resolving **technological uncertainty**.

**Must demonstrate:**
- Competent professional couldn't solve it easily
- Advance benefits the field, not just your company
- Uncertainty about if/how it could be done

**Qualifies:**
- Creating new processes, materials, devices, products, services
- Appreciably improving existing ones
- Using science/tech to replicate something in a new way

**Does NOT qualify:**
- Arts, humanities, social sciences
- Routine improvements
- Cosmetic/style changes

### Qualifying Costs

| Cost Type | Notes |
|-----------|-------|
| Staff costs | Salaries, NI, pension for R&D staff |
| Consumables | Materials used up in R&D |
| Software | Licence fees for R&D software |
| Subcontractors | 65% of unconnected contractor costs |
| Data/Cloud | From April 2023 |
| EPWs | Externally provided workers (65%) |

**Cannot claim:**
- Capital expenditure
- Land costs
- Patents/trademarks
- Rent, rates, leasing

### Rates (Accounting periods from 1 April 2024)

**Merged Scheme (replaced SME/RDEC):**
- **Above the line credit:** 20% of qualifying R&D spend
- Creates a taxable credit

**Enhanced R&D Intensive Support:**
- For R&D intensive SMEs (R&D spend > 30% of total spend)
- Additional relief available

### Speed Capital R&D Potential

**Strong Candidates:**

| Project | Why It May Qualify | Evidence Needed |
|---------|-------------------|-----------------|
| AI coaching algorithms | Technological uncertainty in replicating human coaching judgment | Technical specs, failed approaches, competent professional statement |
| Novel telemetry analysis | If creating new methods beyond standard data viz | Documentation of what's "new to the field" |
| Simulation physics improvements | If contributing to engine development, not just using it | Collaboration docs with sim platform |
| Adaptive difficulty/training systems | ML approaches with genuine uncertainty | Training data experiments, iteration logs |

**Weak/Unlikely Candidates:**
- Using existing tools in standard ways
- UI/UX improvements (unless technically novel)
- Content creation (tracks, cars) — usually not "technological"
- Routine software development with known solutions

**Documentation Requirements:**
1. **Contemporaneous records** — timesheets showing R&D hours vs BAU
2. **Technical narrative** — what uncertainty existed, how you tried to resolve it
3. **Competent professional** — someone who can explain why this wasn't obvious
4. **Cost allocation** — which staff, what % of their time, consumables used

**Claim Process (from April 2024):**
1. Submit **Additional Information Form** before or with CT600
2. Describe projects in technical terms
3. HMRC may enquire — be ready to defend
4. Use specialist R&D advisor for first claim (they take ~15-25% of benefit but handle compliance)

**Rates (Merged Scheme 2024+):**
- 20% above-the-line credit on qualifying spend
- If R&D intensive (>30% of costs), enhanced support available
- Credit is taxable but can offset CT liability

---

## Key HMRC Deadlines

| Task | Deadline |
|------|----------|
| Corporation Tax payment | 9 months + 1 day after year end |
| CT600 filing | 12 months after year end |
| Annual accounts (Companies House) | 9 months after year end |
| Confirmation Statement | Annual (date varies) |
| P11D submission | 6 July |
| P11D(b) NI payment | 22 July (19 if cheque) |
| Self Assessment filing | 31 January |
| Self Assessment payment | 31 January |
| VAT return | 1 month + 7 days after quarter |

---

## Drawing vs Company Expense — Quick Reference

| Expense Type | Treatment | Tax Impact |
|--------------|-----------|------------|
| Software subscriptions | Company expense | Deductible |
| SIM cards for work | Company expense | Deductible |
| Business travel | Company expense | Deductible |
| Personal meals | Drawing (DLA) | Not deductible |
| Client entertainment | Disallowable | Not deductible |
| Personal clothing | Drawing (DLA) | Not deductible |
| Uniform/branded gear | Company expense | Deductible |
| Training (business) | Company expense | Deductible |
| Gym membership | Drawing (unless contractual) | Usually not deductible |
| Home office | Apportioned | Partially deductible |

---

## Racing-Specific Tax Guidance (Super Veloce Ltd)

### Clearly Deductible for Racing Driver
- **Race entry fees** — direct business cost
- **Travel to races** — flights, hotels, transfers (wholly business)
- **Race suit, helmet, HANS device** — required safety equipment
- **Team branded clothing** — promotional/uniform
- **Simulator time** — training directly related to racing
- **Coaching fees** — driver coaching, data engineering
- **Race car parts/consumables** — if company owns/operates car
- **Medical certificates** — required for race licence
- **Race licence fees** — mandatory for business
- **Data subscriptions** — timing, telemetry services

### Gray Areas — Document Carefully
- **Gym membership** — deductible IF contractually required by team OR demonstrably necessary for role (document this)
- **Nutrition/supplements** — likely personal unless team contract requires specific regime
- **Physio/sports massage** — deductible if injury is work-related or preventative for racing specifically
- **Flights for "testing"** — ensure documented as business, not leisure + driving

### Likely Personal (Drawings)
- **Everyday clothing** — even if worn to paddock
- **Personal meals** — unless genuinely entertaining clients/sponsors
- **Family travel to races** — personal
- **Non-racing holidays** — even if combined with a track day

### Asset vs Expense
- **Sim rig at home** — capital allowance (100% AIA), not expense
- **Helmet** — could be either (if < £1k, pragmatically expense it)
- **Race car** — definitely capital, complex treatment if company-owned

---

## Sim Racing Business (Speed Capital Ltd)

### Clearly Deductible
- **SimGrid infrastructure** — servers, software, subscriptions
- **Coach Dave Delta development** — staff, contractors, software
- **Sim hardware for testing** — business asset
- **Content creator payments** — if contracted
- **Tournament prize pools** — if SimGrid operates tournaments

### R&D Candidates (Document Carefully)
- **Novel simulation algorithms** — if technologically uncertain
- **New training methodologies** — if measurably advancing the field
- **AI/ML coaching tools** — strong candidate if genuine uncertainty
- **Telemetry analysis innovation** — possible if creating new methods

Keep timesheets. Separate R&D staff time from BAU operations.

---

## Common Director Pitfalls

### 1. Mixing Personal and Business
❌ Using business card for personal shopping, then "forgetting" to log it
✅ Separate cards, immediate logging, clear DLA tracking

### 2. Entertainment Confusion
❌ "I discussed business at dinner" — still disallowable
✅ Staff entertainment (Christmas party etc) IS allowable (within limits)
Note: Client entertaining is NEVER deductible, even if genuine business purpose

### 3. Overdrawn DLA Ignored
❌ Building up drawings without tracking
✅ Monthly DLA reconciliation, plan repayment before year-end

### 4. Mileage vs Actual Costs
❌ Claiming both mileage AND fuel
✅ Choose one method per vehicle, stick to it

### 5. Home Office Double-Dip
❌ Claiming simplified expenses (self-employed method) through Ltd
✅ Company pays director rent, director declares income, offsets actual costs

### 6. "It's for Work" Justification
❌ "I need nice clothes for meetings" — still personal
✅ Branded uniform with company logo — deductible
The test: Would you wear it outside work? If yes, it's personal.

---

## Practical Remuneration Example (2025/26)

### Scenario: £80,000 Available in Company

**Option A: All Dividends**
- £0 salary
- £80,000 dividend
- No employer NI, no employee NI
- Dividend tax: ~£10,500 (after allowances)
- ⚠️ No state pension credits

**Option B: Salary to LEL + Dividends**
- £6,500 salary (Lower Earnings Limit)
- £73,500 dividend
- Employer NI: £224 (15% on £1,496)
- Corp tax saving: ~£1,625 (salary deductible)
- Net better, preserves pension credit

**Option C: Salary to PT + Dividends**
- £12,570 salary (Personal Allowance)
- £67,430 dividend
- Employer NI: £1,135 (15% on £7,566)
- Corp tax saving: ~£3,140
- Uses full personal allowance

*Actual optimal depends on: other income, pension contributions, future plans. Model it or ask accountant.*

---

## When to Get Professional Help

**DIY is fine for:**
- Routine expense categorization
- Basic bookkeeping
- VAT returns (if straightforward)
- Monthly management accounts

**Get an accountant for:**
- Year-end accounts & CT600
- R&D tax credit claims (specialist recommended)
- Remuneration planning
- Company restructuring
- Cross-border income (EU races, international SimGrid revenue)
- Anything involving HMRC investigation

---

## Still To Clarify (David)

- [ ] VAT registration status for each company
- [ ] Specific remuneration strategy for 2025/26
- [ ] Home office arrangement (% claimable)
- [ ] Company car vs mileage allowance
- [ ] Racing gear — company asset or personal?
- [ ] Client entertainment policy
- [ ] R&D tracking for Speed Capital

---

## Sources

- [Corporation Tax rates](https://www.gov.uk/corporation-tax-rates)
- [Director's loans](https://www.gov.uk/directors-loans)
- [Capital allowances](https://www.gov.uk/capital-allowances)
- [R&D tax relief](https://www.gov.uk/guidance/corporation-tax-research-and-development-rd-relief)
- [R&D qualifying costs](https://www.gov.uk/guidance/check-what-research-and-development-rd-costs-you-can-claim)
- [Mileage rates](https://www.gov.uk/expenses-and-benefits-business-travel-mileage/rules-for-tax)
- [VAT registration](https://www.gov.uk/how-vat-works)
- [Expenses](https://www.gov.uk/expenses-if-youre-self-employed)

---

*When in doubt: flag for David, don't guess. Wrong categorization = tax issues.*
