# Best Flight Deals Bot ✈️

A Python bot that automatically checks for cheap flight deals from your origin city to a list of destinations, and flags whenever it finds a price lower than your current lowest recorded price.

It reads your list of destinations (city, IATA code, and current lowest price) from a Google Sheet, searches for live flight prices using SerpApi's Google Flights engine, compares them, and updates the sheet whenever a better deal shows up.

## How it works

1. **`data_manager.py`** — talks to your Google Sheet through the [Sheety](https://sheety.co/) API. It fetches your destination list and can push updated "lowest price" values back to the sheet.
2. **`flight_search.py`** — queries [SerpApi's Google Flights API](https://serpapi.com/google-flights-api) for round-trip flights between an origin and destination within a date window.
3. **`flight_data.py`** — parses the raw flight search results and works out the cheapest flight found.
4. **`main.py`** — ties it all together: loads the destination data, searches for flights (currently configured for Lahore ✈️ Bahrain, one day from now through six months out), and prints/updates the cheapest price found. It also caches API responses locally (via `requests_cache`) so repeated runs don't burn through your free-tier API quota.

## Setup

### 1. Clone and install dependencies

```bash
git clone https://github.com/NinjaVinja/best-flight-deals-bot.git
cd best-flight-deals-bot
pip install -r requirements.txt
```

### 2. Set up your Google Sheet + Sheety

- Create a Google Sheet with columns for `city`, an airport/city code, and `lowestPrice`.
- Connect it to [Sheety](https://sheety.co/) to get a REST API endpoint and project ID.
- Set a username/password in Sheety for basic auth.

### 3. Get a SerpApi key

- Sign up at [serpapi.com](https://serpapi.com/) and grab your API key (there's a free tier).

### 4. Configure environment variables

Copy `.env.example` to `.env` and fill in your own values:

```bash
cp .env.example .env
```

```env
SHEETY_API_KEY="your-sheety-project-id"
SHEETY_USERNAME="your-sheety-username"
SHEETY_PASSWORD="your-sheety-password"

SERPAPI_API_KEY="your-serpapi-key"
```

> ⚠️ **Never commit your real `.env` file.** It's already excluded via `.gitignore`.

### 5. Run it

```bash
python main.py
```

By default it checks flights from `LHE` (Lahore) to `BAH` (Bahrain) for a return trip roughly one to six months out. Edit the parameters in `main.py` to change the origin, destination(s), or date range.

## Tech stack

- Python
- [Requests](https://docs.python-requests.org/) for HTTP calls
- [requests-cache](https://requests-cache.readthedocs.io/) to avoid burning API quota on repeated runs
- [python-dotenv](https://pypi.org/project/python-dotenv/) for environment variable management
- [Sheety](https://sheety.co/) as a lightweight Google Sheets → REST API bridge
- [SerpApi Google Flights](https://serpapi.com/google-flights-api) for live flight data

## Possible improvements

- [ ] Support multiple destinations from the sheet in a single run (currently only checks the first row)
- [ ] Add email/SMS/WhatsApp notifications when a deal is found
- [ ] Schedule the script to run automatically (e.g. via `cron` or GitHub Actions)
- [ ] Add tests for `find_cheapest_flight`

## License

This project is open source and available under the [MIT License](LICENSE).
