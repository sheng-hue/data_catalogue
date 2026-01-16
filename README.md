# ❄️ Snowflake Data Catalog Documentation Generator

A production-ready Streamlit application for generating LLM-powered column documentation for Snowflake views. Designed to handle wide tables with 200-1200 columns efficiently through intelligent batching and one-pass sampling.

## 🚀 Features

### Core Capabilities
- **🔐 Secure SSO Authentication**: Connect to Snowflake using external browser authentication (no passwords stored)
- **📊 View Selection**: Intuitive database → schema → view hierarchy navigation
- **🎲 Efficient Sampling**: One-pass sampling algorithm optimized for wide tables (200-1200 columns)
- **🤖 LLM-Powered Documentation**: Generate column descriptions using Snowflake Cortex or external LLM APIs
- **📦 Intelligent Batching**: Process large column sets with configurable batch sizes and automatic retry logic
- **📥 Professional Export**: Export to CSV or formatted Excel with auto-filter, frozen headers, and text wrapping

### Performance Features
- **One-Pass Sampling**: Fetches all columns in a single query instead of per-column queries
- **Parallel Fallback**: If one-pass fails, falls back to parallel per-column sampling with worker pool
- **Smart Caching**: Connection, metadata, and sampling results cached for performance
- **Progress Tracking**: Real-time progress bars, batch counters, and time estimates
- **Error Recovery**: Failed batches can be retried without reprocessing successful ones

### LLM Configuration
- **Multiple Providers**: Snowflake Cortex, OpenAI, or Anthropic Claude
- **Flexible Scope**: Generate for all columns, selected columns, or columns matching filters
- **Content Control**: Toggle inclusion of data types, sample values, and view descriptions
- **Output Styles**: Choose between technical/data-engineering or business-friendly descriptions
- **Cost Controls**: Configurable batch sizes, column limits, and dry-run token estimation

## 📋 Requirements

- Python 3.8+
- Snowflake account with SSO enabled
- (Optional) OpenAI or Anthropic API key for external LLM providers

## 🛠️ Installation

### 1. Clone the repository

```bash
git clone <your-repo-url>
cd data_catalogue
```

### 2. Create a virtual environment

```bash
python -m venv venv

# On Windows
venv\Scripts\activate

# On macOS/Linux
source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. (Optional) Configure API keys

If using external LLM providers, set environment variables:

**Option A: Environment variables**
```bash
# For OpenAI
export OPENAI_API_KEY="sk-..."

# For Anthropic
export ANTHROPIC_API_KEY="sk-ant-..."
```

**Option B: Streamlit secrets (recommended for deployment)**
```bash
# Copy example secrets file
cp .streamlit/secrets.toml.example .streamlit/secrets.toml

# Edit and add your API keys
nano .streamlit/secrets.toml
```

## 🚀 Running the Application

### Local Development

```bash
streamlit run app.py
```

The app will open in your browser at `http://localhost:8501`

### Production Deployment

For production deployment on Streamlit Cloud, AWS, or other platforms:

1. Ensure `requirements.txt` is up to date
2. Set API keys as environment variables or Streamlit secrets
3. Configure firewall rules to allow Snowflake connections
4. Deploy using your platform's standard process

## 📖 User Guide

### Step 1: Connect to Snowflake

1. In the sidebar, enter:
   - **Account or URL**: Either your Snowflake account identifier (e.g., `xy12345.us-east-1`) or full URL (e.g., `https://xy12345.snowflakecomputing.com`)
   - **Email/Username**: Your Snowflake username (typically your email)
   - (Optional) **Role** and **Warehouse**

2. Click **"Connect (SSO)"**

3. A browser window will open for SSO authentication

4. Once connected, you'll see your connection details in the sidebar

### Step 2: Select a View

1. Use the dropdowns to select:
   - **Database**: Choose from available databases
   - **Schema**: Choose from available schemas in the selected database
   - **View**: Choose from available views in the selected schema

2. (Optional) Add a **View Description**: Provide business context about the view (grain, meaning, caveats). This helps the LLM generate more accurate descriptions.

3. Click **"Load Columns"** to fetch column metadata

### Step 3: Sample Data (Optional but Recommended)

Sampling provides the LLM with example values, resulting in more accurate descriptions.

