# 📚 Reisekosten Pro Documentation

Welcome to the technical documentation for **Reisekosten Pro**.

## 📸 Interface Overview

The application features a modern, responsive design with full support for both light and dark themes.

### Light Mode
![Light Mode](light-screen.png)

### Dark Mode
![Dark Mode](dark-screen.png)

---

## 🏗️ Architecture

The application is a purely client-side Single Page Application (SPA) built entirely with HTML5, vanilla JavaScript, and styled with Tailwind CSS. It requires no backend server, database, or build tools.

### Key Components

- **`index.htm`**: The main and only file containing the HTML structure, inline CSS (for scrollbars and animations), and the JavaScript logic.
- **Tailwind CSS**: Loaded via CDN for rapid and responsive UI development.
- **Leaflet.js**: Used for rendering the interactive map.

## 💾 State Management & Local Storage

All user data is saved directly in the browser's `localStorage` to ensure persistence between sessions.

### Data Structures

The application state is managed via a central `state` object:

```javascript
let state = {
    rentalCost: 0,
    gasCost: 0,
    trips: [
        {
            id: '1',
            origin: 'Berlin',
            destination: 'Munich',
            distance: 584,
            client: 'Acme Corp',
            additionalCosts: 0
        }
    ]
};
```

- **`currentState`**: The active tracker state.
- **`savedTracks`**: An array of saved history states, allowing the user to save/load specific setups.
- **`addressBook` & `clientBook`**: Cached lists of previously entered locations and clients to populate autocomplete datalists.

## 🌍 Map & Geocoding Integration

The application uses open-source tools for geographic operations to avoid API keys and rate limits.

1. **Geocoding (Address to Coordinates)**:
   - Uses the [Photon API (Komoot)](https://photon.komoot.io/) as the primary geocoder.
   - Falls back to the [Nominatim API (OpenStreetMap)](https://nominatim.openstreetmap.org/) if Photon fails.
   - Coordinates are cached in `localStorage` (`geocodeCache`) to minimize redundant API requests.

2. **Routing & Distance (Coordinates to Route)**:
   - Uses the [OSRM (Open Source Routing Machine)](http://project-osrm.org/) API to fetch driving distances and GeoJSON routes.
   - The GeoJSON is then plotted on the Leaflet map with custom colors dynamically generated based on the client name.

## 🧮 Cost Calculation Logic

The total cost is distributed among clients based on the proportional distance traveled for each client.

1. **Total Costs**: The sum of `rentalCost` (fixed) and `gasCost` (calculated via consumption and gas price).
2. **Total Distance**: Sum of all trip distances.
3. **Cost Per Client**: `(Client Distance / Total Distance) * Total Costs`.
4. **Additional Costs**: Users can add specific extra costs (e.g., tolls, parking) to individual trips, which are added directly to the respective client's invoice.

## 🧾 Invoicing

Invoices are generated dynamically using a hidden modal (`#invoice-modal`).
When a user clicks "Rechnung" for a client, the app calculates the Net amount, adds 19% VAT (MwSt.), and populates the modal. The browser's native `window.print()` functionality is used to save the invoice as a PDF or print it.
