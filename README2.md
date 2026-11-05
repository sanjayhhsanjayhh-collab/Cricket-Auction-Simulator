# IPL Cricket Auction Simulator

A real-time, fan-made IPL auction simulator where users create private
rooms, take ownership of IPL franchises, bid on verified IPL players
using virtual currency, build squads, and receive AI-powered team
analysis.

> **Disclaimer:** This is an unofficial fan-made simulation. It is not
> affiliated with, endorsed by, or sponsored by the BCCI, IPL, or any
> franchise. It does not support real-money betting, gambling, wagering,
> cash prizes, or cash-out.

## Features

-   User registration and login
-   Create private auction rooms with unique room codes
-   Join rooms and claim one of the 10 IPL franchises
-   Room creator can also participate as a team owner
-   Live bidding synchronized across connected users
-   Configurable auction timer: 5, 10, or 15 seconds
-   Custom virtual bidding rules:
    -   Starting price: ₹0 crore
    -   Bid increment: ₹0.25 crore
    -   Timer resets after a valid bid
    -   A bid in the final second restarts the full selected timer
-   Automatic SOLD/UNSOLD decisions
-   Automatic squad assignment and virtual-purse deduction
-   Configurable squad-size and player-role requirements
-   Verified real-player data with season-aware player pools
-   AI team ratings, strengths, weaknesses, and suggested playing XI
-   Optional AI-generated tournament simulation
-   Auction summaries and downloadable PDF reports

## IPL Franchises

The simulator supports these 10 franchises:

1.  Chennai Super Kings (CSK)
2.  Mumbai Indians (MI)
3.  Royal Challengers Bengaluru (RCB)
4.  Kolkata Knight Riders (KKR)
5.  Sunrisers Hyderabad (SRH)
6.  Rajasthan Royals (RR)
7.  Delhi Capitals (DC)
8.  Punjab Kings (PBKS)
9.  Gujarat Titans (GT)
10. Lucknow Super Giants (LSG)

Each room should allow one owner per franchise.

## Technology Stack

The planned stack is:

  Layer               Technology
  ------------------- ---------------------------------
  Frontend            React, TypeScript, Tailwind CSS
  Backend             Python, FastAPI
  Database            PostgreSQL / Supabase
  Authentication      Supabase Auth or equivalent
  Real-time updates   WebSockets or Supabase Realtime
  AI analysis         Configured AI provider
  Reports             PDF generation library

Adapt these choices to the implementation in this repository. Do not
assume a service is configured until its credentials and integration are
in place.

## Project Structure

A suggested structure:

``` text
ipl-auction-simulator/
├── frontend/
│   ├── src/
│   ├── public/
│   ├── package.json
│   └── .env.example
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── models/
│   │   ├── schemas/
│   │   ├── services/
│   │   └── main.py
│   ├── tests/
│   ├── requirements.txt
│   └── .env.example
├── database/
│   └── migrations/
├── docs/
└── README.md
```

The actual project may use a different structure; update this section to
match the repository.

## Getting Started

### Prerequisites

Install the following before running the project:

-   Node.js (LTS recommended)
-   npm, pnpm, or yarn
-   Python 3.11 or a version supported by the backend dependencies
-   PostgreSQL, or a configured Supabase project
-   Git

### 1. Clone the repository

``` bash
git clone <YOUR_REPOSITORY_URL>
cd ipl-auction-simulator
```

### 2. Configure the backend

``` bash
cd backend
python -m venv .venv
```

Activate the virtual environment:

**macOS / Linux**

``` bash
source .venv/bin/activate
```

**Windows PowerShell**

``` powershell
.venv\Scripts\Activate.ps1
```

Install dependencies:

``` bash
pip install -r requirements.txt
```

Copy the example environment file and fill in the values:

``` bash
cp .env.example .env
```

On Windows, copy `.env.example` to `.env` manually if `cp` is
unavailable.

Example backend environment variables:

``` env
APP_ENV=development
APP_SECRET_KEY=replace-with-a-long-random-secret
DATABASE_URL=postgresql://USER:PASSWORD@HOST:5432/DATABASE
CORS_ORIGINS=http://localhost:5173
AI_API_KEY=
AI_MODEL=
```

Do not commit `.env` files or real credentials to version control.

Start the backend (adjust the import path if your application entry
point differs):

``` bash
uvicorn app.main:app --reload
```

The API should be available at:

-   API: `http://localhost:8000`
-   Interactive API docs: `http://localhost:8000/docs`

### 3. Configure the frontend

Open a second terminal:

``` bash
cd frontend
npm install
```

Copy `.env.example` to `.env` and configure the frontend:

``` env
VITE_API_BASE_URL=http://localhost:8000
```

Add only public client-side configuration to frontend environment
variables. Never put database passwords, service-role keys, or private
AI API keys in frontend code.

Start the frontend:

``` bash
npm run dev
```

Open the local URL printed by the development server, commonly
`http://localhost:5173`.

## Auction Rules

The default custom simulator rules are:

  Setting                                                     Default
  --------------------------------- ---------------------------------
  Player starting price                                      ₹0 crore
  Bid increment                                           ₹0.25 crore
  Available timer choices                           5, 10, 15 seconds
  Timer after a valid bid                  Reset to selected duration
  Bid in final second                 Reset to full selected duration
  Sale with a valid bid at expiry                                SOLD
  Expiry without a valid bid                                   UNSOLD

