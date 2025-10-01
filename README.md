# eBay Data Pipeline

A production-ready data pipeline that extracts data from eBay's APIs and creates 8 structured tables for data warehouse and analytics purposes.

## 📊 Features

- **8 Comprehensive Tables**: Listings, Categories, Sellers, Transactions, Watch Count, Price History, Shipping Options, and Item Specifics
- **Real eBay API Data**: Uses official eBay Browse, Finding, and Taxonomy APIs
- **Automated Updates**: Daily and weekly update schedules
- **Rate Limit Handling**: Smart fallback mechanisms and error handling
- **Production Ready**: Environment-based configuration, logging, and data validation

## 🗂️ Database Schema

| Table | Update Frequency | Records | Description |
|-------|-----------------|---------|-------------|
| `listings` | Daily | ~1,000s | Active items for sale |
| `categories` | Quarterly | ~5,000 | Product category hierarchy |
| `sellers` | Weekly | ~500 | Seller profiles and stats |
| `transactions` | Daily | ~100s | Completed sales |
| `watch_count` | Daily | ~1,000s | Items being watched |
| `price_history` | Daily | ~1,000s | Price change tracking |
| `shipping_options` | Weekly | ~1,500 | Shipping methods & costs |
| `item_specifics` | Weekly | ~1,000 | Product attributes |

## 🚀 Quick Start

### Prerequisites

- Python 3.7+
- eBay Developer Account ([Sign up here](https://developer.ebay.com/))

### Installation

1. Clone the repository:
```bash
git clone https://github.com/yourusername/ebay-data-pipeline.git
cd ebay-data-pipeline
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. Set up environment variables:
```bash
# Copy the example env file
cp .env.example .env

# Edit .env and add your eBay credentials
nano .env
```

4. Run the pipeline:
```bash
python ebay_pipeline.py
```

## 🔑 Configuration

### Get eBay API Credentials

1. Go to [eBay Developer Program](https://developer.ebay.com/)
2. Sign in or create an account
3. Navigate to "My Account" → "Application Keys"
4. Create a new application (Production or Sandbox)
5. Copy your credentials:
   - **Client ID** (App ID)
   - **Client Secret** (Cert ID)
   - **App ID** (for Finding API)

### Environment Variables

Create a `.env` file in the project root:

```bash
EBAY_CLIENT_ID=your_client_id_here
EBAY_CLIENT_SECRET=your_client_secret_here
EBAY_APP_ID=your_app_id_here

```

**⚠️ IMPORTANT**: Never commit your `.env` file to Git! It's already listed in `.gitignore`.

## 📖 Usage

### Basic Usage

```python
from ebay_pipeline import run_ebay_pipeline
import os
from dotenv import load_dotenv

load_dotenv()

# Run pipeline
tables = run_ebay_pipeline(
    client_id=os.getenv("EBAY_CLIENT_ID"),
    client_secret=os.getenv("EBAY_CLIENT_SECRET"),
    app_id=os.getenv("EBAY_APP_ID"),
    search_query="laptop",
    limit=100
)

# Access individual tables
listings_df = tables["listings"]
transactions_df = tables["transactions"]
```

### Command Line

```bash
# Default search (laptop, limit=20)
python ebay_pipeline.py

# Custom search query
python ebay_pipeline.py "iphone" 50

# Multiple queries
python run_multiple_queries.py
```

### Multiple Product Categories

```python
queries = ["laptop", "iphone", "tablet", "camera"]

for query in queries:
    tables = run_ebay_pipeline(CLIENT_ID, CLIENT_SECRET, APP_ID, 
                               search_query=query, limit=100)
    
    # Save to CSV with query prefix
    for table_name, df in tables.items():
        df.to_csv(f"data/{query}_{table_name}.csv", index=False)
```

## 🔧 API Rate Limits

| API | Limit | Notes |
|-----|-------|-------|
| Browse API | 5,000 calls/day | Resets at midnight PST |
| Finding API | 5,000 calls/day | Resets at midnight PST |
| Taxonomy API | No strict limit | Very stable data |

**Current Usage**: ~6 API calls per pipeline run = **0.12%** of daily quota

You can safely run:
- Daily pipelines: ✅ 800+ times/day
- Multiple products: ✅ 800+ categories/day
- Real-time updates: ✅ Every few minutes if needed

## 📝 License

MIT License - see [LICENSE](LICENSE) file for details

## 🙏 Acknowledgments

- [eBay Developers Program](https://developer.ebay.com/)
- [eBay API Documentation](https://developer.ebay.com/docs)

## ⚠️ Disclaimer

This project is not affiliated with, endorsed by, or sponsored by eBay Inc. Use in accordance with eBay's API Terms of Use.
