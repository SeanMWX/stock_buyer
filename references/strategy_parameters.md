# Strategy Parameters

Use this file when the numeric block in `SKILL.md` needs clarification or refinement.

## Parameters Still Meant To Stay Editable

- `capital.weekly_dca_amount`
  Fixed weekly recurring buy size.
  Current configured value: `100`.

## Formula Notes

- `total_asset = cash_pool + position_value`
- `position_ratio = position_value / total_asset`
- `drawdown_pct = (recent_high - current_price) / recent_high * 100`
- `profit_pct = (current_price - cost_price) / cost_price * 100`

## Fixed Values Already Chosen

- `capital.initial_cash = 1000`
- `capital.weekly_dca_amount = 100`
- `capital.monthly_cash_pool_inflow = 1000`
- `capital.fee_rate_source = fund_current_rate`
- `scheduling.weekly_pretrade_reminder_weekday = monday`
- `scheduling.weekly_dca_weekday = tuesday`
- `scheduling.weekly_trade_reminder_weekday = tuesday`
- `scheduling.monthly_cash_inflow_rule = second_to_last_business_day`
- `scheduling.monthly_cash_inflow_reminder_rule = second_to_last_business_day`
- `price_state.recent_high_lookback_trading_days = 20`

## Interpretation Rules

- Treat `dip_thresholds_pct` and `dip_base_buy_amounts` as aligned arrays.
  Example: threshold `10` maps to base amount `150`.
- Treat `profit_thresholds_pct` and `profit_sell_ratios_pct` as aligned arrays.
  Example: profit `20` maps to sell ratio `20`.
- When multiple buy thresholds trigger on the same day, use only the deepest drawdown tier.
- When multiple sell thresholds trigger on the same day, use only the highest profit tier.
- When both a buy and a sell could trigger, prefer the sell because it reduces risk and preserves the one-trade-per-day rule.

## Practical Defaults Already Set

- Initial cash: `1000`
- Weekly baseline DCA: `100`
- Monthly new cash to pool: `1000`
- Dip thresholds: `5 / 10 / 15`
- Dip base buy amounts: `100 / 150 / 200`
- Profit thresholds: `10 / 20`
- Profit sell ratios: `10 / 20`
- Position floor / cap: `20 / 80`

## Ambiguities Left Explicit On Purpose

- `weekly_dca_amount` and `monthly_cash_pool_inflow` can stay asymmetric.
  That is valid if the user wants new money to accumulate before dip-buy opportunities appear.
- `fee_rate_source = fund_current_rate` means the strategy should read the fund's imported current subscription rate from the Eastmoney payload instead of a single global hard-coded fee.
- `second_to_last_business_day` is currently approximated with Monday-Friday weekdays only.
