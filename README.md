# ce_unit

ce_unit is a small R class library providing Cost‑Effective (CE) specific types for HTA (health technology assessment) workflows. It exposes lightweight, user-defined types that make it easy to represent, manipulate, and serialize cost and outcome data such as QALYs.

This README shows:
- Installation
- How to use the package's Cost type

## Installation

Install from GitHub (example):

```r
# install.packages("remotes") # if needed
remotes::install_github("validate-hta/ce_unit")
```

Or clone and install locally:

```sh
git clone https://github.com/validate-hta/ce_unit.git
R CMD INSTALL ce_unit
```

## How to use the Cost type

The package provides an R6-style `Cost` object (or equivalent constructor) to capture monetary values with metadata (value, year, currency, base currency) and helper methods to:
- inspect value and currency (`get_value()`, `get_currency()`)
- convert currency for a given year (`convert_currency(to_currency)`)
- inflate values between years (`inflate_value(to_year)` / `inflate_next_year()`)
- convert to/from a base currency (`currency_to_base_country()` / `change_year()`)

Below are usage examples showing the public API (no internal class source).

### Example: create, inspect, convert, and inflate

```r
library(ce_unit)
library(jsonlite)

# Construct a Cost object
# (replace Cost$new(...) with the actual constructor if the package uses a different name)
mycost <- Cost$new(value = 10000, year = 2005, currency = "GBP", base_currency = "GBP")

# Inspect
mycost$get_value()    # numeric value in current currency/year
mycost$get_currency() # e.g. "GBP"

# Convert to another currency for the same year
mycost$convert_currency("USD")
mycost$get_value()    # value updated (converted)
mycost$get_currency() # "USD"

# Inflate to a target year (keeps currency unless you convert)
mycost$inflate_value(2020)
current_value <- mycost$get_value()  # value in USD in year 2020 (after inflation)
```

## Design notes & best practices

- Store cost as a structured object: `{ value: numeric, currency: "USD" | "GBP", year: integer }` so downstream code can safely convert/inflate.
- Represent QALYs as numeric outcomes with unit label `"QALY"`.
- The package leaves conversion/inflation data sources to the environment — supply reliable `conversion_df` and `inflation_df` or use the package helper/data you prefer.

- Add a short vignette demonstrating common HTA workflows using `ce_unit`.

Tell me whether to commit this README and/or add the runnable script, and which branch name to use (or I can create one like `docs/readme-qaly`).
