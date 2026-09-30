# SchoolSims Outbound Planner

A self-service tool for Janey (AE) to plan her own outbound week. It turns HubSpot company and contact exports into a weekly plan: how many companies to work per day (from pipeline math), which accounts and contacts, call times, scripts, and a downloadable .xlsx.

## How Janey uses it

Open the site each Monday and work through the four steps. Settings, exclusions and researched contacts stay in her browser between weeks.

## The four steps

1. **Pipeline target**: annual goal, close rate, current open pipeline, average deal, conversion rates, 90-day cycle and slow months (Dec, Jul, Aug by default). Sets companies per day.
2. **HubSpot export**: copy the two Breeze prompts to build the company and contact segments, export both to CSV, upload them.
3. **Review accounts**: uncheck any account she shouldn't call and pick a reason. Exclusions are saved in the browser, keyed by HubSpot Record ID.
4. **Weekly plan**: day-by-day schedule (call 1 in the morning window, call 2 + voicemail in the afternoon, email right after), scripts per contact, contact research, and the .xlsx download.

## Deploy to Vercel

1. Unzip, then either drag the folder into vercel.com/new or run `npx vercel` inside it. No build step or framework.
2. For live contact research, add an environment variable in Vercel > Project > Settings > Environment Variables:
   - `ANTHROPIC_API_KEY` = your Anthropic API key (required for research)
   - `ANTHROPIC_MODEL` = optional, defaults to `claude-sonnet-5-5`
3. Redeploy. The "Find more contacts" tab switches to **Web research** automatically when the key is present.

Each account researched is one API call with up to 6 web searches, billed to that key. The page contains HubSpot contact data once uploaded (in the browser only, nothing is stored server-side), but the research endpoint is public, so turn on Vercel's Deployment Protection (Settings > Deployment Protection) if the URL will be shared.

## Files

- `index.html`: the whole app (HTML, CSS, JS). Loads PapaParse and xlsx-js-style from jsDelivr.
- `api/mine.js`: serverless function that asks Claude with web search to find staff on the account's own website. Returns names, titles and source URLs; never guesses emails.
- `vercel.json`: gives the research function 60 seconds.

## Column matching

Columns are matched by name, so standard HubSpot exports work as-is. The upload card lists any expected columns it couldn't find. Contacts are linked to companies by Associated Company IDs, then company name, then email domain.
