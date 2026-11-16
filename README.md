# Top 20 Monthly Gainers and Triggers

A website that lists the 20 biggest NSE gainers of the calendar month (market cap of Rs 500 crore or more)
and explains what triggered each move. It updates itself every weekday at about 8 pm India time.

## How it works

- `scripts/update.py` downloads NSE daily price files, ranks all stocks, checks market cap on Screener.in,
  gathers announcements, quarterly numbers and news for the top 20, and asks Claude to write the trigger explanation.
- `.github/workflows/update.yml` runs that script on GitHub's servers on a schedule and saves the result.
- `docs/` is the website. GitHub Pages serves it. `docs/data/` holds one file per month, so past months stay available.

## One-time setup

1. Create a repository on GitHub and put these files in it (keep the folder structure, including `.github`).
2. **Turn on the website:** repository Settings > Pages > Source: "Deploy from a branch" > Branch: `main`, folder: `/docs` > Save.
   After a minute the site is live at `https://<your-username>.github.io/<repository-name>/`.
3. **Add the Claude API key:** create a key at https://console.anthropic.com/ , then in the repository go to
   Settings > Secrets and variables > Actions > New repository secret. Name: `ANTHROPIC_API_KEY`. Value: the key.
   Without the key the prices still update but the trigger write-ups do not.
4. **Run it once by hand:** Actions tab > "Update gainers" > Run workflow. Check that it finishes with a green tick.

## Settings you can change

In `.github/workflows/update.yml`, under `env:` of the "Run the update" step, you can add:

- `MIN_MCAP_CR: "500"`  minimum market cap in Rs crore
- `TOP_N: "20"`  number of stocks
- `CLAUDE_MODEL: "claude-sonnet-5-5"`  the model that writes the triggers (a smaller model costs less)

The schedule is the `cron` line in the same file (GitHub uses UTC time).

## Good to know

- The script only calls Claude for a stock when there is new evidence since the last run, which keeps the cost down.
- If NSE or Screener.in blocks GitHub's servers, the run fails and the site keeps showing the last good data.
  The Actions tab shows the error.
- A trigger is the most likely explanation, not proof of cause. This is research support, not investment advice.
