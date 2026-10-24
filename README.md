# Agent Grade check

A GitHub Action that grades your website after each deploy and fails the build if the grade drops below the one you set.

[Agent Grade](https://simgen.dev/agent-grade/) scores a site in three parts:

- **Found** (40 points): can Google, Bing, ChatGPT search, Claude and Perplexity read it, and do they get the same page people get?
- **Protected** (45 points): what AI attack agents look for first: secret files left public, keys in public scripts, login and signup forms with no bot guard, missing security headers.
- **Agent-safe** (15 points): text on the page aimed at AI agents, which can hijack the agents your visitors send.

The check only reads what any visitor can read. It never logs in, submits a form or tries to break anything.

## Use

```yaml
name: Agent Grade
on:
  push:
    branches: [main]
  schedule:
    - cron: '0 9 * * 1'   # every Monday
jobs:
  grade:
    runs-on: ubuntu-latest
    steps:
      - uses: KyleClouthier/agent-grade-action@v1
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
| `api-key` | no | | An Agent Grade Pro or Agency API key: 120 checks an hour instead of 10. Store it as a secret: `api-key: ${{ secrets.AGENT_GRADE_KEY }}` |

## Outputs

`grade` (the letter) and `score` (out of 100). The run summary shows the three parts and a link to the full result, and each lost point shows as a warning on the run page.

## Details and alerts

The public result says how many sensitive files or script secrets a site has, never which ones, so the check is no help to attackers. Site owners get the details with [Agent Grade Pro](https://simgen.dev/agent-grade/#pricing) after confirming ownership: weekly re-grades, an email when the grade drops, and every page scanned. 7 days free.

Built by [Kyle Clouthier](https://simgen.dev/), Clouthier Simulation Labs. MIT licence.
