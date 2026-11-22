# Jade Setup Guide

Jade is a personal headhunter. A job search is mostly unpaid admin: finding roles that fit, reading up on each company, rewriting the same resume, filling in the same forms, and trying to remember what you said in the last interview. Jade does that part. You decide where to apply and what to say yes to. This guide sets her up on Hermes Agent, a free and open source agent from Nous Research that runs on your own machine.

The software is free. The model behind it is not always free, and the real work is telling her the truth about you once, clearly, so she never has to guess.

**Checked on 2026-11-21.** Commands and limits change. Sources are at the bottom.

**How this guide was made:** every command below was copied from the Hermes Agent documentation on that date. We have not test-run them on a fresh machine. If a step does not behave the way it says, see "If a step breaks" near the end: your AI can patch it for you in a minute.

## What Jade does

- Keeps one tracker of every role that fits you and every conversation in motion.
- Researches the company before you apply anywhere.
- Tailors each application to the role, from your real resume and your real answers.
- Fills in the application and holds it at the Submit button until you say go.
- Asks once when she does not know something about you, writes the answer down, and never guesses.
- Tracks every topic that comes up in each interview, and before the next round tells you what to reinforce and what is still open.
- Does not accept, decline, negotiate or schedule anything on her own. You decide.

## What you need before you start

| You need | Why | Cost | Checked |
|---|---|---|---|
| A computer that stays on: macOS, Linux, Windows or WSL2 | The morning role scan only runs while Hermes' gateway is running | Yours already | 2026-11-21 |
| Git and curl | The installer uses them | Free | 2026-11-21 |
| Hermes Agent | Runs Jade | Free, MIT licence | 2026-11-21 |
| A model with at least 64,000 tokens of context | Hermes rejects smaller models at startup | Depends on the provider you pick. Not measured here | 2026-11-21 |
| Your resume as plain text | Every tailored application starts from it | Yours already | 2026-11-21 |
| Telegram (optional) | To get the morning role scan on your phone | Free | 2026-11-21 |

On a brand new install the setup wizard may offer a "Free, start now" choice that needs no account. The Hermes docs say it is shown only when it is available, so do not plan around it.

## The honest timeline

| When | What | Your time |
|---|---|---|
| Day 1 | Install Hermes and get one normal chat working | The Hermes docs say under 5 minutes. Allow 30 |
| Day 1 | Create Jade and give her a desk folder | 20 minutes |
| Day 1 to 2 | Write down what you want and the facts about you | 1 to 2 hours. This is the part that decides everything |
| Day 2 | Turn on web search, the browser and the morning scan | 30 minutes |
| Week 1 | Read every application before it goes out, and answer her questions | 15 minutes a day |

The slow part is not the install. It is the facts. A headhunter who has to guess your salary floor, your notice period or whether you will relocate will guess wrong on a form with your name on it. Answer each of those once and she never asks again.

Two limits to know before you start:

- Some job sites block automated browsers, and some forbid automated applications in their terms. Check the rules of the sites you use. When Jade hits a CAPTCHA or a login wall she is told to stop and tell you.
- Volume is not the goal. Ten applications that fit beat a hundred that do not.

## Setup, in this order

### 1. Install Hermes and get one chat working

Do not add anything else until a plain conversation works. The Hermes quickstart is blunt about this, and it is right.

On macOS and Windows the Hermes docs recommend the Hermes Desktop installer from https://hermes-agent.nousresearch.com/. For a command line only install on macOS, Linux or WSL2:

```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

Reload your shell, pick a model, and start a chat:

```bash
source ~/.zshrc
hermes setup
hermes
```

You should see: a welcome banner naming your model, and a reply to a simple question. If replies are empty or broken, run `hermes model` again and confirm the provider, the model and the login. If anything else looks off, run `hermes doctor`.

### 2. Create Jade

Every agent in Hermes gets its own profile: its own settings, memory and identity. Never point two agents at the same profile.

```bash
hermes profile create jade --description "Personal headhunter. Tracks roles that fit, researches companies, tailors and prepares applications, preps interviews. Asks instead of guessing. The owner decides."
```

Creating the profile also creates a `jade` command. Check it:

```bash
jade chat
```

You should see: the same kind of chat as step 1, now running as the `jade` profile.

### 3. Give Jade her identity

In Hermes, who an agent is lives in a file called `SOUL.md` inside that agent's home. For a profile named `jade` that is:

```text
~/.hermes/profiles/jade/SOUL.md
```

Hermes creates a starter file there. Replace its contents with the text in "Jade's instructions" below. Keep it about who she is and how she talks. What you want and the facts about you go in different files, in the next step.

You should see: after starting a new `jade chat`, she introduces herself as your headhunter when asked who she is.

### 4. Build her desk: what you want, and the facts about you

This is the step everybody rushes, and it is the one that makes Jade useful.

Make a folder for her to work from:

```bash
mkdir -p ~/jade-desk/applications ~/jade-desk/interviews
```

Put four files in it. Hermes loads `AGENTS.md` from the folder you start a chat in, and Jade reads and updates the others as she works.

`~/jade-desk/AGENTS.md`, the rules and the target:

```text
# What I am looking for

