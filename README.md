# Stock News Sentiment Automation using Gemini AI, Google Sheets & Gmail

This workflow automatically fetches daily stock market news, analyzes sentiment using Gemini AI, calculates impact scores, sends alerts for high-impact news and stores structured results in Google Sheets.

### Quick Implementation Steps

1. [Import the workflow into your n8n account](https://n8n.partnerlinks.io/om1efg2qgvwi)
2. Configure the **Market News API (HTTP Request node)**
3. Add your **Google Gemini API credentials**
4. Connect **Google Sheets** and create required columns
5. Configure **Gmail node** (add recipient email)
6. Activate the workflow

## What It Does

This workflow runs every day at **8 AM** and automatically collects the latest stock market news from a public API. It filters, cleans and processes the data to ensure only relevant and unique articles are analyzed.

Each news article is sent to **Google Gemini AI**, which classifies the sentiment as *bullish*, *bearish* or *neutral*, along with a confidence score and reasoning. The workflow then calculates an **impact score** based on sentiment and confidence.

High-impact news is immediately flagged and sent via email alerts, while all processed results are stored in a structured format in Google Sheets for tracking and analysis.

## Who It's For

- Stock traders and investors
- Financial analysts
- Algo trading developers
- FinTech startups
- Anyone tracking market sentiment automatically

## Requirements

- [**n8n account (Self-hosted or Cloud)**](https://n8n.partnerlinks.io/om1efg2qgvwi)
- Public Market News API (already used in workflow)
- Google Gemini API credentials
- Google Sheets account
- Gmail account for sending alerts

## How It Works & Setup Guide

### 1. Trigger Setup

- The workflow starts using **Schedule Trigger**
- Runs daily at **8 AM**

### 2. Fetch Market News

- HTTP Request node calls:

```text
https://api.tradient.org/v1/api/market/news
```

- No authentication required (public API)

### 3. Select & Limit News

- Code node extracts:

```text
data.latest_news
```

- Limits to **top 2 articles**

You can change this limit in:

```javascript
return output.slice(0, 2);
```

### 4. Normalize Data

- Ensures consistent fields:
  - `news_object.title`
  - `news_object.text`
  - `stock_name`

### 5. Remove Duplicate News

- Deduplicates using **title**
- Prevents repeated analysis

### 6. Process One-by-One

- Uses **SplitInBatches**
- Ensures controlled processing

### 7. Rate Limiting

- Wait node prevents API overuse
- Avoids hitting Gemini limits

### 8. Generate AI Prompt

- Creates structured prompt like:
  - Title
  - Content
  - Expected JSON output

### 9. AI Sentiment Analysis

- Uses **Gemini model:**

```text
models/gemini-3.1-flash-lite-preview
```

- Returns:
  - Sentiment
  - Confidence
  - Reason

  ### 10. Parse AI Response

- Removes markdown formatting
- Converts response into valid JSON
- Handles errors safely

### 11. Calculate Impact Score

Formula logic:

- Bullish → `confidence × 1.2`
- Bearish → `confidence × -1.2`
- Neutral → `confidence × 0.3`

### 12. High Impact Filter

- Conditions:
  - Impact > `60`
  - Impact < `-60`

### 13. Email Alert Setup

You MUST configure:

- **Recipient email in Gmail node (`sendTo`)**

Example message:

```text
Stock: XYZ
Title: News title
Sentiment: bullish
Impact: 75
Reason: Strong earnings report
```

### 14. Google Sheets Setup

Create a sheet with EXACT column names:

```text
Title
Stock
Sentiment
Reason
```

**Important:**

- Column names must match exactly (case-sensitive)
- Do NOT rename fields
- Sheet must be connected in the node

## How To Customize Nodes

### Change Number of News Articles

Modify:

```javascript
return output.slice(0, 2);
```

### Adjust Impact Threshold

In **IF node**:

Change:

- `> 60`
- `< -60`

### Modify AI Prompt

Edit **Generate Sentiment Prompt** node to:

- Add more context
- Include additional fields

### Add More Sheet Columns

Update Google Sheets node mapping:

Add new fields like:

- `confidence`
- `impact_score`

## Add-ons (Enhancements)

- Slack/Telegram alerts instead of Gmail
- Dashboard (Power BI / Looker Studio)
- Store historical trends in database
- Add stock price correlation
- Multi-language news analysis
- Increase batch size with queue system

## Use Case Examples

### 1. Daily Trading Signals
Receive instant notifications for high-impact bullish and bearish news that may influence trading decisions.

### 2. Portfolio Monitoring
Continuously monitor sentiment surrounding stocks in your investment portfolio and stay informed about important developments.

### 3. Market Intelligence Platform
Create an automated research system that continuously analyzes financial news and stores structured insights.

### 4. Algorithmic Trading Support
Use AI-generated sentiment scores as one of the inputs for automated trading strategies.

### 5. Financial News Classification
Automatically organize, categorize and prioritize market news based on sentiment and impact scores.

This workflow can be customized further for numerous financial research and investment automation scenarios.

## Troubleshooting Guide

| Issue | Possible Cause | Solution |
|--------|----------------|----------|
| No data written to Google Sheets | Incorrect column names or mapping | Ensure the sheet contains the required columns exactly as configured. |
| Email alerts are not sent | Gmail credentials are missing or invalid | Reconnect your Gmail OAuth credentials and verify the recipient email. |
| AI response cannot be parsed | Gemini returned invalid JSON | Review the prompt format and parsing node configuration. |
| Duplicate news is still processed | Deduplication logic doesn't match article titles | Improve the duplicate matching logic or include article URLs. |
| Workflow never starts | Schedule Trigger is inactive | Activate the workflow and verify the configured schedule. |
| News API returns no articles | API service unavailable or endpoint issue | Test the endpoint manually and verify the API response. |

---

## Need Help?

Whether you're implementing this workflow for investment research, financial monitoring or enterprise automation, **WeblineIndia** can help you customize, optimize and scale it for production use.

Our experts can assist you with:

- Custom AI prompt engineering and workflow optimization
- Advanced n8n workflow development
- Google Sheets, Gmail and third-party API integrations
- Automated financial reporting and alert systems
- Scalable business process automation solutions

Learn more about our **n8n Automation Services**:
https://www.weblineindia.com/n8n-automation/

Explore our **Business Process Automation Solutions**:
https://www.weblineindia.com/process-automation-solutions.html

Or **contact WeblineIndia** to discuss your automation requirements:
https://www.weblineindia.com/contact-us.html
