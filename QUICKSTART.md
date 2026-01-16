# 🚀 Quick Start Guide

Get up and running with the Snowflake Data Catalog Documentation Generator in 5 minutes.

## Prerequisites

- Python 3.8 or higher
- Snowflake account with SSO enabled
- (Optional) OpenAI or Anthropic API key

## Installation

### 1. Install Dependencies

```bash
# Create virtual environment (optional but recommended)
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install required packages
pip install -r requirements.txt
```

### 2. Configure API Keys (Optional)

If you want to use external LLM providers:

```bash
# For OpenAI
export OPENAI_API_KEY="sk-..."

# For Anthropic
export ANTHROPIC_API_KEY="sk-ant-..."
```

Or use Snowflake Cortex (no API key needed).

### 3. Run the App

```bash
streamlit run app.py
```

The app will open in your browser at `http://localhost:8501`

## First-Time Walkthrough

### Step 1: Connect (2 minutes)

1. Enter your Snowflake account:
   - Use account identifier: `xy12345.us-east-1`
   - Or full URL: `https://xy12345.snowflakecomputing.com`

2. Enter your email/username

3. Click **"Connect (SSO)"**

4. Authenticate in the browser window that opens

5. You'll see "✅ Connected!" in the sidebar

### Step 2: Select View (1 minute)

1. Choose **Database** from dropdown
2. Choose **Schema** from dropdown
3. Choose **View** from dropdown
4. (Optional) Add view description for better LLM results
5. Click **"Load Columns"**

### Step 3: Sample Data (1 minute)

1. Adjust sample size if needed (default 2000 rows works well)
2. Click **"Sample Data"**
3. Wait for sampling to complete

**Note**: For views with 200-1200 columns, this typically takes 10-60 seconds.

### Step 4: Configure & Generate (1 minute)

1. Expand **"LLM Settings"**

2. Quick configuration:
   - If you have Cortex: Check "Use Snowflake Cortex"
   - Otherwise: Select provider (OpenAI/Anthropic) and model
   - Keep defaults for other settings

3. Click **"Generate Descriptions"**

4. Watch the progress bar - this takes 2-30 minutes depending on column count

### Step 5: Export Results (30 seconds)

1. Review the results table
2. Click **"Download CSV"** or **"Download Excel"**
3. Done! 🎉

## Common First-Time Issues

### "Account not found"
- Use the exact account identifier from your Snowflake URL
- Format: `account.region.cloud` (no https://)

### "API key not found"
- Use Snowflake Cortex instead (no API key needed)
- Or set environment variable: `export OPENAI_API_KEY="sk-..."`

### Sampling is slow
- Reduce sample size to 1000 rows
- Or check "Skip sampling" for fastest results

### LLM errors
- Try a different model (gpt-3.5-turbo is most reliable)
- Reduce batch size to 40-50 if you hit token limits

## Example Session

Here's a complete example for a 500-column view:

```bash
# Terminal 1: Set API key (if using OpenAI)
export OPENAI_API_KEY="sk-..."
streamlit run app.py

# Browser:
# 1. Connect to Snowflake
#    Account: xy12345.us-east-1
#    Email: user@company.com
#    [Click Connect (SSO)]

# 2. Select View
#    Database: ANALYTICS
#    Schema: PUBLIC
#    View: CUSTOMER_360
#    Description: "Customer 360 view with all customer attributes"
#    [Click Load Columns]
#    Result: ✅ Loaded 523 columns

# 3. Sample Data
#    Sample size: 2000 rows
#    [Click Sample Data]
#    Result: ✅ Sampled 2000 rows, collected samples for 523 columns

# 4. Generate
#    Provider: OpenAI
#    Model: gpt-3.5-turbo
#    Batch size: 60
#    [Click Generate Descriptions]
#    Result: ✅ Generation complete! (9 batches, 12 minutes)

# 5. Export
#    [Click Download Excel]
#    Result: customer_360_documentation.xlsx downloaded
```

## Next Steps

- Read the full [README.md](README.md) for detailed documentation
- Explore advanced features like column filtering and custom batch sizes
- Set up `.streamlit/secrets.toml` for persistent API keys
- Share your documentation with your team!

## Performance Tips

### For Small Views (< 100 columns)
- Sample size: 2000 rows
- Batch size: 100
- Expected time: 2-5 minutes

### For Medium Views (100-500 columns)
- Sample size: 1500 rows
- Batch size: 60-80
- Expected time: 5-15 minutes

### For Large Views (500-1200 columns)
- Sample size: 1000 rows
- Batch size: 40-60
- Expected time: 15-45 minutes

## Getting Help

If you run into issues:

1. Check the **Troubleshooting** section in [README.md](README.md)
2. Review error messages - they usually explain what went wrong
3. Try with a smaller view first (< 50 columns) to test setup
4. Open an issue on GitHub with details about your problem

---

**Happy documenting! 📊✨**
