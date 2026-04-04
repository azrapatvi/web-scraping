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

**Tech stack:** `requests`, `BeautifulSoup`, `pandas`, `lxml`

---

### 2. Amazon Beauty Products Scraper
**File:** `amazon_data_scraping.ipynb`

Scrapes beauty product listings from Amazon India, extracting:
- Product name
- Rating
- Price

**Tech stack:** `requests`, `BeautifulSoup`, `pandas`, `lxml`

---

### 3. Books to Scrape — Full Catalogue Scraper
**File:** `books_scraped_data.ipynb`

Scrapes all 1000 books across 50 pages from [books.toscrape.com](https://books.toscrape.com), extracting:
- Book name
- Price
- Star rating (as word → e.g. "Three")
- Stock availability (listing page)
- Book description (from individual book page)
- Book image URL
- Link to individual book page

**Tech stack:** `requests`, `BeautifulSoup`, `pandas`, `lxml`, `urllib.parse`

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
}
```

#### 12. Extracting from `aria-label` Attributes
Amazon stores the actual product title in an `aria-label` attribute, not as inner text. Learned to use `.get()` to read HTML attributes:
```python
text = title_tag.get('aria-label', '').strip()
```

#### 13. Filtering Out Sponsored Results
Sponsored product cards share the same HTML structure as real ones. Learned to skip them by checking the title:
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
When some products are missing a rating or price, appending `None` keeps all lists the same length — critical for building a valid DataFrame:
```python
ratings.append(span_tag.text.strip() if span_tag else None)
```

#### 16. Verifying List Lengths Before Creating a DataFrame
```python
len(prod_name), len(ratings), len(prices)
```

#### 17. Controlling Display with pandas Options
Learned to stop pandas from truncating long strings in output:
```python
pd.set_option('display.max_colwidth', None)
```

---

### From Project 3 — Books to Scrape

#### 18. Not All Sites Need Headers
`books.toscrape.com` is a practice site designed for scraping — it returned data with a plain `requests.get()` and no headers at all. Learned that headers are only needed when a site actively blocks bots.

#### 19. Reading Data from HTML Attributes
Book names were stored in the `alt` attribute of `<img>` tags, image URLs in `src`, and page links in `href` — not in the visible text. Learned to use `.get()` for all of these:
```python
book_name.append(img_tag.get('alt'))
imgs.append("https://books.toscrape.com/" + img_tag.get('src'))
href = a_tag.get('href')
```

#### 20. Extracting Ratings Stored as CSS Class Names
Star ratings weren't stored as numbers or text — they were encoded as a CSS class like `star-rating Three`. Learned to read the class list and grab the second value:
```python
classes = i.get('class')  # returns ['star-rating', 'Three']
ratings.append(classes[1])  # → 'Three'
```

#### 21. Following Links to Scrape Deeper Data
The listing page didn't have descriptions — those were only on individual book pages. Learned to build each book's URL and make a second request inside the loop:
```python
book_url = urljoin(url, a_tag.get('href'))
response1 = requests.get(book_url)
soup1 = BeautifulSoup(response1.text, 'lxml')
```

#### 22. Fixing Broken URLs with `urljoin`
Naively joining the base URL with a relative `href` like `../../a-light-in-the-attic/index.html` produces a broken URL. Learned to use `urljoin` from Python's built-in `urllib.parse` which correctly resolves `../` paths:
```python
from urllib.parse import urljoin
full_url = urljoin(url, a_tag.get('href'))  # resolves ../../ correctly
```

#### 23. Using `find_next_sibling()` to Navigate Relative to a Known Element
The book description had no unique class — it was just a `<p>` tag after a `<div id="product_description">`. Learned to find that anchor div and grab what comes right after it:
```python
div = soup1.find('div', id='product_description')
p = div.find_next_sibling('p')
description.append(p.get_text(strip=True))
```

#### 24. Fixing Encoding Issues with `response.encoding`
Some book descriptions showed garbled characters like `Â£` instead of `£`. Learned to explicitly set the encoding before parsing:
```python
response1.encoding = 'utf-8'
```

#### 25. Cleaning Duplicate Text with String Splitting
Some descriptions had trailing junk text like `...more` appended at the end. Learned to clean it by splitting on that pattern:
```python
text = text.split('...more')[0]
```

#### 26. Adding a New Column to an Existing DataFrame
Instead of rebuilding the whole DataFrame, learned to assign a new list directly as a column:
```python
final_df['description page'] = desc_page
final_df['description of book'] = description
```

#### 27. Checking `final_df.shape` to Verify Scrape Results
After scraping, learned to quickly check how many rows and columns were collected:
```python
final_df.shape  # → (1000, 5) means 1000 books, 5 columns
```

---

## Setup

```bash
pip install requests beautifulsoup4 pandas lxml
```

## Notes
- Some websites block scrapers — adding a `User-Agent` header often helps
- Amazon requires a fuller header bundle than most sites
- Practice sites like `books.toscrape.com` need no headers at all
- Always use `urljoin` instead of string concatenation for building URLs from relative `href` values
- Always set `response.encoding = 'utf-8'` when scraping sites with special characters
- Always check a site's `robots.txt` before scraping
- Be respectful: add delays between requests for large-scale scraping

