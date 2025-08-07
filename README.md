# n8n-Crypto-Agent
The n8n Crypto Agent is an intelligent automation workflow that connects with WhatsApp, enabling users to interact with multiple crypto-focused agents via simple chat commands or media. The system supports web scraping, live news summarization, market data queries, image analysis, and more all through natural chat interactions.

---

# Youtube video 
I’ve just created a YouTube video demo for the Crypto News Agent!
Check out the video here to see how the agent works in action:

👉 [Watch the video on YouTube](https://youtu.be/LSXQx85KvZ0)



## Table of Contents

1. Overview
2. Core Features
3. How It Works

   * Scraping Latest Crypto News
   * Summarizing and Saving News
   * Getting Summarized News via Chat
   * General Crypto Q\&A
   * Voice & Image Queries
4. Supported Triggers and Examples
5. Agent Workflow and Classification
6. Scheduled News Updates
7. Technologies and Models Used
8. API Integrations
9. Output Format and Structure

---

## Overview

The **n8n Crypto Agent** enables users to interact with real-time crypto data, news, and analytics via WhatsApp. Users can trigger multiple agent processes, including live news scraping, news summarization, Google Sheet storage, crypto info lookup, price conversion, trading chart analysis, and even voice/image query support.

<img width="1411" height="516" alt="Screenshot 2025-08-07 at 6 20 41 PM" src="https://github.com/user-attachments/assets/c3143f3f-d18c-4c49-ade4-e6d2f384130e" />
---

## Core Features

* 🔥 **Live Crypto News Scraping** (from top sources)
* 🤖 **Summarization via LLM** (JSON-structured for easy management)
* 🗂 **Google Sheets Storage** (for raw and summarized news)
* 📝 **Chat-driven Query Support** (news, general crypto Q\&A)
* 🖼️ **Image Analyzer** (trading screenshot analysis)
* 🎙️ **Voice Query Support** (via OpenAI transcription)
* 🔄 **Automated 6-Hour News Refresh**
* 🛠️ **Integrated CoinMarketCap Tools**

---

## How It Works

### 1. Scraping Latest Crypto News

* **Trigger:** Chat messages like:
  * “Scrape the latest crypto news”
  * “Fetch fresh news from CoinMarketCap”
  * “Update the news list”
* **Process:**

  * **CoinTelegraph:** Scrape [RSS feed](https://cointelegraph.com/rss), select 5 latest articles, extract full news data.
  * **CoinDesk:** Scrape [RSS feed](https://www.coindesk.com/arc/outboundfeeds/rss), select 5 news.
  * **CoinMarketCap:** Scrape [News Page](https://coinmarketcap.com/headlines/news), filter for top coins, select 5, extract full news.
<img width="733" height="636" alt="Screenshot 2025-08-07 at 6 19 16 PM" src="https://github.com/user-attachments/assets/5eeb14e6-9d3f-49d9-b5ef-9f97a47e20d7" />


### 2. Summarizing and Saving News

* Clean raw news data.
* Feed each news item to LLM for **summary in JSON format**.
* Save both **raw and summarized** news to Google Sheets for later retrieval.
  <img width="1127" height="506" alt="Screenshot 2025-08-07 at 6 20 25 PM" src="https://github.com/user-attachments/assets/85710176-0f39-41d9-8d22-757f9ff8dc73" />


### 3. Getting Summarized News via Chat

* **Trigger:** e.g.

  * “Tell me top 5 crypto news”
  * “Summarize today’s headlines”
  * “Any breaking Bitcoin news?”
* Calls **NewsExtractionAgent**: fetches & summarizes already-scraped news from Google Sheets, provides an easy-to-read answer via chat.
<img width="893" height="322" alt="Screenshot 2025-08-07 at 6 20 01 PM" src="https://github.com/user-attachments/assets/7f4ef535-3241-4ab9-ad1f-d1182aad8c88" />


### 4. General Crypto Q\&A

* **Trigger:** General crypto questions like:

  * “What is Bitcoin?”
  * “Explain Ethereum merge”
  * “Is crypto legal in Pakistan?”

* Calls **GeneralCryptoAgent**, which connects to CoinMarketCap API for:

  * **Crypto Info** (price, 24h change, rank)
  * **Crypto Listings** (market cap order)
  * **Live Price**
  * **Global Metrics**
  * **Price Conversion**
* Handles both general and specific queries.
  <img width="887" height="368" alt="Screenshot 2025-08-07 at 6 20 08 PM" src="https://github.com/user-attachments/assets/a8e64846-ebde-4c69-a7f6-7bfd1e5ed791" />

 
### 5. Voice & Image Queries

* **Voice:** If a user sends a voice message, it's transcribed using OpenAI's voice model and passed as text to the system.
* **Image:** If a trading screenshot is sent, **CryptoChartImageAnalyzerAgent** extracts market info (price, trend, key indicators) and gives trading suggestions in structured format.
  
<img width="1157" height="409" alt="Screenshot 2025-08-07 at 6 23 33 PM" src="https://github.com/user-attachments/assets/811bf98d-e37e-4ab7-b23f-76f2804afa2d" />

---

## Supported Triggers & Examples

| # | Intent                 | User Triggers (Examples)                                                                            | Agent                           |
| - | ---------------------- | --------------------------------------------------------------------------------------------------- | ------------------------------- |
| 1 | `scrape_news`          | “Scrape the latest crypto news”, “Fetch fresh news from CoinMarketCap”, “Update the news list”      | ScrapingAgent                   |
| 2 | `get_news`             | “Tell me top 5 crypto news”, “Summarize today’s headlines”, “What’s happening in the crypto world?” | NewsExtractionAgent             |
| 3 | `general_crypto_query` | “What is Bitcoin?”, “Explain Ethereum merge”, “How does blockchain work?”                           | GeneralCryptoAgent              |
| 4 | `analyze_chart_image`  | Sending a trading screenshot image                                                                  | CryptoChartImageAnalyzerAgent   |
| 5 | `voice_query`          | Sending a voice message                                                                             | OpenAI Transcription + Main LLM |

---

## Agent Workflow & Classification

All user queries are classified and routed using a **text classifier**, returning structured JSON output:

```json
{
  "agent": "AGENT_NAME",           # ScrapingAgent, NewsExtractionAgent, CryptoChartImageAnalyzerAgent, GeneralCryptoAgent
  "intent": "INTENT_NAME",         // scrape_news, get_news, analyze_chart_image, general_crypto_query
  "reason": "Why this agent was selected based on user input",
  "meta": {
    "content_type": "text | image",
    "input_text": "User’s original message (or 'image' if image)",
    "confidence": "High | Medium | Low"
  }
}
```

---

## Scheduled News Updates

* **Every 6 Hours:**

  * The system automatically triggers the news scraper.
  * Scrapes, cleans, and summarizes the latest news.
  * Stores both raw and summarized news in Google Sheets for instant chat-based access.

---

## Technologies & Models Used

* **n8n**: Workflow automation
* **WhatsApp**: User chat interface
* **OpenAI GPT-4.1 Mini**: Main LLM agent for news, chat, summarization (low-cost, high performance)
* **Claude 3.5 Sonnet**: Tool-calling, robust business/critical logic (best for accurate tool use)
* **CoinMarketCap API**: Crypto data, live prices, conversion, info
* **Google Sheets**: Persistent news storage
* **OpenAI Whisper**: Voice transcription
* **Image Analyzer (LLM/vision)**: Screenshot analysis

### Model Comparison

| Model                 | Input Cost (per 1M tokens)   | Output Cost (per 1M tokens)   | Context Window / Notes                                                                                                   |
| --------------------- | ---------------------------- | ----------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **GPT‑4.1 Mini**      | \$0.40                       | \$1.60                        | \~1 million token context window, good and also consider cheap                                                                                         |
| **Claude 3.5 Sonnet** | (Approx. \$3) | (Approx. \$15) | High-context, tool calling, best for  business-critical tool use                                                                                 |
| **GPT‑4o Mini**       | \$0.15                       | \$0.60                        | 128K token context; use for news summerization ([OpenAI][1]) |

[1]: https://openai.com/index/gpt-4o-mini-advancing-cost-efficient-intelligence/?utm_source=chatgpt.com "GPT-4o mini: advancing cost-efficient intelligence"

---

## API Integrations

* **CoinTelegraph** RSS
* **CoinDesk** RSS
* **CoinMarketCap** (News page scraping + full API tools)
* **Google Sheets API**
* **WhatsApp API**
* **OpenAI API** (GPT-4.1 Mini, Whisper)
* **Anthropic API** (Claude Sonnet 3.5)

---

## Output Format & Structure

All agent outputs and responses are structured for easy automation and integration, including:

* **JSON-formatted summaries**
* **Structured trading chart analysis**
* **Chat-friendly responses**

---

## Example: Market Snapshot (Image Analyzer)

```
📊 Market Snapshot
• Current Price: (If visible)
• 24h Change: (Percentage and value)
• 24h High / Low: (If visible)
• Chart Timeframe: (e.g., 1W, 1D)
• Trend Direction: (Uptrend / Downtrend / Sideways)
• Key Indicators: (e.g., MA(7), MA(25), if visible)

✅ Next Step Suggestion
• Hold / Sell / Take Partial Profit
• Buy now / Wait for dip / Wait for breakout
• Resistance / Support Levels
• Set Alerts at: (if any critical levels visible)
• Risks or missing info to consider?
```
Here is the Google sheet where news are saved 
https://docs.google.com/spreadsheets/d/1zcGG1gqBzYfbeGapVQptPahOMnnrpQKDwFONNPMREhs/edit?usp=sharing


