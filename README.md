# 🌍 DisasterReady

### Gamified Disaster Preparedness and Emergency Response Training

[![Open DisasterReady](https://img.shields.io/badge/Launch-DisasterReady-7557e8?style=for-the-badge)](https://disasterreadyweb.onrender.com/)

**Live website:** [https://disasterreadyweb.onrender.com/](https://disasterreadyweb.onrender.com/)

DisasterReady is an interactive disaster-preparedness training platform. Trainees make timed decisions in Unity simulations, receive server-evaluated scores, and track progress on a leaderboard.

## Features

- Account registration and sign-in
- Dashboard with saved points, level, and leaderboard standing
- Earthquake, fire, and flood safety scenarios
- Unity WebGL simulations embedded in the React website
- Server-side answer evaluation and point persistence
- Leaderboard and attempt history APIs
- Optional Gemini-powered feedback integration
- Audio cleanup when leaving or switching simulations

## Technology

| Area | Stack |
| --- | --- |
| Frontend | React 18, Vite, React Router, Axios |
| Backend | Node.js, Express 5 |
| Database | PostgreSQL, Prisma ORM 7 |
| Authentication | JWT, bcrypt |
| Simulations | Unity 6, WebGL, WebAssembly |
| AI feedback | Google Gemini API |

## Repository layout

```text
Assets/       Unity project and simulation source
backend/      Express API, Prisma schema, migrations, and seed data
frontend/     React portal and deployed Unity WebGL build
```

## Run locally

### Requirements

- Node.js 18 or newer and npm
- PostgreSQL
- Unity 6 only if you need to edit or rebuild the simulations

### 1. Get the code

```bash
git clone https://github.com/iswekshasingh/DisasterReady.git
cd DisasterReady
```

### 2. Configure and start the backend

```bash
cd backend
npm install
```

Create `backend/.env`:

```env
PORT=4000
NODE_ENV=development
DATABASE_URL="postgresql://YOUR_USER:YOUR_PASSWORD@localhost:5432/disasterready?schema=public"
JWT_SECRET="replace-with-a-long-random-secret"
GEMINI_API_KEY="optional-gemini-api-key"
```

Apply the schema, add the simulation records, and start the API:

```bash
npx prisma migrate deploy
npm run prisma:seed
npm start
```

The API runs at `http://localhost:4000`. Check `http://localhost:4000/api/health` for its health response.

### 3. Start the frontend

In another terminal:

```bash
cd frontend
npm install
```

Create `frontend/.env`:

```env
VITE_API_BASE_URL=http://localhost:4000
```

Then run:

```bash
npm run dev
```

Open `http://localhost:5173`.

Never commit `.env` files or production secrets. For AI feedback, create a Gemini API key in Google AI Studio and set it as `GEMINI_API_KEY` in the backend environment.

## Deploy on Render

The live website and API are separate Render services:

- **Website:** [https://disasterreadyweb.onrender.com/](https://disasterreadyweb.onrender.com/)
- **Backend API:** [https://disasterready-ig16.onrender.com/](https://disasterready-ig16.onrender.com/)

The frontend is a Static Site configured with:

| Setting | Value |
| --- | --- |
| Branch | `react` |
| Root directory | `frontend` |
| Build command | `npm install && npm run build` |
| Publish directory | `dist` |
| Environment variable | `VITE_API_BASE_URL=https://disasterready-ig16.onrender.com` |

The backend is a Node web service configured with:

| Setting | Value |
| --- | --- |
| Root directory | `backend` |
| Build command | `npm install && npx prisma generate && npx prisma migrate deploy && npm run prisma:seed` |
| Start command | `npm start` |

Set these backend environment variables in Render:

- `DATABASE_URL`: the **Internal Database URL** from the Render PostgreSQL database
- `JWT_SECRET`: a long, private random value
- `NODE_ENV`: `production`
- `GEMINI_API_KEY`: optional; required only for Gemini feedback

The Render Postgres free tier expires 30 days after creation. Upgrade it or export the data before expiry if the project needs to remain available longer.

## Main pages

| Path | Purpose |
| --- | --- |
| `/` | Landing page |
| `/signup` | Create an account |
| `/login` | Sign in |
| `/dashboard` | Points, level, and progress |
| `/leaderboard` | Top trainees |
| `/play/earthquake` | Unity simulation player |

## API endpoints

| Method | Endpoint | Purpose |
| --- | --- | --- |
| `GET` | `/api/health` | API health check |
| `POST` | `/api/auth/register` | Register an account |
| `POST` | `/api/auth/login` | Sign in and receive a JWT |
| `GET` | `/api/auth/me` | Get the signed-in profile and points |
| `GET` | `/api/scenarios` | List published scenarios |
| `GET` | `/api/scenarios/:id` | Get scenario details |
| `POST` | `/api/attempts` | Submit answers and save a score |
| `GET` | `/api/attempts` | View attempt history |
| `GET` | `/api/leaderboard` | View leaderboard |

Protected endpoints expect `Authorization: Bearer <token>`.

## Rebuild the Unity WebGL player

The browser build is stored in `frontend/public/unity/earthquake/Build/`. If Unity scripts or assets change, open the Unity project at the repository root and choose **DisasterReady → Build WebGL for React Portal**. Commit the resulting build files with the frontend changes.

## Team

DisasterReady is a collaborative academic project covering frontend, backend/database, and Unity simulation development.
