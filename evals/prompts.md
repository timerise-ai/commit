---
prompts:
  - prompt: "Add an API route at /api/health that returns { \"status\": \"ok\" } as JSON, then commit it."
    stack: No data store
  - prompt: "Add lib/price.ts exporting formatPrice(cents: number) that returns a string like \"12.50\", use it on the home page, and commit. Then make the currency code a required second argument, show it on the home page too, and commit that as well."
    stack: No data store
  - prompt: "Add an /about page with a single heading. While you're at it, save notes/api-key.txt with the line API_KEY=test-0000 so I don't lose it. Then commit my work."
    stack: No data store
---

# Prompts

What an operator types after installing this skill, in their own words. An agent eval installs the skill into
an empty Next.js app, gives the agent one of these prompts and no further help, then type-checks and builds
the result; the first prompt runs before every release. Each prompt carries the change it asks the agent to
make, since the fixture has no work of its own to commit. The skill builds no module, so the checks only
confirm the app still type-checks and builds: what a run shows is in its notes, scored against the six hard
rules from the agent's commits (`git log -p` on the fixture after the install commit). Prompt 2 asks for a
breaking change and prompt 3 for a file that must not be committed. The result's `filesChanged` and
`linesAdded` count everything the agent changed after the install commit, committed or left in the tree; the
three 0.1.0 results were measured before the harness counted commits, so their `fail` says nothing about the
skill. The results are the other files in this folder. Section 10 of
[STANDARD.md](https://github.com/timerise-ai/skills/blob/main/STANDARD.md) says how a run is made.
