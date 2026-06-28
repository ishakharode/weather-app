# Weather Now 🌤️

A weather app that shows live current conditions and a 5-day forecast for any city, built with vanilla JavaScript.

**🔗 Live demo:** https://ishakharode.github.io/weather-app/

---

## What it does

- User types a city name and clicks Search
- App shows current temperature, "feels like" temperature, humidity, wind speed
- App shows a 5-day forecast with daily high/low temperatures
- Shows a loading state while fetching, and a clear error message if the city isn't found or the network fails

---

## How it works 

This app talks to two free APIs from **Open-Meteo** (no API key required):

1. **Geocoding step** — The city name (e.g. "Pune") is sent to the Open-Meteo Geocoding API, which returns latitude/longitude coordinates for that city.
2. **Weather step** — Those coordinates are sent to the Open-Meteo Forecast API, which returns the current weather and a 5-day forecast.
3. **Render step** — The returned data (JSON) is used to update the page: temperature, icons, forecast cards, etc.

These two API calls are **sequential, not parallel** — the second call needs the result (coordinates) from the first call, so it has to wait for the first one to finish before it can start.

---

## Tech used

- Plain HTML, CSS, JavaScript — no frameworks, no build tools
- `fetch()` with `async/await` for making API calls
- `try / catch / finally` for error handling
- CSS variables for consistent theming

---

## Code structure

| Part | What it does |
|---|---|
| `weatherInfo(code)` | Converts Open-Meteo's numeric weather codes (e.g. `61`) into an emoji + label (e.g. "🌧️ Light rain") using a lookup object |
| `getCoordinates(city)` | `async` function — calls the geocoding API, returns the matched place (name, country, lat, lon) |
| `getWeather(lat, lon)` | `async` function — calls the forecast API using those coordinates, returns current + daily weather data |
| `renderWeather(place, weatherData)` | Takes the API data and writes it into the DOM (text content, forecast cards) |
| `getMockData(city)` | Fallback used only if the real API call fails — generates realistic-looking placeholder data so the demo still works offline |
| `searchWeather()` | The main function tying it all together: calls the two API functions in order, handles loading/error states, and renders the result |

---



****summary
> "I built a weather app that fetches live data from a public REST API. The user enters a city name, and the app makes two sequential API calls — one to convert the city name into coordinates, and another to fetch the weather for those coordinates. I used async/await to manage the asynchronous calls and try/catch/finally for error handling, so the app fails gracefully if the city doesn't exist or the network is down."

**Concepts to be ready to explain:**

1. **`async` / `await`**
   Lets you write asynchronous code (like API calls) in a way that reads top-to-bottom, instead of nesting `.then()` callbacks. `await` pauses execution of *that function* until the Promise resolves — it does **not** freeze the whole browser.

2. **Why sequential calls, not `Promise.all()`**
   `Promise.all()` is for *independent* calls that can run at the same time. Here, the second call depends on the result of the first (you need coordinates before you can ask for weather), so they must run one after another.

3. **`try / catch / finally`**
   - `try`: the "happy path" code
   - `catch`: runs if anything in `try` throws an error (bad city name, network failure)
   - `finally`: always runs regardless of success or failure — used here to re-enable the search button

4. **`fetch()` and Promises**
   `fetch()` returns a Promise that resolves to a `Response` object. `response.json()` is itself async and parses the body into a usable JavaScript object.

5. **CORS (Cross-Origin Resource Sharing)**
   A browser security rule that blocks a webpage from calling an API on a different domain unless that server explicitly allows it. Open-Meteo allows it (CORS-enabled), which is why this works directly from the browser with no backend server needed.

6. **Why there's a mock data fallback**
   While developing, the live API was blocked in a sandboxed preview environment. Rather than leaving the demo broken, I added a fallback that generates plausible placeholder data so the UI can still be demonstrated, while the real fetch logic remains the primary path and works fully once deployed (e.g. on GitHub Pages).

**Likely follow-up questions:**

- *"What would you improve?"* → Add debounce on the search input (avoid firing a request on every keystroke), cache recent city results, add a loading skeleton instead of plain text.
- *"What happens if the API is down?"* → The `catch` block handles it — shows an error message instead of crashing.
- *"Why no framework (React etc.)?"* → The app is small enough that vanilla JS keeps it simple and dependency-free; the same logic would carry over directly into a React component using `useState`/`useEffect`.

---

## Run locally

Download `index.html` and open it directly in any browser — no installation or build step needed.

## Possible improvements

- Debounce the search input to avoid unnecessary API calls
- Cache results for recently searched cities
- Add a geolocation button to auto-detect the user's current city
