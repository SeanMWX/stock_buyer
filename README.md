# stock_buyer

This repository root is now the runtime root of the `fund_buying_decision` skill.

## Runtime Structure

The actual skill runtime is the repo root:

- `SKILL.md`
- `scripts/`
- `references/`

Local-only content is kept outside the runtime contract:

- `local_test/`
- `data/`
- `skill_creator/`
- `README.md`
- `.gitignore`

## Current Capabilities

The skill currently supports:

- importing Eastmoney `pingzhongdata` JS payloads into local SQLite
- showing a detailed fund report for a fund code
- checking drawdown alerts such as "remind me when the fund is down 10% from its recent high"
- maintaining a strategy account state with `cash_pool`, `position_units`, and `avg_cost_price`
- recording manual cash flows and manual trades
- generating Monday / Tuesday / monthly reminders
- evaluating one explicit strategy action: `buy_dca`, `buy_dip`, `sell_take_profit`, `hold`, or `skip_data_missing`
- requiring explicit confirmation before a suggested strategy action is written into the trade log

Current strategy flow is human-in-the-loop:

- `evaluate_strategy.py` only generates reminders and suggestions
- `confirm_strategy_action.py` writes the suggested trade only after explicit confirmation
- `record_strategy_trade.py` records user-confirmed cash flows or manual buy/sell operations

## Main Commands

- Import fund data:

`python scripts/import_eastmoney_pingzhongdata.py 011598`

- Show a detailed fund report:

`python scripts/report_fund_details.py 011598 --refresh`

- Check whether drawdown alert tiers are triggered:

`python scripts/check_fund_alert.py 004475`

- Create or overwrite a stored strategy account:

`python scripts/manage_strategy_account.py upsert 011598 --account-id main --cash-pool 1000 --position-units 0 --fund-type equity_fund`

- Record a real cash flow or manual trade:

`python scripts/record_strategy_trade.py 011598 --account-id main --trade-type cash_inflow --gross-amount 1000`

- Generate reminders and a suggested action without modifying account state:

`python scripts/evaluate_strategy.py 011598 --account-id main --refresh`

- Confirm and execute the current suggested strategy action:

`python scripts/confirm_strategy_action.py 011598 --account-id main --refresh --expected-action buy_dca`

## Data Storage

Default database path:

`~/.fund_buying_decision/fund_buying_decision.db`

You can also keep separate datasets with:

`--db ~/.fund_buying_decision/<name>.db`

By default it writes to `~/.fund_buying_decision/fund_buying_decision.db` and stores:

- fund metadata
- raw JS snapshots
- return summary metrics
- asset code lists
- time series such as net worth and rank-in-peer data
- quarterly report metrics
- current manager data
- similar-fund lists
- strategy account state
- strategy trade history
- auto-generated reminders

## Local Testing

Local tests are intentionally outside the runtime under `local_test/fund_buying_decision/`.

Current test entrypoints:

- `python skill_creator/scripts/quick_validate.py .`
- `python local_test/fund_buying_decision/run_smoke.py`
- `python local_test/fund_buying_decision/run_cli_tests.py`