- Role: {the job titles you want}
- Level: {seniority}
- Industries: {the ones you want, and the ones you will not do}
- Location: {remote, hybrid or on site, and where}
- Pay: {your floor, and how you want pay questions answered on forms}
- Deal breakers: {anything that rules a role out}

# How Jade works this desk

- tracker.md holds every role that fits and every conversation in motion. Update it after every action.
- resume.md is my real resume. Tailor from it. Never add a job, a number or a skill that is not in it.
- FACTS.md holds answers about me. Before any form, read it. If a form asks something that is not in FACTS.md, stop, ask me once, and write my answer there.
- Research the company before preparing any application. Save the notes in applications/{company}/research.md.
- Save each tailored application in applications/{company}/.
- Fill in the form, then stop at the Submit button and show me what you entered. Submit only when I say so for that role.
- After every interview, save the topics that came up in interviews/{company}.md. Before the next round, compare them with what was already covered and tell me what to reinforce and what is still open.
- If a site shows a CAPTCHA, a login wall or a terms warning, stop and tell me.
```

`~/jade-desk/FACTS.md`, the answers she must never guess. Start with these and let her add to it:

```text
# Facts about me (asked once, never guessed)

- Legal name on applications: {answer}
- Work authorization: {answer}
- Notice period or start date: {answer}
- Willing to relocate: {answer}
- Willing to travel: {answer}
- How to answer pay questions: {answer}
- Contact email and phone for applications: {answer}
- Profile link to use: {answer}
```

`~/jade-desk/resume.md`: paste your resume as plain text.

`~/jade-desk/tracker.md`: start it empty with one header line.

```text
| Company | Role | Link | Stage | Last action | Next step | Open questions |
|---|---|---|---|---|---|---|
```

You should see: when you run `jade chat` from inside `~/jade-desk` and ask what you are looking for, she answers from `AGENTS.md`.

### 5. Turn on web search and the browser

Jade needs two toolsets. Web search finds roles and reads company pages. The browser fills in forms. Open the tools menu for her profile and confirm both are on:

```bash
hermes -p jade tools
```

Hermes ships with its own browser and needs no setup for it. Job sites usually need you signed in, and that part is your call. The Hermes docs describe two ways: attaching Jade to a browser you already have open, from inside a chat:

```text
/browser connect
```

or a managed copy of your everyday browser profile, which is opt-in. Read the Browser page in the Hermes docs before you turn either on, and do not paste passwords into a chat.

Start a new `jade chat` after changing her tools. An old conversation keeps its old permissions.

You should see: when you ask Jade to look up a company's careers page, she returns real roles with links.

### 6. Schedule the morning role scan

Hermes has a built-in scheduler. Give the job Jade's folder so she reads your rules when she runs. The folder must be written as a full path.

```bash
hermes -p jade cron create "weekdays at 8am" \
  "Morning role scan. Search for new roles that match AGENTS.md. For each one that fits, add a row to tracker.md with the link and one line on why it fits. Do not apply to anything. Then list every conversation in tracker.md that needs a reply or a next step from me today. Keep it under 200 words." \
  --workdir /Users/you/jade-desk \
  --name "Role scan"
```

Schedules run from a gateway, and each profile has its own. Start Jade's, and check that her scheduler is alive:

```bash
jade gateway start
hermes -p jade cron status
```

`jade gateway install` keeps it running after a restart.

By default the scan is saved on your machine. To get it on your phone, connect Telegram first with `hermes gateway setup`, then create the job with `--deliver telegram` added.

Two things to check on your machine:

- Whether a scheduled run has web search with the default settings. Run `hermes -p jade tools` to see which toolsets are on for each place Jade runs.
- Whether your times come out in your own timezone. Hermes reads the schedule in the profile's `timezone:` setting. Add `--timezone America/Los_Angeles` (or your zone) to pin it.

You should see: new rows in `tracker.md` the next weekday morning.

### 7. Run your first application, by hand

Do the first one with her, start to finish, before you trust the routine. From inside `~/jade-desk`, run `jade chat` and give her one real role.

You should see: research notes saved first, then a tailored application saved in its folder, then at least one question for you if the form asks something that is not in `FACTS.md`, then a stop at the Submit button.

## Jade's instructions

Paste this into `~/.hermes/profiles/jade/SOUL.md`. Change the parts in braces.

```text
# Who you are

You are Jade, a personal headhunter who works for one person: {your name}. You are not a recruiter for companies. Your only client is the candidate.

Your job is to take the admin out of a job search: track every role that fits and every conversation in motion, research each company, tailor and prepare each application, and prepare your client for every interview.

# How you work

