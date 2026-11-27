# Slack Emoji Importer

Chrome and Firefox extension that embeds a search UI into your Slack workspace's emoji customization page, letting you find and import emojis from slackmojis.com and slackemoji.com with one click.

## Features

- Embedded search panel on Slack's `/customize/emoji` page
- Search across both slackmojis.com and slackemoji.com
- One-click "Add to Slack" — no manual download/upload needed
- Filter by source (toggle each site on/off)
- Loads popular emojis on page load

## Installation

### Chrome (Development)
1. Clone this repository
2. Open `chrome://extensions/` in Chrome
3. Enable "Developer mode"
4. Click "Load unpacked" and select this extension's directory
5. Navigate to `https://{your-workspace}.slack.com/customize/emoji`

### Firefox (Development)

Firefox 140 or later is necessary.

1. Clone this repository
2. Run `./package.sh firefox`. This writes the Firefox build to `build/firefox/`.
3. Open `about:debugging#/runtime/this-firefox`
4. Click **Load Temporary Add-on** and select `build/firefox/manifest.json`. Do not select the `manifest.json` in the repository root: that one is for Chrome.
5. Navigate to `https://{your-workspace}.slack.com/customize/emoji`

Firefox removes a temporary add-on when it closes. To keep the add-on installed, sign it on addons.mozilla.org as unlisted (see [Packaging](#packaging)).

## Usage

1. Go to your Slack workspace's emoji customization page
2. The "Emoji Importer" panel appears above the emoji list
3. Type a search term and click Search (or press Enter)
4. Click "Add to Slack" on any emoji to import it directly

## Permissions

| Permission | Why |
|---|---|
| `activeTab` | Interact with the Slack emoji customization page |
| `*://*.slack.com/*` | Inject UI and call Slack's emoji upload API |
| `https://slackmojis.com/*` | Fetch emoji listings and images |
| `https://slackemoji.com/*` | Fetch emoji listings and images |
| `https://emojis.slackmojis.com/*` | Load emoji preview images from CDN |

## Privacy

This extension does not collect, store, or transmit any user data. See [PRIVACY_POLICY.md](PRIVACY_POLICY.md) for details.

## Packaging

Run `./package.sh` to make both zips. The script needs `jq` and `zip`.

- `./package.sh chrome` makes `slack-emoji-importer-chrome-vX.Y.Z.zip` for the Chrome Web Store.
- `./package.sh firefox` makes `slack-emoji-importer-firefox-vX.Y.Z.zip` for addons.mozilla.org (AMO).

The two browsers use the same JavaScript, CSS and icons. Only the manifest is different:

- Chrome uses `manifest.json` from the repository root as is.
- Firefox uses `manifest.json` merged with `firefox/manifest.overrides.json`. The Firefox manifest runs `background.js` as an event page, because Firefox has no background service worker. It also adds the add-on ID and the "no data collection" statement that AMO requires.

To check the Firefox build, run `npx web-ext lint --source-dir build/firefox`.

To sign the Firefox build for your own use (unlisted, stays installed after restarts), put your AMO API keys in a file as `WEB_EXT_API_KEY` and `WEB_EXT_API_SECRET`, load them into the shell, then run:

```bash
npx web-ext sign --channel=unlisted --source-dir build/firefox --artifacts-dir build/signed
```

Create the API keys at https://addons.mozilla.org/en-US/developers/addon/api/key/.
