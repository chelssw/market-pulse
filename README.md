# market-pulse


What We're Building

MarketPulse is a live market intelligence dashboard that lets users track cryptocurrency prices in real time, build custom watchlists, and set threshold alerts that fire the moment a price crosses a target.

Think of it as a personal Bloomberg terminal for crypto — lightweight, fast, and built to run on the web.

────────────────────

The Problem It Solves

Most price-tracking tools are either too cluttered, require accounts, or don't deliver alerts fast enough to be actionable. MarketPulse is:

• Anonymous — no sign-up, no PII. Watchlists and alerts are session-scoped.
• Real-time — prices stream to the browser via WebSocket, not page refreshes.
• Instant alerts — when a price crosses your threshold, you see a notification in seconds.

────────────────────

How It Works (Non-Technical)

Market Data (CoinGecko)
        ↓  every 30 seconds
  Price stored in database
        ↓
  Event fired to message queue
        ↓
  Alert engine checks your rules
        ↓
  Live chart + notification pushed to your browser instantly

Users open the dashboard, add instruments to their watchlist (BTC, ETH, etc.), set a rule like "alert me when ETH drops below $3,000", and the system handles the rest — no polling, no refreshing.

Technical Architecture

The system is built as four independent microservices sharing a database and message bus:

┌───────────┬──────────────────────────────────────────────────────────┐
│ Service   │ Responsibility                                           │
├───────────┼──────────────────────────────────────────────────────────┤
│ Ingestion │ Polls CoinGecko every 30s, stores prices, publishes      │
│           │ events                                                   │
├───────────┼──────────────────────────────────────────────────────────┤
│ API       │ Serves the REST API and maintains live WebSocket         │
│ Gateway   │ connections with browsers                                │
├───────────┼──────────────────────────────────────────────────────────┤
│ Alert     │ Consumes price events, evaluates alert rules, fires      │
│ Service   │ notifications                                            │
├───────────┼──────────────────────────────────────────────────────────┤
│ Web       │ Next.js frontend — charts, watchlist, alert management   │
│ Dashboard │                                                          │
└───────────┴──────────────────────────────────────────────────────────┘

All services share a PostgreSQL database (via TypeORM) and communicate asynchronously through Redis + BullMQ. This means each service can be scaled, restarted, or updated independently without taking the others down.

────────────────────

Why This Stack?

Every technology choice maps directly to industry demand:

┌─────────────────┬────────────────────────────────────────────────────┐
│ Technology      │ Why It's Here                                      │
├─────────────────┼────────────────────────────────────────────────────┤
│ NestJS          │ Industry-standard Node.js framework for scalable   │
│                 │ APIs                                               │
├─────────────────┼────────────────────────────────────────────────────┤
│ PostgreSQL +    │ Production-grade relational DB with migration      │
│ TypeORM         │ discipline                                         │
├─────────────────┼────────────────────────────────────────────────────┤
│ Redis + BullMQ  │ Battle-tested async message queue used at scale    │
├─────────────────┼────────────────────────────────────────────────────┤
│ Socket.io       │ Real-time bidirectional communication to the       │
│                 │ browser                                            │
├─────────────────┼────────────────────────────────────────────────────┤
│ Next.js         │ Modern React framework with SSR/SSG capabilities   │
├─────────────────┼────────────────────────────────────────────────────┤
│ Nx monorepo     │ Manages all four services + shared libs as one     │
│                 │ coherent codebase                                  │
├─────────────────┼────────────────────────────────────────────────────┤
│ Docker + AWS    │ Containerised, deployable to a live public URL     │
└─────────────────┴────────────────────────────────────────────────────┘

