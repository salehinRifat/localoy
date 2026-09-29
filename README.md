# Localoy
### Group mates
## S. M. Abdullah 1975
## Abu Salehin Rifat 1976
## Sadika Islam Barna 2007

---

# Localoy -  Hyperlocal Mutual Aid Platform

A neighborhood-based web application where people post needs ("I need a ladder") and offers ("I can drive someone to the hospital"), and the system matches them based on location, category, and trust. It replaces unstructured group chats and spreadsheets with a purpose-built tool that brings structure, safety, and accountability to community mutual aid.

Built on the **MERN stack**, the project demonstrates full-stack engineering through geo-matching, real-time coordination, a match state machine, and a reputation system.

---

## Table of Contents

- [Problem Statement](#problem-statement)
- [Objectives](#objectives)
- [Scope](#scope)
- [Target Users](#target-users)
- [Core User Journey](#core-user-journey)
- [Features](#features)
- [Unique Engineering Challenges](#unique-engineering-challenges)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Project Structure](#project-structure)
- [Contributing](#contributing)
- [License](#license)

---

## Problem Statement

In most neighborhoods:

- People have skills and spare time but don't know who nearby needs help.
- People need help but don't know who to ask — and asking strangers feels unsafe.
- Existing platforms (Facebook groups, WhatsApp) are unstructured: posts get buried, there's no matching, no accountability, and no trust signals.

Mutual aid — neighbors helping neighbors with no money involved — grew rapidly during COVID-19 and disasters, but it ran on spreadsheets and group chats. There is no good software for it. This project builds that software.

## Objectives

- Provide a structured platform for posting needs and offers.
- Match users based on geographic proximity and category.
- Enable real-time coordination between matched users.
- Build trust through a reputation and rating system.
- Keep the feed clean through automatic post expiry.
- Demonstrate a complete, deployable MERN application with real users.

## Scope

### In Scope
- User authentication and profiles
- Need/offer post creation and browsing
- Geo-based filtering and matching
- Match lifecycle management
- Real-time notifications and chat
- Reputation and rating system
- Admin moderation tools
- Emergency/panic alert system (see [Features](#features))
- Deployment to free-tier cloud services

### Out of Scope
- Monetary transactions (this is aid, not commerce)
- Native mobile apps (PWA instead)
- Identity verification via government ID
- Multi-language support (English only for MVP)

## Target Users

| Actor | Description |
|---|---|
| Neighbor (User) | Posts needs/offers, browses, expresses interest, chats, rates |
| Helper | A user who responds to a need |
| Requester | A user who posts a need |
| Admin/Moderator | Reviews reports, removes abusive posts, manages categories, responds to panic alerts |
| System | Sends notifications, expires old posts, computes reputation |

## Core User Journey

1. Ayesha signs up and sets her location (Dhaka, Dhanmondi).
2. She posts a need: *"Need a wheelchair for my father this Friday, 2 hours."*
   - Category: Medical equipment
   - Location: her area
   - Expires: Friday evening
3. Rahim, 1.2 km away, sees it in his feed (filtered by distance).
4. He clicks **"I can help"** → Ayesha receives a real-time notification.
5. They chat in-app and agree on time and place.
6. Match status → Accepted → Completed.
7. Both rate each other. Rahim's reputation increases.
8. Post auto-expires after Friday so the feed stays clean.

This loop — **post → match → complete → rate** — is the heart of the product. All other features add depth on top of it.

## Features

### MVP (Must Ship)
- Email/password authentication (JWT)
- Create need / offer posts (title, description, category, location)
- Browse feed with filters: category, distance, type
- "I can help" button → creates a Match
- Match status flow: pending → accepted → completed → cancelled
- Basic in-app notification
- Mark post as fulfilled

### Layer 2 (Depth)
- Socket.io real-time notifications and chat
- Reputation system with star ratings after completion
- Image uploads (Cloudinary)
- Admin dashboard for report handling and category management
- Post expiry via MongoDB TTL index

### Safety & Trust
- **Panic Button** — A one-tap emergency alert available during an active match, with selectable categories:
  - 🚨 Robbery
  - 🩺 Medical Emergency
  - 🔥 Fire
  - ⚠️ General SOS Alert *(placeholder — confirm exact category name/behavior)*
  
  Triggering it immediately sends the user's live location, match ID, and selected category to admins/moderators and the user's stored emergency contact, so a response can be routed appropriately instead of a single generic alert.
- **Check-in confirmation** — Both parties confirm the agreed meeting time/location; if neither marks "arrived" within a set window, a soft alert is triggered.
- **Emergency contact field** on user profile, used only by the panic button.
- **Report / Block user**, feeding into admin moderation tools.
- **Verified badge** — Email + phone verification only (no government ID, per scope).

### Stretch (Show Off)
- Smart matching — suggest nearby helpers for a need
- Trust score combining ratings, completed matches, account age, verification
- Email/SMS notifications (Nodemailer / Twilio)
- PWA support — installable on cheap phones
- Analytics dashboard — matches completed per area

## Unique Engineering Challenges

Most student projects are CRUD clones. This project has genuinely interesting engineering problems:

1. **Geo-matching** — Find open needs within a radius, sorted by relevance (MongoDB `$near` + `2dsphere`).
2. **Match lifecycle as a state machine** — Prevents invalid transitions (e.g., cannot complete a cancelled match).
3. **Reputation/trust scoring** — A weighted formula that can be justified, not just an average.
4. **Real-time coordination** — Socket.io for instant notifications, chat, and panic alerts.

Additional design considerations:
- Time-boxed content via TTL expiry keeps the feed fresh.
- Dual-role users (helper and requester) complicate auth and permissions.
- Panic alerts must be delivered with minimal latency and degrade gracefully if the socket connection drops (e.g., fallback to a REST call).
- Real pilot potential on campus or in a neighborhood.

## Tech Stack

| Layer | Choice | Rationale |
|---|---|---|
| Frontend | React (Vite) + Tailwind + React Router | Fast, componentized |
| State | Context API or Zustand | Auth and notifications |
| Backend | Node.js + Express | REST API |
| Real-time | Socket.io | Notifications, chat, panic alerts |
| Database | MongoDB Atlas + Mongoose | Geo queries, flexible schema, TTL |
| Auth | JWT + bcrypt | Standard and defensible |
| Uploads | Cloudinary | Free tier, easy integration |
| Maps | Leaflet + OpenStreetMap | Free, no API key required |
| Deployment | Vercel (FE) + Render (BE) + Atlas (DB) | All free tiers |

## Getting Started

### Prerequisites
- Node.js (v18+)
- npm or yarn
- A MongoDB Atlas account (or local MongoDB instance)
- A Cloudinary account (for image uploads)

### Installation

```bash
# Clone the repository
git clone https://github.com/<your-username>/hyperlocal-mutual-aid.git
cd hyperlocal-mutual-aid

# Install backend dependencies
cd server
npm install

# Install frontend dependencies
cd ../client
npm install
```

### Running Locally

```bash
# Start the backend (from /server)
npm run dev

# Start the frontend (from /client)
npm run dev
```

The frontend will typically run on `http://localhost:5173` and the backend on `http://localhost:5000` (adjust based on your config).

## Environment Variables

Create a `.env` file in `/server` with:

```env
PORT=5000
MONGO_URI=your_mongodb_atlas_connection_string
JWT_SECRET=your_jwt_secret
CLOUDINARY_CLOUD_NAME=your_cloud_name
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
```

## Project Structure

```
hyperlocal-mutual-aid/
├── client/           # React frontend (Vite)
│   ├── src/
│   └── ...
├── server/           # Express backend
│   ├── models/
│   ├── routes/
│   ├── controllers/
│   ├── sockets/      # Socket.io handlers (chat, notifications, panic alerts)
│   └── ...
└── README.md
```

## Contributing

This project is currently developed as part of an academic/portfolio project. Contributions, issues, and feature suggestions are welcome via pull requests.

## License

This project is licensed under the MIT License.
