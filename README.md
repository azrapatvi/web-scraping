# Web Scraping Learning 

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

**Tech stack:** `requests`, `BeautifulSoup`, `pandas`, `lxml`

---

### 2. Amazon Beauty Products Scraper
**File:** `amazon_data_scraping.ipynb`

Scrapes beauty product listings from Amazon India, extracting:
- Product name
- Rating
- Price

**Key concepts covered:**
- Sending a full browser-like header bundle to avoid bot detection
- Extracting product names from `aria-label` attributes (not inner text)
- Filtering out sponsored listings before storing data
- Using `data-component-type` attribute to isolate real product cards
- Aligning multiple lists with `None` fallbacks to prevent DataFrame shape errors
- Verifying list lengths before building a DataFrame
- Multi-page scraping with f-string URLs and `pd.concat`
- Saving results to CSV

**Tech stack:** `requests`, `BeautifulSoup`, `pandas`, `lxml`

---

## 🧠 What I Learned

### From Project 1 — AmbitionBox

#### 1. Handling 403 Errors with Headers
My first request to AmbitionBox returned a blocked response. I learned that many websites detect and block scraper bots. The fix was adding a `User-Agent` header to mimic a real browser:
```python
headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 6.3; Win64; x64) ...'}
response = requests.get(url, headers=headers).text
```

#### 2. Parsing HTML with BeautifulSoup
Learned to load raw HTML into BeautifulSoup and navigate it:
```python
soup = BeautifulSoup(response, "lxml")
soup.prettify()  # nicely formatted HTML for inspection
```

#### 3. Finding Elements by Tag and Class
Learned `find_all()` to locate elements, and how to filter by CSS class name:
```python
soup.find_all('h2', class_='companyCardWrapper__companyName')
```

#### 4. Extracting Clean Text
Discovered two ways to strip whitespace from element text:
```python
element.text.strip()
element.get_text(strip=True)
```

#### 5. Navigating Nested HTML
Rather than searching the whole page, I learned to first find a parent container, then search *inside* it:
```python
company = soup.find_all('div', class_='companyCardWrapper')
for i in company:
    print(i.find('h2').text.strip())
```

#### 6. Conditional Extraction
Some elements shared the same class but held different data. I learned to check inner text labels to separate them:
```python
if "Review" in title_text:
    rev = count_text
if "Salar" in title_text:
    sal = count_text
```

#### 7. Defensive Coding with `None` Checks
Learned to handle missing elements gracefully so the scraper doesn't crash:
```python
name_tag = comp.find('h2')
name.append(name_tag.text.strip() if name_tag else None)
```

#### 8. Multi-Page Scraping with a Loop
```python
for page in range(1, 10):
    url = f"https://www.ambitionbox.com/list-of-companies?page={page}"
```

#### 9. Combining DataFrames with `pd.concat`
```python
final_df = pd.concat([final_df, df], ignore_index=True)
```

#### 10. Saving Data to CSV
```python
final_df.to_csv('data/scraped_data.csv', index=False)
```

---

### From Project 2 — Amazon

#### 11. Sending a Full Header Bundle
Amazon is stricter than most sites. A single `User-Agent` wasn't enough — I learned to send a realistic full header set including `Accept-Language`, `Accept-Encoding`, and `Connection`:
```python
headers = {
    "User-Agent": "Mozilla/5.0 ...",
    "Accept-Language": "en-US,en;q=0.9",
    "Accept-Encoding": "gzip, deflate, br",
    "Connection": "keep-alive",
    ...
}
```

#### 12. Extracting from `aria-label` Attributes
Amazon stores the actual product title in an `aria-label` attribute of the `<h2>` tag, not as inner text. Learned to use `.get()` to read HTML attributes:
```python
text = title_tag.get('aria-label', '').strip()
```

#### 13. Filtering Out Sponsored Results
Sponsored product cards share the same HTML structure as real ones. Learned to skip them by checking if the title starts with `"Sponsored"`:
```python
if text.startswith('Sponsored'):
    continue
```

#### 14. Targeting Product Cards with `data-component-type`
Learned to use custom HTML attributes (not just `class`) to precisely select elements:
```python
all_prods = soup.find('div', class_='s-main-slot').find_all('div', {'data-component-type': 's-search-result'})
```

#### 15. Keeping Lists Aligned with `None` Fallbacks
When some products are missing a rating or price, appending `None` keeps all three lists the same length — critical for building a valid DataFrame:
```python
ratings.append(span_tag.text.strip() if span_tag else None)
prices.append(price_tag.text.strip() if price_tag else None)
```

#### 16. Verifying List Lengths Before Creating a DataFrame
Learned to always check lengths match before building a DataFrame, to catch alignment bugs early:
```python
len(prod_name), len(ratings), len(prices)
```

#### 17. Controlling Display with pandas Options
Learned to stop pandas from truncating long strings in the output:
```python
pd.set_option('display.max_colwidth', None)
```

---

## Setup
```bash
pip install requests beautifulsoup4 pandas lxml
```

## Notes
- Some websites block scrapers — adding a `User-Agent` header often helps
- Amazon requires a fuller header bundle than most sites
- Always check a site's `robots.txt` before scraping
- Be respectful: add delays between requests for large-scale scraping

## Upcoming Projects
*(Add your future scraping projects here)*