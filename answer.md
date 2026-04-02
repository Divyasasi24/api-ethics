import requests
import time

API_URL = "https://healthstats-api.example.com/records"
API_KEY = "free_tier_key_abc123"

RATE_LIMIT_DELAY = 6  # 6 seconds = ~10 requests per minute (free tier safe)

records = []
for page in range(1, 101):
    response = requests.get(API_URL, params={"page": page, "key": API_KEY})

    if response.status_code == 429:  # Too Many Requests
        print(f"Rate limit hit on page {page}. Backing off 60s...")
        time.sleep(60)
        continue

    response.raise_for_status()
    data = response.json()
    records.extend(data["results"])

    time.sleep(RATE_LIMIT_DELAY)  # respectful delay between every request
