# AI-Powered Stock Market Intelligence Dashboard

![Dashboard Preview](file:///C:/Users/nicke/Downloads/ai_portfolio_dashboards_stock_intelligence_global_ev_analytics%20(2).html)

## Overview & Problem Statement
Retail and institutional analysts often track technical stock indicators separately from qualitative financial news sentiment. This project bridges that gap by combining **6-month historical OHLCV market data** (`AAPL`, `MSFT`, `NVDA`, `TSLA`, `GOOGL`) with **automated LLM financial news sentiment analysis** (`GPT-4o-mini`) and **Power BI DAX modeling** into a unified executive intelligence dashboard.

- **Author GitHub:** [github.com/nickelsun44-crypto](https://github.com/nickelsun44-crypto)
- **Tech Stack:** Python (`yfinance`, `pandas`, `openai`), SQL, Power BI (Power Query + DAX), OpenAI GPT-4o-mini API

## Architecture & Workflow
1. **Data Ingestion (`stock_ai_pipeline.py`):** Fetches historical OHLCV stock data using `yfinance` (with built-in offline generator fallback) and computes Daily Return %, 20-Day SMA (`SMA_20`), and 50-Day SMA (`SMA_50`).
2. **AI Sentiment Pipeline (`Python + OpenAI LLM`):** Passes ticker-level financial news headlines through a structured `gpt-4o-mini` prompt pipeline to output a numeric `Sentiment_Score` (`-1.0` to `+1.0`), a categorical `Sentiment_Label` (`Bullish`, `Bearish`, `Neutral`), and a 1-sentence `AI_Summary`.
3. **Storage (`CSV / SQL`):** Exports cleaned market tables (`stock_market_data.csv`) and AI sentiment outputs (`stock_ai_sentiment.csv`) ready for relational querying (`stock_analytics_queries.sql`) and Power BI ingestion.
4. **Transformation & Modeling (`Power Query + DAX`):** Links `stock_market_data` and `stock_ai_sentiment` on a central `Ticker` / `Date` dimension and powers interactive KPI cards via custom DAX measures (`stock_dax_measures.dax`).

## Repository Structure
| File | Description |
| :--- | :--- |
| `stock_ai_pipeline.py` | End-to-end Python ingestion, technical indicator calculation, and LLM sentiment script |
| `stock_market_data.csv` | Generated 6-month historical OHLCV dataset with `Daily_Return`, `SMA_20`, and `SMA_50` |
| `stock_ai_sentiment.csv` | Generated LLM sentiment scores, labels, and executive summaries per ticker |
| `stock_analytics_queries.sql` | SQL queries joining technical moving average crossovers with AI sentiment |
| `stock_dax_measures.dax` | Power BI DAX measures for KPI cards and dynamic AI executive insight display |
| `dashboard_preview.png` | High-resolution Power BI dashboard layout preview |
| `requirements.txt` | Python package dependencies |

## Quick Start
```bash
pip install -r requirements.txt
export OPENAI_API_KEY="your-api-key"
python stock_ai_pipeline.py
```

## Essential Power BI DAX Measures
```dax
Latest Close Price = 
VAR MaxDate = MAX('stock_market_data'[Date])
RETURN
CALCULATE(
    AVERAGE('stock_market_data'[Close]),
    'stock_market_data'[Date] = MaxDate
)

Avg Volume = AVERAGE('stock_market_data'[Volume])
52W High = MAX('stock_market_data'[High])
52W Low = MIN('stock_market_data'[Low])

AI Sentiment Status = 
SELECTEDVALUE('stock_ai_sentiment'[Sentiment_Label], "Neutral")

Executive AI Summary = 
SELECTEDVALUE('stock_ai_sentiment'[AI_Summary], "Select a single stock ticker to view AI-generated market insights.")
```
