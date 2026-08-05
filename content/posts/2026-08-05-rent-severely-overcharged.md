---
title: "Your Landlord is Overcharging You for Utilities. Here is the Python Script to Prove It"
date: 2026-08-05T20:00:31+08:00
draft: false
tags: ["finance", "smart-saving", "renters-rights", "python"]
summary: "How to audit your landlord's overpriced utility bill using open-source tools and stop paying for your neighbor's AC."
---

I was lurking in r/personalfinance yesterday and saw a post from a renter getting fleeced for $250 a month in "shared water andTrash." The landlord just handed them a blurry Excel sheet. 

This is illegal in most states. If a lease says utilities are "billed proportionally," you have a legal right to see the master meter and the exact square footage formula. Spoiler alert: they almost never calculate it right. 

We are going to stop taking their word for it. We are going to reverse-engineer their spreadsheet.

## Extracting the Data

You need the raw numbers. Ask for the master utility bills. If they say no, send a certified letter citing your local tenant rights act. Usually, that's enough to make them sweat.

Once you have the PDFs, skip the manual data entry. A top comment in that thread suggested manually typing the last 12 months into a spreadsheet. That is insane. 

Use `tabula-py`. It's a Python wrapper for Tabula, the Java PDF table extractor.

```bash
pip install tabula-py
```

If you get a Java error, you probably don't have a JRE installed. On Ubuntu, just run `sudo apt install default-jre`. On Windows, download the latest OpenJDK 21. I haven't tested this on ARM Macs yet, but the community says it runs flawlessly under Rosetta.

## Crushing the Numbers

Most landlords bill by total occupancy or by square footage. Let's assume they claim to bill by square footage. 

Here is a quick script to parse the extracted CSVs and calculate what you actually owe, assuming a standard RUBS (Ratio Utility Billing System) formula.

```python
import pandas as pd

# Load the master bill and the landlord's calculated charges
master_bill = pd.read_csv('master_utility.csv')
your_calculated_bill = pd.read_csv('your_charges.csv')

# Square footage variables
complex_total_sqft = 25000
your_apt_sqft = 850

# Calculate the legally defensible split
your_percentage = your_apt_sqft / complex_total_sqft
actual_owed = master_bill['total_cost'] * your_percentage

# Compare against what they demanded
results = pd.concat([your_calculated_bill['amount'], actual_owed], axis=1)
results['overcharge'] = results['amount'] - actual_owed
print(results)
```

Run this and look at the `overcharge` column. 

If that number is consistently above $50 a month, you are actively subsidizing someone else's water heater. In the original thread, the poster ran the math and realized the landlord was undercounting the complex's total square footage by leaving out a vacant unit. They were paying about 15% more than they should have.

## Deploying the Receipts

You don't need a lawyer for step one. You just need a paper trail.

1. Export the output of that script to a clean PDF.
2. Write a calm, professional email to the property manager.
3. Include the math. "I noticed the square footage calculation in your August billing doesn't match county tax records. My corrected portion is $145, not $250. Attached is the breakdown."
4. Pay the lower amount and deduct the difference. Clearly state in the email why you are doing this.

If they try to hit you with a late fee, point out that they accepted partial payment. Most states require landlords to provide a proper accounting before they can demand a specific sum. A blurry screenshot doesn't count as proper accounting. 

Yes, fighting your landlord on $100 a month is exhausting. But $100 invested monthly into an S&P 500 index fund at 8% annualized is $18,000 in 10 years. Protect that capital.

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [{
    "@type": "Question",
    "name": "What is a Ratio Utility Billing System (RUBS)?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "A RUBS formula divides the cost of a master utility bill among tenants based on square footage or occupancy. Landlords use it instead of installing individual sub-meters for each unit."
    }
  },{
    "@type": "Question",
    "name": "Can a landlord charge a flat fee for utilities without showing the bill?",
    "acceptedAnswer": {
      "@type": "Answer",
      "text": "In most jurisdictions, no. If a lease states utilities are allocated proportionally, the tenant has the right to an itemized accounting and the master utility bill to verify the math."
    }
  }]
}
</script>