# FIRE Foundation Website

Website for the **Forum for the International Renewal of Education (FIRE)** — an initiative by international educators championing ethical, values-based education in the age of AI.

**Live site:** [firefoundation.eu](https://firefoundation.eu)

---

## Project Structure

```
firefoundation/
├── index.html              # Main single-page website
├── privacy-policy.html     # GDPR-compliant privacy policy page
├── README.md
├── images/
│   ├── logo.png            # FIRE Foundation logo
│   ├── dandelion.jpg       # Hero background image
│   ├── community.jpg       # Mission section image
│   ├── krzys.jpg           # Team photo - Krzysztof Zajaczkowski
│   ├── agata.jpg           # Team photo - Agata Hofman
│   ├── csillia.jpg         # Team photo - Csilla Fuszek
│   ├── arjav.jpg           # Team photo - Arjav Parikh
│   ├── agnieszka.jpg       # Team photo - Agnieszka Kosiak
│   ├── ruth.jpeg           # Team photo - Ruth Bar-Sinai
│   ├── paul.jpg            # Team photo - Paul Schober
│   ├── kristof.jpg         # Team photo - Kristof Fenyvesi
│   └── luis.png            # Team photo - Luis Pastor
└── articles/
    └── road-to-a-better-self.pdf   # Article by FIRE team members
```

## Tech Stack

- **Pure HTML/CSS/JS** — no build tools, no frameworks
- **Google Fonts** — Lora (headings) + Inter (body)
- **Google Apps Script** — backend for pledge form submissions
- **Google Sheets** — data storage for pledge submissions

## Site Sections

| Section     | Description                                                         |
|-------------|---------------------------------------------------------------------|
| Hero        | Tagline, call-to-action, Plutarch quote                             |
| Mission     | Why FIRE exists, core values (Compassion, Integrity, Courage, Love) |
| Manifesto   | 3 tabbed sections: General, Pedagogical, Practical (10 principles each) |
| Team        | 9 team member cards with photos and bios                            |
| Pledge CTA  | Call-to-action to sign the pledge                                   |
| Contact     | Contact details for Agata Hofman and Arjav Parikh                   |
| Footer      | Logo, nav links, privacy policy link, legal info                    |

## Navigation

The desktop nav includes a **Reading** dropdown that lists articles published by FIRE team members. Each article entry shows the title, format, and contributing team members. On mobile, articles appear as direct links in the nav menu.

---

## Pledge Form & Google Sheets Integration

The "Sign the Pledge" button opens a modal form that collects user data and submits it to a Google Sheet via Google Apps Script.

### Data Collected

| Field       | Required | Stored in Sheet |
|-------------|----------|-----------------|
| First Name  | Yes      | Column B        |
| Last Name   | Yes      | Column C        |
| Email       | Yes      | Column D        |
| Country     | Yes      | Column E        |
| Contact Me  | No       | Column F        |

Column A stores the **timestamp** (auto-generated server-side by Apps Script).

### How It Works

1. User fills out the pledge form in the modal and ticks the required GDPR consent checkbox
2. On submit, the JS sends a `POST` request with a JSON body to the Apps Script web app
3. The Apps Script `doPost()` function parses the JSON and appends a row to the Google Sheet
4. On success, the modal shows a "Thank You" confirmation screen

### Apps Script Setup

The deployed Apps Script web app is at:

```
https://script.google.com/macros/s/AKfycbw8aXQN02yrWJ-GlRMqZev1LcTP3C2jmit-zqnOwugi1Uxy2dCxYpng7fQg9zPMzCsIkg/exec
```

**Deployment ID:** `AKfycbw8aXQN02yrWJ-GlRMqZev1LcTP3C2jmit-zqnOwugi1Uxy2dCxYpng7fQg9zPMzCsIkg`

The Apps Script code (lives in Google Apps Script editor, not in this repo):

```javascript
function doPost(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var data = JSON.parse(e.postData.contents);

  sheet.appendRow([
    new Date(),
    data.firstName,
    data.lastName,
    data.email,
    data.country,
    data.contactMe
  ]);

  return ContentService
    .createTextOutput(JSON.stringify({ status: 'success' }))
    .setMimeType(ContentService.MimeType.JSON);
}
```

### Google Sheet Column Headers

| A         | B         | C        | D     | E       | F         |
|-----------|-----------|----------|-------|---------|-----------|
| timestamp | firstName | lastName | email | country | contactMe |

### Updating the Apps Script

If you need to modify the Apps Script code:

1. Open the Google Sheet > **Extensions > Apps Script**
2. Edit the code
3. Click **Deploy > Manage deployments**
4. Edit the existing deployment and click **Deploy** to publish changes

> **Note:** If you create a *new* deployment instead of editing the existing one, you'll get a new URL and will need to update it in `index.html`.

---

## GDPR Compliance

- The pledge form includes a **required consent checkbox** that users must tick before submitting
- The checkbox links to [privacy-policy.html](privacy-policy.html) which details:
  - What data is collected and why
  - Legal basis for processing (GDPR Article 6(1)(a) — consent)
  - Data retention and deletion policy
  - User rights (access, rectification, erasure, portability, etc.)
  - Contact information for data-related requests
- The optional "contact me" checkbox is separate, so marketing consent is opt-in only
- The privacy policy link is also in the site footer

---

## Deployment

Static site — no build step required. Deploy by uploading all files to your web host, ensuring the directory structure is preserved. Make sure the `articles/` and `images/` folders are included.