**Options:**
- **One-pass sampling (default)**: Efficiently samples all columns in a single query
- **Skip sampling**: Faster but LLM only sees column names and types
- **Configure sample size**: Adjust the number of rows to sample (500-5000)

Click **"Sample Data"** to collect example values.

**Performance Notes:**
- For views with 200-1200 columns, one-pass sampling typically completes in 10-60 seconds
- If one-pass fails (permissions or unsupported operations), the app automatically falls back to parallel per-column sampling
- Sampling is cached, so you won't need to repeat it for the same view

### Step 4: Configure LLM Settings

Expand the **"LLM Settings"** panel to configure generation:

#### Provider Selection
- **Snowflake Cortex**: Uses Snowflake's built-in LLM functions (no external API needed)
  - Models: `mistral-large`, `mixtral-8x7b`, `llama2-70b-chat`, `mistral-7b`
- **External Provider**: OpenAI or Anthropic
  - OpenAI: `gpt-4`, `gpt-4-turbo-preview`, `gpt-3.5-turbo`
  - Anthropic: `claude-3-opus`, `claude-3-sonnet`, `claude-3-haiku`

#### Generation Scope
- **All columns**: Document all columns in the view
- **Selected columns**: Manually select specific columns
- **Columns matching filter**: Use text or regex to filter columns

#### Content Controls
- **Include data types**: Show column data types to LLM (recommended)
- **Include sample values**: Show example values (⚠️ may expose sensitive data to LLM)
- **Include view description**: Provide view context to LLM (recommended)

#### Output Configuration
- **Output Style**:
  - Technical/Data Engineering: For technical audiences
  - Business-Friendly: Non-technical language for business users
- **Max Description Length**: 100-500 characters per description

#### Cost Controls
- **Batch size**: Columns per LLM call (25-150, default 60)
  - Larger batches = fewer API calls but more tokens per call
  - Smaller batches = more API calls but lower risk per call
- **Max columns per run**: Safety cap to prevent runaway costs (default 300)
- **Dry-run estimate**: Click to see approximate token usage before running

### Step 5: Generate Documentation

1. Click **"Generate Descriptions"**

2. Monitor progress:
   - Progress bar shows overall completion
   - Batch counter shows current batch / total batches
   - Timer shows elapsed time

3. If any batches fail:
   - Successful batches are preserved
   - Failed columns are marked with error messages
   - Click **"Retry Failed Batches"** to retry only the failed ones

### Step 6: Review and Export Results

#### Results Table
- **Search**: Use the search box to filter columns by name or description
- **Table View**: Browse all columns with descriptions, confidence scores, and sample values
- **Metrics**: View total columns, average confidence, error count, and samples collected

#### Export Options
- **Download CSV**: Simple CSV format for easy importing to other tools
- **Download Excel**: Formatted Excel file with:
  - Frozen header row
  - Auto-filter enabled
  - Text wrapping for descriptions and samples
  - Optimized column widths

## ⚙️ Configuration

### Snowflake Permissions

Your Snowflake user/role needs:
- `USAGE` on database and schema
- `SELECT` on views you want to document
- (For Cortex) `USAGE` on Cortex functions

### LLM Provider Configuration

#### Snowflake Cortex
No additional configuration needed - uses your Snowflake connection.

