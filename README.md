# Web Scraping Learning Journey 🕷️

A collection of web scraping projects built while learning Python scraping techniques using `requests`, `BeautifulSoup`, and `pandas`.

## Projects

### 1. AmbitionBox Company Scraper
**File:** `ambitionbox_scraping.ipynb`

Scrapes company listings from [AmbitionBox](https://www.ambitionbox.com/list-of-companies), extracting:
- Company name
- Rating
- Industry type & location
- Number of reviews
- Number of salaries

**Key concepts covered:**
- Bypassing 403 errors using custom User-Agent headers
- Parsing HTML with BeautifulSoup (`find_all`, `class_`, `.text.strip()`)
- Extracting structured data from nested HTML elements
- Looping across multiple pages to collect larger datasets
- Saving results to CSV with pandas

**Tech stack:** `requests`, `BeautifulSoup`, `pandas`, `lxml`

## Setup
```bash
pip install requests beautifulsoup4 pandas lxml
```

## Notes

- Some websites block scrapers — adding a `User-Agent` header often helps
- Always check a site's `robots.txt` before scraping
- Be respectful: add delays between requests for large-scale scraping

