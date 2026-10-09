# webtables
```py
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import time

driver = webdriver.Chrome()
driver.maximize_window()

driver.get("https://assertqa.com/practice/webtables")

wait = WebDriverWait(driver, 10)

# Wait for table to load
wait.until(EC.presence_of_element_located((By.TAG_NAME, "table")))
time.sleep(2)

# TC01 - Print all column headings
print("\nTC01 - Column Headings")

headers = driver.find_elements(By.XPATH, "//table//thead//th")

for header in headers:
    print(header.text)


# TC02 - Print the first data row
print("\nTC02 - First Data Row")

rows = driver.find_elements(By.XPATH, "//table//tbody/tr")

if rows:
    print(rows[0].text)
else:
    print("No data rows found")


# TC03 - Print the last data row
print("\nTC03 - Last Data Row")

# Go to the last page using the Last button if available
last_buttons = driver.find_elements(
    By.XPATH,
    "//button[contains(., 'Last')]"
)

if last_buttons and last_buttons[0].is_enabled():
    last_buttons[0].click()
    time.sleep(1)

rows = driver.find_elements(By.XPATH, "//table//tbody/tr")

if rows:
    print(rows[-1].text)
else:
    print("No data rows found")


# TC04 - Search employee by last name
print("\nTC04 - Search by Last Name")

search = driver.find_element(
    By.XPATH,
    "//input[contains(@placeholder, 'Search') or @type='search']"
)

search.clear()
search.send_keys("Smith")
time.sleep(1)

rows = driver.find_elements(By.XPATH, "//table//tbody/tr")

if rows and any("Smith" in row.text for row in rows):
    for row in rows:
        if "Smith" in row.text:
            print(row.text)
else:
    print("No matching employee found")

search.clear()
time.sleep(1)


# TC05 - Extract all email addresses
print("\nTC05 - All Email Addresses")

emails = []

# Collect records from every page
while True:
    rows = driver.find_elements(By.XPATH, "//table//tbody/tr")

    for row in rows:
        cells = row.find_elements(By.TAG_NAME, "td")

        for cell in cells:
            value = cell.text.strip()
            if "@" in value and "." in value:
                if value not in emails:
                    emails.append(value)

    next_buttons = driver.find_elements(
        By.XPATH,
        "//button[contains(., 'Next')]"
    )

    if not next_buttons or not next_buttons[0].is_enabled():
        break

    next_buttons[0].click()
    time.sleep(1)

for email in emails:
    print(email)


# TC06 - Employee with highest Due amount
print("\nTC06 - Highest Due Amount")

# Go to first page
first_buttons = driver.find_elements(
    By.XPATH,
    "//button[contains(., 'First')]"
)

if first_buttons and first_buttons[0].is_enabled():
    first_buttons[0].click()
    time.sleep(1)

employees = []

while True:
    rows = driver.find_elements(By.XPATH, "//table//tbody/tr")

    for row in rows:
        cells = row.find_elements(By.TAG_NAME, "td")

        if len(cells) >= 4:
            last_name = cells[0].text.strip()
            first_name = cells[1].text.strip()
            email = cells[2].text.strip()
            due_text = cells[3].text.strip()

            try:
                due = float(
                    due_text.replace("$", "").replace(",", "")
                )
                employees.append(
                    (first_name, last_name, due, email)
                )
            except ValueError:
                pass

    next_buttons = driver.find_elements(
        By.XPATH,
        "//button[contains(., 'Next')]"
    )

    if not next_buttons or not next_buttons[0].is_enabled():
        break

    next_buttons[0].click()
    time.sleep(1)

if employees:
    highest = max(employees, key=lambda employee: employee[2])
    print("Employee:", highest[0], highest[1])
    print("Due amount: $", highest[2])
else:
    print("No Due amounts found")


# TC07 - Verify a particular website link exists
print("\nTC07 - Website Link Verification")

target_url = "http://www.jsmith.com"

# Search all rows on all pages for the target website
found = False

first_buttons = driver.find_elements(
    By.XPATH,
    "//button[contains(., 'First')]"
)

if first_buttons and first_buttons[0].is_enabled():
    first_buttons[0].click()
    time.sleep(1)

while True:
    links = driver.find_elements(
        By.XPATH,
        "//table//tbody//a"
    )

    for link in links:
        href = link.get_attribute("href")
        if href and target_url in href:
            found = True
            print("Link found:", href)
            break

    if found:
        break

    next_buttons = driver.find_elements(
        By.XPATH,
        "//button[contains(., 'Next')]"
    )

    if not next_buttons or not next_buttons[0].is_enabled():
        break

    next_buttons[0].click()
    time.sleep(1)

if found:
    print("PASS")
else:
    print("FAIL - Website link not found")


# TC08 - Count data rows without the header
print("\nTC08 - Total Data Rows")

# Return to the first page
first_buttons = driver.find_elements(
    By.XPATH,
    "//button[contains(., 'First')]"
)

if first_buttons and first_buttons[0].is_enabled():
    first_buttons[0].click()
    time.sleep(1)

total_rows = 0

while True:
    rows = driver.find_elements(By.XPATH, "//table//tbody/tr")
    total_rows += len(rows)

    next_buttons = driver.find_elements(
        By.XPATH,
        "//button[contains(., 'Next')]"
    )

    if not next_buttons or not next_buttons[0].is_enabled():
        break

    next_buttons[0].click()
    time.sleep(1)

print("Total data rows:", total_rows)

input("\nPress Enter to close the browser...")
driver.quit()
```
<img width="1919" height="1016" alt="Screenshot 2026-10-09 141416" src="https://github.com/user-attachments/assets/ce90074d-973f-4fa9-bb5a-90ee4bdd6658" />
<img width="723" height="813" alt="Screenshot 2026-10-09 141825" src="https://github.com/user-attachments/assets/e957bf10-62c2-4bca-94ee-53a73f8c8ecc" />
