
# Smart Mirror

A customizable smart mirror web app built with Vue and Vite. This project features several widgets for real-time information display.

## Widgets

### WeatherWidget
Displays the current time, date, and live weather for Akron, Ohio. Weather updates every 30 minutes between 5am and 10pm.

### CommuteWidget
Shows drive times from your home to multiple destinations (Westfield, Ohm, Painting) using Mapbox Directions API. Updates every 15 minutes between 6am and 10am.

### CalendarWidget
Fetches and displays the next 5 upcoming events from your Google Calendar ("Gabe & Hannah" calendar) using a NoCodeAPI endpoint.

## Local Setup
1. Clone the repository.
2. Run `npm install` to install dependencies.
3. Run `npm run dev` to start the development server.
4. Open your browser to the provided localhost URL.

# Production Setup
1. Run `npm run deploy:all` to build and deploy the app to GitHub Pages.
2. After deployment, your app will be live at: https://glidderdale.github.io/smart-mirror/

## Customization
- Update API keys and calendar IDs in the widget components as needed.
- Add or modify widgets in the `src/components` directory.

---

For more details, see the code in each widget component under `src/components/`.
