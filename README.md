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

## 🧠 What I Learned

### 1. Handling 403 Errors with Headers
My first request to AmbitionBox returned a blocked response. I learned that many websites detect and block scraper bots. The fix was adding a `User-Agent` header to mimic a real browser:
```python
headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 6.3; Win64; x64) ...'}
response = requests.get(url, headers=headers).text
```

### 2. Parsing HTML with BeautifulSoup
Learned to load raw HTML into BeautifulSoup and navigate it:
```python
soup = BeautifulSoup(response, "lxml")
soup.prettify()  # nicely formatted HTML for inspection
```

### 3. Finding Elements by Tag and Class
Learned `find_all()` to locate elements, and how to filter by CSS class name:
```python
soup.find_all('h2', class_='companyCardWrapper__companyName')
```

### 4. Extracting Clean Text
Discovered two ways to strip whitespace from element text:
```python
element.text.strip()
element.get_text(strip=True)
```

### 5. Navigating Nested HTML
Rather than searching the whole page, I learned to first find a parent container, then search *inside* it — which is more reliable and avoids mismatches:
```python
company = soup.find_all('div', class_='companyCardWrapper')
for i in company:
    print(i.find('h2').text.strip())  # search within each card
```

### 6. Conditional Extraction (Reviews vs Salaries)
Some elements shared the same class name but contained different data. I learned to extract them by checking inner text labels:
```python
if "Review" in title_text:
    rev = count_text
if "Salar" in title_text:
    sal = count_text
```

### 7. Defensive Coding with `None` Checks
In the multi-page scraper, I learned to handle missing elements gracefully so the scraper doesn't crash:
```python
name_tag = comp.find('h2')
name.append(name_tag.text.strip() if name_tag else None)
```

### 8. Multi-Page Scraping with a Loop
Scaled from scraping 1 page to 9 pages using an f-string URL and a `for` loop:
```python
for page in range(1, 10):
    url = f"https://www.ambitionbox.com/list-of-companies?page={page}"
```

### 9. Combining DataFrames with `pd.concat`
Instead of overwriting data each iteration, I learned to append each page's DataFrame to a master one:
```python
final_df = pd.concat([final_df, df], ignore_index=True)
```

### 10. Saving Data to CSV
```python
final_df.to_csv('data/scraped_data.csv', index=False)
```

---

## Setup
```bash
pip install requests beautifulsoup4 pandas lxml
```

## Notes
- Some websites block scrapers — adding a `User-Agent` header often helps
- Always check a site's `robots.txt` before scraping
- Be respectful: add delays between requests for large-scale scraping

## Upcoming Projects
*(Add your future scraping projects here)*