The host chooses the timer when creating the room. The host may also be
a team owner and bid for their own team.

### Server-side validation

The backend must be authoritative for every bid. It must verify that:

-   The auction room is active.
-   The player is currently available for bidding.
-   The bidder is authenticated and owns the selected team.
-   The team has enough remaining virtual purse.
-   The bid follows the configured increment.
-   The owner is not bidding against their own team's current highest
    bid.
-   The bid is accepted before the server-side timer expires.

Use database transactions or equivalent atomic operations to prevent two
simultaneous bids from creating inconsistent results. Clients must never
decide the winning bid, sale price, purse deduction, or SOLD/UNSOLD
status.

## Player Data

Only use real, verifiable IPL players.

Player records should include, where available:

-   Full name and stable player identifier
-   Nationality
-   Date of birth or verified age
-   Batting style and bowling style
-   Playing role
-   IPL seasons and franchises
-   Verified career statistics
-   Source and last-updated timestamp
-   Image source and usage/licensing information

Keep player pools season-specific. Distinguish current-season players
from historical players, retained players, released players, and players
selected for custom auctions.

Do not fabricate players, player statistics, player histories,
photographs, or performance ratings. If a field cannot be verified, mark
it as unavailable rather than guessing. Respect source terms and image
licensing.

## Squad Configuration

Suggested defaults:

-   Minimum squad size: 15
-   Maximum squad size: 25
-   Player-role requirements: configurable by room host
-   Overseas-player limit: configurable by room host

These are simulator settings, not a statement of official IPL squad
regulations. A room should display whether each team meets its
configured requirements.

## AI Team Analysis

After the auction, the AI analysis module can evaluate:

-   Batting strength
-   Bowling strength
-   All-rounder options
-   Wicketkeeping options
-   Squad depth
-   Overseas-player balance
-   Recent player performance
-   Overall squad balance

Possible outputs:

-   Overall rating out of 100
-   Category ratings
-   Team strengths and weaknesses
-   Suggested playing XI
-   Improvement suggestions
-   Ranking of the 10 squads

Ratings should be based on documented inputs and explain their
methodology. Label estimates and uncertainty clearly. AI rankings are
opinions generated from available data, not guaranteed outcomes.

## Tournament Simulation

The optional tournament simulator may generate a fictional schedule,
match scores, points table, playoffs, and champion.

Every generated fixture and result must be clearly labelled
**AI-generated simulation**. Never present simulated results as actual
IPL fixtures or match results.

## Suggested Database Entities

A relational schema may include:

-   `users`
-   `auction_rooms`
-   `room_members`
-   `teams`
-   `team_owners`
-   `players`
-   `player_seasons`
-   `player_statistics`
-   `auction_settings`
-   `auction_lots`
-   `bids`
-   `player_sales`
-   `team_squads`
-   `team_purse_transactions`
-   `team_analysis`
-   `tournament_simulations`
-   `simulation_matches`

Use foreign keys, indexes, uniqueness constraints, and database
transactions. Store a durable bid and purse transaction history for
auditability.

## Testing Checklist

Before deploying, test at least the following:

-   A user can create a room and join it.
-   The host can also own a team.
-   Each team can have only one owner per room.
-   Two users cannot claim the same team.
-   Only an authenticated team owner can bid for that team.
-   Bids follow the configured increment.
-   Teams cannot exceed their remaining virtual purse.
-   A team cannot bid against its own current highest bid.
-   Simultaneous bids are processed consistently.
-   The timer resets correctly after a valid bid.
-   A player is sold only once.
-   A SOLD player is added to the winning squad exactly once.
-   The winning bid is deducted exactly once.
-   An expired lot with no valid bid becomes UNSOLD.
-   All connected clients receive consistent auction updates.
-   Squad qualification checks use the room's configured rules.
-   AI analysis clearly identifies missing or uncertain player data.
-   Tournament outputs are labelled as simulations.

## Security and Privacy

-   Keep secrets and private API keys on the backend.
-   Validate permissions on every protected API and WebSocket action.
-   Apply rate limits to authentication and bidding endpoints.
-   Validate and sanitize user input.
-   Use secure session handling and HTTPS in production.
-   Restrict database access with appropriate policies.
-   Do not trust client-side purse balances or bid values.
-   Avoid exposing private room information to unauthorized users.
-   Provide a way to report abuse and remove inappropriate room content.

## Deployment

Deploy the frontend and backend using hosting providers that support
your chosen stack. Configure:

-   Production database and migrations
-   Backend environment variables
-   Frontend API URL
-   CORS allowlist
-   Authentication redirect URLs
-   WebSocket support
-   HTTPS
-   Logging and error monitoring
-   Database backups

Run the test suite and verify real-time bidding with multiple
independent browser sessions before opening the application to users.

## Contributing

1.  Create a feature branch.
2.  Make focused changes.
3.  Add or update tests.
4.  Run formatting, linting, and tests.
5.  Submit a pull request describing the change.

## License

Choose and add a license before publishing the repository. Confirm that
you have permission to use any third-party player photographs, team
logos, trademarks, statistics feeds, or other protected material. The
project name and disclaimer do not grant rights to those assets.
