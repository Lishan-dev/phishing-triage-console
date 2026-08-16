# Signal - Phishing Triage Console

A single-file, zero-dependency web app that automates the first-pass triage step a SOC email-security analyst runs on a reported phishing email: authentication validation, sender-alignment checks, and URL/domain risk analysis - producing a transparent, evidence-scored threat level instead of a black-box flag.

**[Open `index.html` in any browser — no build step, no server, no API keys needed.]**

## Why this exists

Most "phishing detector" portfolio projects are either a single regex check or a call to a paid API with no visible reasoning. This tool is built to demonstrate the actual triage *logic* a SOC analyst applies — the kind of reasoning you'd walk an interviewer through on a whiteboard — encoded into working software:

- Parses `Authentication-Results` for SPF / DKIM / DMARC outcomes
- Compares the visible `From` domain against `Return-Path` and `Reply-To` for BEC-style mismatches
- Scores every URL in the email against lookalike-domain heuristics: brand-keyword + hyphen patterns, character substitution (`micros0ft` → `microsoft`), edit-distance near-misses, suspicious TLDs, raw-IP hosting, punycode, and known shortener services
- Combines everything into a weighted 0–100 score, mapped to Low / Medium / High / Critical, with every point of the score shown and explained
- Generates a downloadable Markdown incident report

Everything runs client-side. No data is transmitted anywhere, which also means it's safe to paste real headers into for a live interview demo.

## What it deliberately does *not* do (yet)

This tool covers **static email-side triage** — where most reported phishing resolves. It does not correlate with identity/sign-in telemetry, because that requires authenticated access to a live tenant (e.g. Microsoft Graph sign-in logs) or a threat-intel API key (VirusTotal, URLScan.io). That's the natural "Phase 2" and is worth describing explicitly in an interview — being clear about a tool's boundaries is itself a signal of good judgment.

**Roadmap ideas (good talking points, not yet built):**
- Microsoft Graph / Entra ID sign-in log correlation (the manual correlation from the underlying case study, automated)
- Optional VirusTotal / URLScan.io lookups when an API key is supplied
- WHOIS/domain-age lookup via a passive-DNS API
- Batch mode: upload a `.eml` file directly instead of pasting headers
- Export to JSON for SIEM ingestion, not just Markdown

## Try it

Click **"Load phishing sample"** for a fictional lookalike-domain credential-phishing email (SPF/DKIM/DMARC all fail, malicious URL) or **"Load legitimate sample"** for a clean, properly authenticated email, to see the scoring model handle both cases correctly — a tool that flags everything as malicious isn't actually useful to a SOC.

## Tech

Single HTML file. Vanilla JS, no frameworks, no build tooling, no dependencies. Deploy anywhere static: GitHub Pages, Netlify, Vercel, or just double-click `index.html`.

## Background

This tool grew out of a full narrative SOC case study (email investigation → identity correlation → containment → executive reporting) written up separately. This app operationalizes the header/URL/scoring portion of that investigation into something interactive.
