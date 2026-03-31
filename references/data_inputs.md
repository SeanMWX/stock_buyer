# Data Inputs

Use this file when mapping the strategy to the local SQLite database or when deciding which strategy-state fields still need to be stored locally.

## Bundled Data Importer

The skill bundles:

`python {baseDir}/scripts/import_eastmoney_pingzhongdata.py <fund_code>`

Default database path:

`<repo-root>/data/funds.sqlite`

The repo root keeps a compatibility wrapper at:

`python scripts/import_eastmoney_pingzhongdata.py <fund_code>`

## Tables Produced By The Importer

- `funds`
  Latest basic metadata for each fund.
- `fund_snapshots`
  Raw JS snapshots plus parsed JSON per source timestamp.
- `fund_return_metrics`
  Summary returns such as one-month, three-month, six-month, and one-year.
- `fund_asset_codes`
  Current stock and bond code lists exposed by the payload.
- `fund_timeseries`
  Net worth, accumulated worth, estimated stock position, peer ranking, and related time series.
- `fund_report_metrics`
  Quarterly or categorical report-style metrics such as asset allocation and holder structure.
- `fund_managers`
  Current manager snapshot.
- `fund_manager_power_metrics`
  Manager score dimensions.
- `fund_similar_funds`
  Same-type fund lists exposed by the payload.

## Strategy Inputs Covered Today

- `current_price`
  Derive from the latest `fund_timeseries` row where `series_type = 'net_worth_trend'`.
- `recent_high`
  Derive from `net_worth_trend` over the configured 20-trading-day lookback window.
- `fund_code`
  Read from `funds` or `fund_snapshots`.
- `effective_fee_rate_pct`
  Read from `funds.current_rate`.

## Strategy Inputs Missing From The Current Importer

- `cash_pool`
  This is strategy state, not source-market data.
- `cost_price`
  This comes from executed trades or position accounting.
- `position_value`
  This depends on position size and current price.
- `last_add_trade_date`
  This comes from a strategy trade log.
- `is_weekly_dca_day`
  This comes from the strategy schedule, not the market payload.
- `is_monthly_cash_inflow_day`
  This comes from the cash schedule, not the market payload.

## Strategy Tables Available Now

- `strategy_accounts`
  Persist `cash_pool`, current position size, average cost price, and fund type per account/fund pair.
- `strategy_trades`
  Persist cash inflows, manual trades, and strategy-generated buys or sells.
- `strategy_reminders`
  Persist Monday pretrade reminders, Tuesday trade reminders, and monthly cash-inflow reminders.

## Important Constraint

Do not infer strategy state from market data alone.
If `cash_pool`, `cost_price`, or prior trade dates are unavailable, return `skip_data_missing` instead of inventing them.

## Business-Day Approximation

The current implementation treats business days as Monday through Friday.
It does not yet model Chinese public-holiday closures, so the "second-to-last business day" reminder is an approximation unless a holiday-aware calendar is added later.
