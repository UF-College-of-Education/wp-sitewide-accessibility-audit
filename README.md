# WordPress Sitewide Accessibility Audit Tool

A Node.js script that performs comprehensive accessibility audits on WordPress sites. Automatically detects sitemaps, runs pa11y checks on all pages, and generates detailed HTML reports with automatic retry logic and configurable settings.

## Features

### Core Capabilities
- **Automatic Sitemap Detection**: Checks `/sitemap_index.xml`, `/wp-sitemap.xml`, and `/sitemap.xml`
- **Comprehensive Coverage**: Parses sitemap indexes and extracts URLs from posts, pages, categories, tags, and authors
- **WCAG 2.1 Level AA Compliance**: Tests against industry-standard accessibility guidelines
- **Detailed HTML Reports**: Includes summary statistics, per-page issues, WCAG references, HTML context, and severity levels

### Reliability Features
- Automatic retry with exponential backoff (up to 3 attempts)
- Configurable concurrent processing for scalable performance
- Extended timeouts (90s default) for slow-loading pages
- Optimized Chrome flags for enhanced browser stability
- Rate limiting protection with configurable delays
- Automatic browser instance cleanup
- Retry statistics in generated reports

## Prerequisites

- Node.js 14.0.0 or higher
- npm or yarn

## Installation

Clone the repository and install dependencies:

```bash
npm install
```

## Quick Start

Display help information:

```bash
npm run help
```

Run an audit:

```bash
node audit.js yourwordpresssite.com
```

## Usage

### Basic Usage

Audit any WordPress website by providing its URL:

```bash
node audit.js yourwordpresssite.com
```

Supported URL formats:

```bash
# Without protocol (https:// added automatically)
node audit.js yourwordpresssite.com

# With protocol
node audit.js https://yourwordpresssite.com

# Direct sitemap URL
node audit.js https://yourwordpresssite.com/custom-sitemap.xml
```

### npm Scripts

```bash
# Standard audit
npm run audit -- yourwordpresssite.com

# Audit and open report in browser
npm run audit:open -- yourwordpresssite.com

# Maximum reliability mode
npm run audit:reliable -- yourwordpresssite.com

# Reliability mode with auto-open
npm run audit:reliable:open -- yourwordpresssite.com

# Programmatic usage example
npm run example -- yourwordpresssite.com
```

**Note:** The `--` is required when passing arguments to npm scripts.

### Additional Scripts

- **audit-and-open.js**: Runs audit and opens report in default browser
- **run-reliable-audit.js**: Runs audit with maximum reliability settings
- **example-usage.js**: Demonstrates programmatic usage for CI/CD integration

```bash
node audit-and-open.js yourwordpresssite.com
node run-reliable-audit.js yourwordpresssite.com
node example-usage.js yourwordpresssite.com
```

## Output

Generates `report.html` with:

- **Summary Statistics**: Pages scanned, success/failure rates, total issues
- **Issue Breakdown**: Error and warning counts
- **Page-by-Page Results**: Detailed accessibility issues per page
- **Issue Details**: Type, WCAG code, message, HTML context, CSS selector

## Report Structure

### Header Section
- Site URL
- Generation timestamp
- WCAG standard version

### Summary Cards
- Pages scanned
- Successful/failed checks
- Total issues
- Error and warning counts

### Detailed Results
- Page titles and URLs
- Complete accessibility issue lists
- Context and selectors for debugging

## Configuration

### Default Settings
- Standard: WCAG 2.1 Level AA
- Timeout: 90 seconds per page
- Wait: 3 seconds after page load
- Includes warnings (excludes notices)
- Max retries: 3 attempts per URL
- Concurrent checks: 1
- Delay between requests: 5 seconds
- Batch size: 3 URLs

These defaults prioritize reliability. Increase concurrency for faster scans on stable sites.

### Environment Variables

```bash
# Concurrency and Performance
PA11Y_MAX_CONCURRENT=3          # Default: 1
PA11Y_BATCH_SIZE=10             # Default: 3
PA11Y_REQUEST_DELAY=3000        # Milliseconds, default: 5000

# Timeouts
PA11Y_PAGE_TIMEOUT=120000       # Milliseconds, default: 90000
PA11Y_PAGE_WAIT=5000            # Milliseconds, default: 3000
PA11Y_NAV_TIMEOUT=120000        # Milliseconds, default: 90000

# Retry Configuration
PA11Y_MAX_RETRIES=5             # Default: 3
PA11Y_RETRY_DELAY=10000         # Milliseconds, default: 5000
PA11Y_RETRY_MULTIPLIER=1.5      # Default: 2
```

### Configuration Examples

```bash
# Increased concurrency and timeouts
PA11Y_MAX_CONCURRENT=5 PA11Y_PAGE_TIMEOUT=120000 node audit.js yourwordpresssite.com

# Maximum reliability
PA11Y_MAX_CONCURRENT=1 PA11Y_MAX_RETRIES=5 PA11Y_REQUEST_DELAY=5000 node audit.js yourwordpresssite.com
```

## Architecture

### Custom Report Generation

Uses custom HTML generation instead of standard pa11y reporters for:

1. **Multi-page aggregation**: Standard reporters handle single pages only
2. **Summary statistics**: Calculates totals across all pages
3. **Unified reports**: Single `report.html` file with navigation and filtering
4. **WordPress-specific context**: Better organization for multi-page audits

## Troubleshooting

### Common Issues

**Timeout errors**
- Automatic retry included by default
- Default timeout: 90 seconds
- Increase timeout: `PA11Y_PAGE_TIMEOUT=120000 node audit.js yoursite.com`

**Network errors**
- Automatic retry logic included
- Increase attempts: `PA11Y_MAX_RETRIES=5 node audit.js yoursite.com`
- Default delay: 5 seconds between requests

**Memory issues**
- Increase Node.js memory for large sites:
```bash
node --max-old-space-size=4096 audit.js yoursite.com
```

**Rate limiting**
- Default: 5-second delays with single concurrent check
- More conservative: `PA11Y_REQUEST_DELAY=10000 node audit.js yoursite.com`

**Sitemap not found**
- Automatic detection checks: `/sitemap_index.xml`, `/wp-sitemap.xml`, `/sitemap.xml`
- Provide direct URL if needed: `node audit.js https://yoursite.com/custom-sitemap.xml`

### Error Handling

The script provides:
- Automatic retries with exponential backoff
- Error classification (retryable vs. non-retryable)
- Graceful degradation for failed pages
- Detailed logging with retry information
- Guaranteed browser cleanup on exit
- Real-time progress tracking

### Performance Optimization

**Faster scans** (stable sites):
```bash
PA11Y_MAX_CONCURRENT=5 PA11Y_BATCH_SIZE=10 node audit.js yoursite.com
```

**Reliable scans** (problematic sites):
```bash
PA11Y_MAX_CONCURRENT=1 PA11Y_REQUEST_DELAY=5000 node audit.js yoursite.com
```

**Maximum reliability** (challenging sites):
```bash
PA11Y_MAX_CONCURRENT=1 PA11Y_MAX_RETRIES=5 PA11Y_PAGE_TIMEOUT=120000 PA11Y_REQUEST_DELAY=5000 node audit.js yoursite.com
```

## License

This tool is provided as-is for accessibility testing purposes.
