I asked Claude to test a skill that I personally found interesting named: “api-review” at the link: 
https://skills-hub.ai/skills/api-review

The purpose of the skill is to check if the REST API design is consistent, best practices are followed, naming consistency, pagination etc.
It did not flag any security issues with the skill. Here is the exact response (I did not read a single line of code on the GitHub repository of the skill).

I have added the .md file for the skill in the GitHub repo.

## Claude's Response
```## What it does, plainly

This is a checklist written for an AI assistant, not a program. It tells the assistant: "go read through this person's code, find all the web addresses (endpoints) their software offers to the outside world, and judge whether they were built sensibly."

The judging covers things like:
- Are things named consistently? (One place says "user," another says "users.")
- When someone asks for data, does the code actually just fetch it — or does it secretly change something?
- When something goes wrong, does the code report the right kind of failure, and always in the same shape?
- If a list could return a million items, is there a way to get them in smaller batches?
- Is there protection against someone hammering the system with requests, or accidentally submitting the same order twice?

At the end it asks for a report: a summary table, a numbered list of problems with severity ratings and file locations, and a score out of 100. It also has a "check your own work" step (re-read the report, fix weak spots, up to two passes) and a list of things it should *not* complain about, to reduce nitpicking.

## Does it phone home, touch passwords, or move your data?

Short answers: no, no, and no — with two things worth knowing.

**No external servers.** Nothing in the file contains a web address, an upload step, or an instruction to send anything anywhere. Every action is "read files in this project."

**No credentials.** It never asks for passwords, keys, or logins. It does look at authentication-related code (which parts of your system require a login), but that's reading how the doors are configured, not collecting the keys.

**One local write.** The last section tells the assistant to jot a few lines into a note file on your own machine, under a Claude settings folder — the date, whether the review went well, and one suggestion for improvement. It stays on your computer. It's a bit presumptuous to write files you didn't ask for, but it's harmless and it skips silently if the folder isn't there.

The two things to know:

1. **It's meant to run without asking you anything.** The file explicitly says not to ask questions and to investigate the whole codebase. So it will read broadly — including config files that may sit near secrets. It's reading, not transmitting, but if you'd rather it stayed in one corner, the file supports being pointed at a specific area instead of everything.

2. **Reading your code means your code goes to the AI.** That's true of any assistant working on your project and isn't special to this file — but "no external server" means *this file* doesn't add one, not that your code never leaves your machine.

## Is it safe?

Yes, as far as I can tell. It only reads and reports; it doesn't change your code, delete anything, install anything, or reach out to the internet. The worst realistic outcome is a mediocre report or some confidently wrong advice.

Two soft caveats rather than warnings: the score out of 100 is invented on the spot and shouldn't be treated as a real measurement, and the "next steps" at the bottom suggest running other commands with names like `/security-review` — those are separate things you'd have to install and check yourself. This file can't run them for you.```