# ⚡ Gas Storage Pricing Model

## 🧩 Overview
This project implements a **prototype pricing model** for evaluating a **natural gas storage contract**.  
It simulates the financial flows from **injecting (buying)** and **withdrawing (selling)** gas over time, accounting for storage limits, fees, and operational costs.

The model calculates the **net contract value (profit or loss)** under different market scenarios and storage strategies.

> 💬 This prototype is designed for testing and validation. In the future, it may serve as the basis for an automated quoting and pricing system.

---

## 🧠 Methodology

The pricing model is built around the following process:

1. **Injection Phase**  
   - Gas is purchased and stored when market prices are favorable.  
   - Injection costs are incurred per cubic foot injected.

2. **Storage Phase**  
   - Gas remains in the facility, incurring a fixed **monthly storage cost**.

3. **Withdrawal Phase**  
   - Stored gas is sold on later dates, generating cash inflows.  
   - Withdrawal costs are incurred per cubic foot withdrawn.

4. **Net Contract Value**  
   \[
   \text{Contract Value} = \text{Cash Inflows} - \text{Storage Costs} - \text{Purchase & Transaction Costs}
   \]

---

## 🧮 Function Description

### `price_contract()`

```python
def price_contract(
    in_dates, 
    in_prices, 
    out_dates, 
    out_prices, 
    rate, 
    storage_cost_rate, 
    total_vol, 
    injection_withdrawal_cost_rate
)
| Parameter                        | Type                    | Description                                                  |
| -------------------------------- | ----------------------- | ------------------------------------------------------------ |
| `in_dates`                       | list of `datetime.date` | Dates when gas is **injected** into storage                  |
| `in_prices`                      | list of `float`         | Market prices at injection dates                             |
| `out_dates`                      | list of `datetime.date` | Dates when gas is **withdrawn** (sold)                       |
| `out_prices`                     | list of `float`         | Market prices at withdrawal dates                            |
| `rate`                           | `float`                 | Quantity of gas injected or withdrawn per operation (cf/day) |
| `storage_cost_rate`              | `float`                 | Fixed monthly storage cost (USD)                             |
| `total_vol`                      | `float`                 | Maximum storage capacity (cf)                                |
| `injection_withdrawal_cost_rate` | `float`                 | Cost per cubic foot for injection/withdrawal (USD/cf)        |

How It Works

Date Sequencing

Merges and sorts all injection and withdrawal dates chronologically.

Injection Events

Gas is injected if capacity allows.

Adds purchase cost + injection cost to total expenses.

If full capacity is reached, injection is skipped with a warning.

Withdrawal Events

Gas is withdrawn if there’s sufficient volume.

Adds sales revenue – withdrawal cost to total cash inflow.

If there’s not enough gas, withdrawal is skipped with a warning.

Storage Cost Estimation

Calculates duration (in months) between first injection and last withdrawal.

Applies a fixed monthly fee over this period.

Net Value Calculation

Returns total profit or loss from the contract.

from datetime import date

# Example input data
in_dates = [date(2022, 1, 1), date(2022, 2, 1), date(2022, 2, 21), date(2022, 4, 1)]
in_prices = [20, 21, 20.5, 22]
out_dates = [date(2022, 1, 27), date(2022, 2, 15), date(2022, 3, 20), date(2022, 6, 1)]
out_prices = [23, 19, 21, 25]

rate = 100000                      # cf/day
storage_cost_rate = 10000          # monthly storage fee
injection_withdrawal_cost_rate = 0.0005
max_storage_volume = 500000        # cf

result = price_contract(
    in_dates, in_prices, out_dates, out_prices,
    rate, storage_cost_rate, max_storage_volume, injection_withdrawal_cost_rate
)

print(f"\nThe value of the contract is: ${result:.2f}")


Sample Output
Injected gas on 2022-01-01 at a price of 20
Extracted gas on 2022-01-27 at a price of 23
Injected gas on 2022-02-01 at a price of 21
Extracted gas on 2022-02-15 at a price of 19
Injected gas on 2022-02-21 at a price of 20.5
Extracted gas on 2022-03-20 at a price of 21
Injected gas on 2022-04-01 at a price of 22
Extracted gas on 2022-06-01 at a price of 25

The value of the contract is: $839998.50
