# CircleTel × Zoho One landing page

The landing page for the Microsoft vs Zoho social campaign. Its one job is to collect formal quote requests for Zoho One (or Zoho Workplace, or Zoho as a Microsoft 365 backup).

## Page flow

1. **Hero:** the campaign's outage message, plus a one-tap quote starter (company size) that jumps into the form with that answer filled in.
2. **Trust bar:** Zoho Authorized Partner, 45+ apps, 100M+ Zoho users, local support.
3. **Problem:** one provider, one outage. Email, meetings, chat and files flip from down to running on Zoho.
4. **Zoho One suite:** apps by department (collaboration, sales, marketing, service, finance, HR, analytics), with what each replaces on Microsoft.
5. **Microsoft vs Zoho:** a 12-row comparison against Microsoft 365 Business Standard, plus a "keep both" backup option.
6. **How it works:** three steps and what CircleTel includes.
7. **Quote form:** three steps, with the easy questions first and contact details last.
8. **FAQ:** answers to the usual objections to switching, then a final call to action.

> **Before going live, check the comparison table** against current Microsoft and Zoho plan pages. Plan inclusions change, and comparative claims must be accurate.

It's a single static page (`index.html` + `assets/`) with no build step. Host it anywhere that serves static files, or drop it into the main site.

## Wire up the quote form

The form runs in **preview mode** until you give it somewhere to send requests. In preview mode it validates, logs the payload to the browser console and shows the success screen, but sends nothing.

Open `index.html` and set the endpoint near the top of the `<script>` block:

```js
const QUOTE_ENDPOINT = "https://…";
```

The page sends a JSON `POST` to that URL. Good targets:

- a **Zoho Flow** or **Zoho CRM webhook** that creates a Lead (the natural fit, since CircleTel is a Zoho partner)
- **Zoho Forms**, or your own API

The endpoint must allow CORS from the page's domain.

### Payload

| field | notes |
|---|---|
| `interest`, `company_size` | step 1 (`interest` defaults to Zoho One) |
| `company` | step 2, required |
| `industry`, `users`, `current_platform` | step 2, optional |
| `first_name`, `last_name`, `email`, `phone` | step 3, required |
| `job_title` | step 3, optional |
| `consent` | always `true` (POPIA tick box is required) |
| `utm_source`, `utm_medium`, `utm_campaign`, `utm_content` | read from the URL, for campaign attribution |
| `page`, `submitted_at`, `lead_source` | added automatically |

Tag the social ads' links with UTMs so each lead shows which post it came from, for example:
`?utm_source=facebook&utm_medium=paid_social&utm_campaign=ms-vs-zoho&utm_content=email-down`

## Analytics

If Google Tag Manager or GA4 is on the page, these events are pushed to `dataLayer`:

- `cta_click` (with `cta`, the button that was clicked)
- `quote_start` (hero size chip)
- `quote_step` (1–3)
- `quote_submitted`
- `suite_tab`

Together they give you a funnel from first click to submitted lead.

## Swapping in the final artwork

Photos are taken from the campaign PDF and can be replaced one for one:

| file | used in |
|---|---|
| `assets/hero-outage.jpg` | hero (square works best, subject on the left) |
| `assets/team.jpg` | "Here's how we fix it" (portrait, subjects centred) |
| `assets/zoho-one-box.png` | "Meet Zoho Workplace" (transparent PNG) |
| `assets/zoho-partner.jpg` | Zoho Authorized Partner badge |
| `assets/circletel-logo-white.png`, `assets/circletel-logo.png` | logos from circletel.co.za |

## Design notes

- The look matches circletel.co.za: Manrope headings, Inter body text, navy `#1B2A4A`, orange `#E87A1E`, pill buttons and 16px cards.
- The orange arc and the failure notifications ("Meeting failed to join", "Cannot connect to server") come from the campaign artwork, so the page reads as the continuation of the ad.
- The page responds down to 320px, has visible keyboard focus and respects reduced-motion settings.
