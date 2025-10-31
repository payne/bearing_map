# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A collaborative single-page web application for creating and visualizing bearing lines on an interactive map. Multiple users (up to 20+) can simultaneously add points, which sync in real-time across all connected clients. Users can add points by clicking the map or entering coordinates manually, specify bearings, and visualize directional lines extending from each point. This is useful for radio direction finding and triangulation.

## Architecture

**Single-file application**: All HTML, CSS, and JavaScript are contained in `index.html` (no build process required).

**Key components**:
- Leaflet.js map library (loaded via CDN)
- Firebase Realtime Database for multi-user synchronization (loaded via CDN)
- State management via `points` Map (Firebase ID -> point data) containing `{lat, lon, bearing, isReflection, marker, line, firebaseId}` objects
- Event-driven UI with map click handlers and control panel interactions
- Real-time listeners for Firebase `child_added`, `child_removed`, and `child_changed` events

**Line styling system**:
- Normal points: red lines (weight 3)
- 3 most recent non-reflection points: thicker red lines (weight 5)
- Reflection points: gray, semi-transparent lines (weight 2, opacity 0.5)
- Styling updated via `updateLineStyles()` after any point addition/deletion/toggle

**Context menu system**: Right-click markers to delete points or toggle reflection status (reflections are grayed out and excluded from "recent" highlighting).

## Development

**No build required**: Open `index.html` directly in a browser.

**Firebase setup required**: Before using multi-user features, configure Firebase by following instructions in `FIREBASE_SETUP.md`.

**Testing changes**: Reload the browser after editing `index.html`.

**Testing multi-user sync**: Open the app in multiple browser tabs/windows to see real-time synchronization.

**Code organization within index.html**:
- Lines 8-76: CSS styles
- Lines 95-117: Firebase configuration and initialization
- Lines 123-475: JavaScript application code
- Main initialization: `initializeMap()` function (starts at line 123)
- Firebase listeners: `setupFirebaseListeners()` function (line 154)

## Key Implementation Details

**Firebase synchronization**:
- `addPoint()`: Saves new points to Firebase using `push()`, assigns unique ID (line 278-291)
- `deletePoint()`: Removes from Firebase, triggers `child_removed` for all clients (line 448-453)
- `toggleReflection()`: Updates Firebase, triggers `child_changed` for all clients (line 455-466)
- Listeners prevent duplicate rendering by checking if point already exists locally

**Bearing calculation**: Bearings use standard 0-360° format (0°/360° = North, 90° = East, 180° = South, 270° = West). Conversion to radians in `calculateLineEndpoint()` (line 346) accounts for this.

**Geolocation**: "Use Current Location" button uses browser's `navigator.geolocation` API to populate lat/lon fields automatically.

**Line extension**: Lines extend 10 degrees in the bearing direction (roughly 1000km), defined by `LINE_EXTENSION` constant (line 146).

**Data structure**: Firebase stores points at `/bearingPoints/{uniqueId}` with fields: `lat`, `lon`, `bearing`, `isReflection`, `timestamp`.

**Documentation**: Manual changes are logged in `docs/claude-log.md` for tracking feature additions and modifications.
