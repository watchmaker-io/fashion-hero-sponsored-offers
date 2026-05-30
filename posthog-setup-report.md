<wizard-report>
# PostHog post-wizard report

The wizard has completed a deep integration of PostHog analytics into this project. The app is a client-side React prototype ("fake door" test for FashionHero Sponsored Listings) bundled into a single HTML file with no build system. PostHog is initialised via a browser CDN snippet injected into the outer `<head>` of `index.html`, before the bundler's `DOMContentLoaded` listener fires. This ensures the `console.debug` interceptor is active throughout the entire app lifecycle — including after the bundler swaps the DOM with the decoded template content.

The integration works by overriding `console.debug` to intercept all calls with the first argument `'sponsored_listings_event'`. The app's existing `emitEvent()` helper is left untouched; PostHog capture calls piggyback on it transparently. The PostHog stub queues all calls until the real SDK loads asynchronously from CDN, so no events are lost even if they fire before the network request completes. On `waitlist_signup` — the key conversion event — `posthog.identify()` is called with the merchant ID to link the session to a person profile.

| Event | Description | File |
|---|---|---|
| `landing_page_view` | Fired when the landing page mounts; marks the top of the conversion funnel. | `index.html` (Landing component) |
| `cta_clicked` | Fired when the merchant clicks "Spróbuj już dziś", navigating to the configurator. | `index.html` (App `goConfig`) |
| `config_started` | Fired once when the configurator first mounts, with default campaign settings. | `index.html` (Configurator component) |
| `category_changed` | Fired each time the merchant switches product category, with `from`/`to` values. | `index.html` (Configurator `onCatChange`) |
| `bid_changed` | Fired when the CPC bid slider changes, with `proposedBid` and `previousBid`. | `index.html` (Configurator `onBidChange`) |
| `budget_set` | Fired when the daily budget changes, with `dailyBudget` and `previousBudget`. | `index.html` (Configurator `onBudgetChange`) |
| `waitlist_signup` | Fired when the merchant submits their campaign config — the primary conversion event. | `index.html` (Configurator `submit`) |

## Next steps

We've built some insights and a dashboard for you to keep an eye on user behaviour, based on the events we just instrumented:

- [Analytics basics dashboard](/dashboard/1649199)
- [Waitlist signup conversion funnel](/insights/vYDFChvn) — 3-step funnel: landing view → CTA click → signup
- [Waitlist signups over time](/insights/UQNmnkEs) — daily trend of completed signups
- [Preferred category at signup](/insights/fyp0knA5) — which product categories merchants choose
- [CPC bid selected at signup](/insights/qIxFfXij) — distribution of bid amounts at conversion
- [Daily budget chosen at signup](/insights/LR0wflb1) — distribution of daily budgets at conversion

### Agent skill

We've left an agent skill folder in your project. You can use this context for further agent development when using Claude Code. This will help ensure the model provides the most up-to-date approaches for integrating PostHog.

</wizard-report>