- You research the company before you prepare any application.
- You tailor from the client's real resume. You never add a job, a number, a skill or a date that is not in it.
- If you do not know a fact about the client, you ask once, save the answer, and never guess. A wrong answer on a form is worse than a late one.
- You fill in an application and stop before submitting. You submit only when the client approves that role.
- You keep the tracker current. If it is not in the tracker, it did not happen.
- After each interview you record every topic that came up. Before the next round you compare it with what was already covered, and you tell the client what to reinforce and what is still open.
- You never accept, decline, negotiate or schedule on your own. You research, apply and prep. The client decides.
- If a site blocks you, asks for a login, or shows terms that forbid automated use, you stop and say so.

# How you talk

- Polished, quick and warm. You sound like someone who already knows the hiring manager.
- Lead with what needs a decision today.
- One line per role: company, role, stage, next step.
- When you ask a question, ask exactly one, and say which form or interview needs it.
```

This persona was written from Jade's job description for this guide. It is not a copy of the instructions she runs on in our own team.

## First test

Run `jade chat` from inside `~/jade-desk` and send her this:

```text
Find three open roles that match what I am looking for. For each one, give me the company, the role, the link and one line on why it fits. Add them to tracker.md. Do not apply to anything. Then tell me which facts about me you would need before applying to the first one.
```

A good answer gives three real roles with links, adds three rows to `tracker.md`, applies to nothing, and lists the facts that are missing from `FACTS.md` instead of filling them in.

If she invents an answer about you, tighten the "ask once" line in `SOUL.md` and add the missing fact to `FACTS.md`.

## When it goes wrong

| What you see | Why | Fix |
|---|---|---|
| Empty or broken replies | Model or login is wrong | `hermes model`, then a plain chat before anything else |
| She does not know what you want | The chat was not started in `~/jade-desk` | Start `jade chat` from that folder |
| She cannot search or open pages | Web or browser toolset is off, or the chat is older than the change | `hermes -p jade tools`, then a new chat |
| A job site shows a CAPTCHA or blocks her | The site does not want automated browsers | Apply to that one yourself. She can still do the research and the tailoring |
| She filled in something that is not true | The fact was missing and she guessed | Add it to `FACTS.md`, tighten the "ask once" line, and check the form before it goes out |
| No morning scan | Jade's gateway is not running, or the job is missing its folder | `hermes -p jade cron status`, `jade gateway start`, and recreate the job with `--workdir` |
| Anything else | | `hermes doctor` |

## If a step breaks

Hermes changes fast, so a command here can drift from what your version expects. You do not need us to fix it. Paste this into Hermes itself, or into Claude or ChatGPT, with the error underneath:

```text
I am following a setup guide for a personal headhunter agent on Hermes Agent. This step failed. Here is the step, the exact command I ran, and the full error. Check the current Hermes Agent docs at https://hermes-agent.nousresearch.com/docs, tell me what changed, and give me the corrected command.
```

`hermes doctor` is worth running first. It catches most setup problems on its own.

## What it costs to run

- Hermes Agent: free, MIT licence.
- The model: billed by whichever provider you choose in `hermes model`. Every search, every tailored application and every morning scan is model usage. Not measured here, so no number is given.
- Web search: Hermes supports several search providers and the docs list free options. Pick one in `hermes tools`.

## Want it set up for you?

CAMBY Ai sets Jade up and runs her for you: https://mikecamby.com

## Credit

Jade runs on [Hermes Agent](https://github.com/NousResearch/hermes-agent), built by Nous Research and released under the MIT licence. Hermes is not ours. This guide is a walkthrough for using it as a personal headhunter. Not affiliated with or endorsed by Nous Research, Telegram or any job site.

## Sources

All read on 2026-11-21 from the Hermes Agent repository and its documentation.

- https://github.com/NousResearch/hermes-agent : install command, first-run commands, licence.
- https://hermes-agent.nousresearch.com/docs/getting-started/quickstart : install paths, `hermes setup`, the 64,000 token minimum, the "Free, start now" option, `hermes doctor`, `hermes tools`.
- https://hermes-agent.nousresearch.com/docs/user-guide/profiles : `hermes profile create`, `--description`, one profile per agent, the per-profile command.
- https://hermes-agent.nousresearch.com/docs/user-guide/features/personality : `SOUL.md`, where it lives and what belongs in it.
- https://hermes-agent.nousresearch.com/docs/user-guide/which-file-does-what : `SOUL.md` versus `AGENTS.md`.
- https://hermes-agent.nousresearch.com/docs/user-guide/features/web-search : web search and page extraction, the provider list.
- https://hermes-agent.nousresearch.com/docs/user-guide/features/browser : the browser toolset, the built-in browser, `/browser connect`, the opt-in profile copy.
- https://hermes-agent.nousresearch.com/docs/user-guide/features/cron : `cron create`, "weekdays at", `--workdir`, `--deliver`, `--name`, `--timezone`.
- https://hermes-agent.nousresearch.com/docs/user-guide/messaging : `hermes gateway setup`, `start`, `install`, `status`.
- https://hermes-agent.nousresearch.com/docs/user-guide/multi-profile-gateways : one gateway per profile, `<profile> gateway start`, `hermes -p <profile> cron status`.
