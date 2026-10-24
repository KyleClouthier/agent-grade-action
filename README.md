# AgentDecoy scan

A GitHub Action that scans your website after each deploy for what a hostile AI agent would find (leaked keys and files, open forms, missing headers, hidden instructions aimed at agents) and fails the build if the score drops below the one you set.

[AgentDecoy](https://simgen.dev/agentdecoy/) scores a site in three parts:

- **Found** (40 points): can Google (and Gemini), Bing (and Copilot), ChatGPT search, Claude and Perplexity read it, and do they get the same page people get? Plus llms.txt, sitemap, Bing verification, structured data and Open Graph.
- **Protected** (45 points): what AI attack agents look for first: secret files left public, keys in public scripts, login and signup forms with no bot guard, missing security headers.
- **Agent-safe** (15 points): text on the page aimed at AI agents, which can hijack the agents your visitors send.

The check only reads what any visitor can read. It never logs in, submits a form or tries to break anything.


## Part of a network

Every site that joins AgentDecoy makes every other site safer and smarter. Sites running [AgentDecoy Guard](https://simgen.dev/agentdecoy/guard/) share what their decoys catch: an address that uses a stolen secret on one site is refused on logins and forms across all of them within the hour, and what the network learns about AI search (which fixes really bring AI crawlers) goes into every grade this Action reads. Public totals: [Agent Attack Index](https://simgen.dev/agentdecoy/attack-index/).

## Use

```yaml
name: AgentDecoy
on:
  push:
    branches: [main]
  schedule:
    - cron: '0 9 * * 1'   # every Monday
jobs:
  grade:
    runs-on: ubuntu-latest
    steps:
      - uses: KyleClouthier/agentdecoy-action@v1
        with:
          site: example.com
          min-grade: B
```

Run it after your deploy job (`needs: deploy`) so it grades what just went live.

## Inputs

| Input | Required | Default | What it does |
| --- | --- | --- | --- |
| `site` | yes | | The site to grade |
| `min-grade` | no | `C` | Lowest grade that passes (A to F) |
| `api-key` | no | | An AgentDecoy Pro or Agency API key: 120 checks an hour instead of 10. Store it as a secret: `api-key: ${{ secrets.AGENT_GRADE_KEY }}` |

## Outputs

`grade` (the letter) and `score` (out of 100). The run summary shows the three parts and a link to the full result, and each lost point shows as a warning on the run page.

The same API also returns `platform` (WordPress, Shopify, Next.js and so on) and `fixes`, a numbered list of what to change and where to do it on that platform; the full result page shows both.

## Details and alerts

The public result says how many sensitive files or script secrets a site has, never which ones, so the check is no help to attackers. Site owners get the details with [AgentDecoy Pro](https://simgen.dev/agentdecoy/#pricing) after confirming ownership: weekly re-grades, an email when the grade drops, and every page scanned. 7 days free.

Built by [Kyle Clouthier](https://simgen.dev/), Clouthier Simulation Labs. MIT licence.

## The rest of AgentDecoy

- **Guard**: decoys that catch attack bots and AI agents on your own site and report them to your account, with every catch sorted as a script, a browser or an AI agent. A free [WordPress plugin](https://simgen.dev/agentdecoy/guard/), and a [JavaScript version](https://github.com/KyleClouthier/agentdecoy) for Cloudflare Workers, Next.js and Express.
- **The Agent Attack Index**: public monthly totals of what the decoys see across every Guard site: https://simgen.dev/agentdecoy/attack-index/
- **Plans**: every feature of Free, Pro, Agency and Guard side by side: https://simgen.dev/agentdecoy/plans/
