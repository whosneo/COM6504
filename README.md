# COM6504 — Ins

A progressive web application (PWA) for sharing location-aware stories with photos. Built for the University of Sheffield **COM6504** module, with online MongoDB persistence and offline support via Service Worker and IndexedDB.

## Features

- **User accounts** — register / login with Passport (local strategy) and session cookies
- **Stories** — create posts with text, camera snapshots, and geolocation
- **Events** — associate stories with events
- **Search** — full-text search over story content (MongoDB text index) and offline search
- **Offline-first** — Service Worker caches the app shell; IndexedDB stores stories for offline use and sync
- **PWA** — Web App Manifest, installable icons, standalone display
- **Map** — view story locations on a map page
- **Avatar upload** — profile image upload via Multer

## Tech stack

| Layer | Technology |
|-------|------------|
| Runtime | Node.js |
| Server | Express 4 |
| Views | EJS |
| Database | MongoDB + Mongoose |
| Auth | Passport, express-session, pbkdf2 password hashing |
| Client | jQuery, Bootstrap, custom scripts |
| Offline | Service Worker, IndexedDB (`idb`) |
| Uploads | Multer |
| Transport | HTTPS (self-signed certs in `private_access/`) |

## Project structure

```
.
├── app.js                 # Express app setup
├── bin/www                # HTTPS server entry (default port 443)
├── controllers/           # Route handlers (auth, stories, users, …)
├── databases/ins.js       # MongoDB connection
├── middlewares/passport.js
├── models/                # Mongoose schemas (User, Story, Event, Comment)
├── routes/                # Express routers
├── views/                 # EJS templates
├── public/                # Static assets, service worker, client scripts
│   ├── service-worker.js
│   ├── manifest.json
│   └── scripts/
└── private_access/        # TLS key and certificate (local dev)
```

## Prerequisites

- **Node.js** (LTS recommended)
- **MongoDB** running locally (default URI: `mongodb://localhost:27017/Ins`)
- Ability to bind **port 443** (or set `PORT`; on Linux this may need elevated privileges)

## Setup

```bash
# Clone
git clone git@github.com:whosneo/COM6504.git
cd COM6504

# Install dependencies
npm install

# Ensure MongoDB is running
# e.g. systemctl start mongod   or   mongod --dbpath /path/to/data

# Start the HTTPS server (default port 443)
npm start
```

Then open:

```text
https://localhost/
```

Your browser will warn about the self-signed certificate in `private_access/`; accept it for local development.

### Custom port

```bash
PORT=8443 npm start
# → https://localhost:8443/
```

## Main routes

| Path | Description |
|------|-------------|
| `/` | Home |
| `/register`, `/login`, `/logout` | Authentication |
| `/stories` | Story list / create |
| `/events` | Events |
| `/comments` | Comments |
| `/map/:lat/:lon` | Map for a location |
| `/upload_avatar` | Avatar upload (authenticated) |

## Offline behaviour

1. On first visit (online), the Service Worker caches the app shell listed in `public/service-worker.js`.
2. Stories can be written to **IndexedDB** when offline.
3. When back online, data can be reconciled with **MongoDB** via the API.

## Configuration notes

- MongoDB URL is set in `databases/ins.js` (`mongodb://localhost:27017/Ins`).
- Session secret and `cookie.secure: true` are configured in `app.js` (HTTPS required for cookies).
- TLS files: `private_access/ca.key` and `private_access/ca.crt`.

## License

Coursework project for COM6504. Not published as an open-source product; reuse only as permitted by the module rules.
