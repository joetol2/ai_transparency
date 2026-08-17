# Authentication Limitation Notice

## Current State

The site uses a client-side password gate. The password is stored in plaintext JavaScript within the HTML source:

```javascript
var PASSWORD = "qs2026";
```

**This is not meaningful access control.** Any person who can load the page URL can read the full HTML source — including the password and all page content — without entering anything. The visual gate is a usability convention, not a security boundary.

The site is hosted via GitHub Pages. GitHub Pages serves static files from a public or private repository with no server-side request handling. This means:

- There is no server to validate a session token or credentials before serving content.
- No `.htpasswd`, HTTP Basic Auth, or session cookie can be enforced at the hosting layer without adding a proxy or identity-aware access layer in front of GitHub Pages.
- Content in the repository and deployed to GitHub Pages should be treated as publicly accessible, regardless of what the client-side gate displays.

## What Is Required for Real Access Control

Real access control requires one of the following:

### Option 1: Cloudflare Access (recommended, lowest migration cost)

Add Cloudflare as a proxy in front of the GitHub Pages URL and configure Cloudflare Access with an identity policy (Google Workspace, Okta, email one-time PIN, or similar). Cloudflare enforces the auth challenge before the browser receives any page content.

**Steps required:**
1. Point the `ai_transparency` custom domain (or create one) through Cloudflare DNS.
2. Enable Cloudflare Access for the subdomain.
3. Configure an access policy — e.g., "allow users with @q-s.com email addresses."
4. Remove or disable the current client-side password gate to avoid confusion.

The GitHub repository and deployment workflow remain unchanged. GitHub Pages continues to serve the static files. Cloudflare sits in front and enforces the gate before any content reaches the browser.

**Cost:** Cloudflare Access free tier covers up to 50 users. Paid tiers are available for larger teams.

### Option 2: Move hosting to a platform with native auth

Netlify, Vercel, and similar platforms support password protection or identity-aware access controls at the hosting layer for static sites. The repository and content remain unchanged; only the deployment target changes.

### Option 3: Move to a private GitHub repository with no public Pages deployment

If the audience is limited to GitHub users in the organization, serving the content only through the GitHub repository (not via GitHub Pages) and controlling access via repository visibility and team membership is a simple option. This eliminates the public URL entirely.

## What This Means for Content

Until one of the above options is implemented:

- Do not add confidential Q-S client information, deal specifics, personnel data, or sensitive legal strategy documents to this site.
- Treat all content as if it were publicly readable.
- The site's current content — legal tracking and internal guidance on U.S. AI law — is appropriate for a restricted-distribution internal resource but should not include anything that would cause harm if read by an external party.

## Developer Checklist Before Publishing Content

- [ ] Is this content appropriate to publish if a competitor, regulator, or member of the public reads it?
- [ ] Does this content reference any confidential client matter?
- [ ] Does this content include personal data, credentials, or internal system references?

If any answer is yes, do not publish it to this site until real authentication is in place.