**Availability**: Check if Cortex is available in your region: [Cortex Documentation](https://docs.snowflake.com/en/user-guide/snowflake-cortex/llm-functions)

#### OpenAI
```bash
# Set environment variable
export OPENAI_API_KEY="sk-..."

# Or add to .streamlit/secrets.toml
OPENAI_API_KEY = "sk-..."
```

#### Anthropic
```bash
# Set environment variable
export ANTHROPIC_API_KEY="sk-ant-..."

# Or add to .streamlit/secrets.toml
ANTHROPIC_API_KEY = "sk-ant-..."
```

### Performance Tuning

For optimal performance with different table sizes:

| Column Count | Batch Size | Sample Size | Expected Time* |
|--------------|------------|-------------|----------------|
| 50-200       | 60-100     | 2000        | 2-5 minutes    |
| 200-500      | 60-80      | 1500        | 5-15 minutes   |
| 500-1000     | 40-60      | 1000        | 15-30 minutes  |
| 1000-1200    | 40-50      | 1000        | 30-45 minutes  |

*Time estimates assume GPT-3.5-turbo or similar speed model

### Memory Considerations

The app uses caching extensively:
- **Connection**: Cached at resource level (single connection per session)
- **Metadata**: Cached for 5 minutes
- **Sampling**: Cached for 10 minutes
- **Results**: Stored in session state until cleared

For very wide tables (1000+ columns), expect ~100-500MB of memory usage for caching.

## 🔧 Troubleshooting

### Connection Issues

**Problem**: SSO authentication fails
- **Solution**: Ensure your Snowflake account has SSO enabled
- **Check**: Verify account identifier is correct (no https:// prefix if entering just the account)
- **Try**: Use full URL format: `https://account.region.snowflakecomputing.com`

**Problem**: "Account not found" error
- **Solution**: Use the exact account identifier from your Snowflake URL
- **Format**: `account.region.cloud` (e.g., `xy12345.us-east-1.aws`)

### Sampling Issues

**Problem**: One-pass sampling fails
- **Solution**: App automatically falls back to per-column sampling
- **Alternative**: Check "Skip sampling" to generate without samples

**Problem**: Sampling is very slow
- **Cause**: View has complex transformations or large data
- **Solution**: Reduce sample size or skip sampling

**Problem**: Permission denied during sampling
- **Solution**: Ensure your role has SELECT permission on the view

### LLM Generation Issues

**Problem**: "API key not found"
- **Solution**: Set OPENAI_API_KEY or ANTHROPIC_API_KEY environment variable
- **Alternative**: Use Snowflake Cortex instead

**Problem**: JSON parsing errors
- **Solution**: App automatically retries with stricter prompts
- **Check**: Ensure you're using a capable model (gpt-3.5-turbo or better)

**Problem**: Descriptions are low quality
- **Solution**:
  - Enable "Include sample values" for more context
  - Provide a detailed view description
  - Try a more capable model (gpt-4, claude-3-sonnet)
  - Adjust output style to match your needs

**Problem**: Rate limit errors
- **Solution**:
  - Reduce batch size to make smaller API calls
  - Reduce max columns to process fewer columns
  - Wait a moment and retry failed batches

### Export Issues

**Problem**: "openpyxl not installed" warning
- **Solution**: `pip install openpyxl`

**Problem**: Excel file doesn't open
- **Solution**: Try CSV export instead, or update openpyxl: `pip install --upgrade openpyxl`

## 🏗️ Architecture

### Key Design Decisions

1. **One-Pass Sampling**: Instead of querying each column individually, the app samples all columns in a single query, then processes the results in Python. This is 10-100x faster for wide tables.

2. **Batched LLM Calls**: Columns are processed in batches to balance token limits, cost, and error resilience. Failed batches can be retried independently.

3. **Progressive Enhancement**: The app works with just column names and types, but provides better results when sample values and view descriptions are available.

4. **Defensive Error Handling**: Every LLM batch is wrapped in error handling, and partial results are preserved even if some batches fail.

5. **Smart Caching**: Metadata and sampling results are aggressively cached to avoid repeated expensive operations.

### File Structure

```
data_catalogue/
├── app.py                          # Main Streamlit application
├── requirements.txt                # Python dependencies
├── README.md                       # This file
├── .gitignore                      # Git ignore rules
└── .streamlit/
    └── secrets.toml.example        # Example secrets file
```

### Technologies Used

- **Streamlit**: Web application framework
- **snowflake-connector-python**: Snowflake database connectivity
- **pandas**: Data manipulation and export
- **openpyxl**: Excel file generation
- **openai**: OpenAI API client (optional)
- **anthropic**: Anthropic API client (optional)

## 🤝 Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🙏 Acknowledgments

- Built with [Streamlit](https://streamlit.io/)
- Powered by [Snowflake](https://www.snowflake.com/)
- LLM support from [OpenAI](https://openai.com/) and [Anthropic](https://www.anthropic.com/)

## 📧 Support

For issues, questions, or suggestions:
- Open an issue in the GitHub repository
- Contact the maintainer at [your-email@example.com]

---

**Built with ❤️ for the data community**
