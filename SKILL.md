---
name: web-publish
description: Publish user-provided articles and public notices through website admin interfaces, especially WordPress. Use EgoLite by default, check for duplicates, preserve source attribution, preview content and images, and verify the live permalink.
---

# Web Publish

Publish the exact content and destination the user selects. Keep the source intact; prepare a copy for CMS formatting when needed.

## Browser and authorization

- Use EgoLite `ego-browser` for website interaction. Read `~/.mirasim/skills/ego-browser/SKILL.md` and run `ego-browser skill` at the start of each browser task. Follow the installed guide and use one TaskSpace for the whole task.
- Do not use `agent-browser` unless the user explicitly asks for it. Do not silently switch browser tools if EgoLite fails.
- Reuse the existing logged-in session when available. Never store credentials, cookies, or session data in this skill, logs, or output packages. Enter user-provided credentials only on the named destination's login page.
- Never bypass a certificate, security, or privacy warning. Stop and hand control to the user to resolve it. Also stop for CAPTCHA or browser-owned permission prompts.
- Public posting is consequential. Publish only when the user clearly identifies the content, destination/audience, and asks to publish. Otherwise prepare a draft and ask before the final publish action.

## EgoLite installation detection and guidance

At the start of each browser-dependent task, check `command -v ego-browser` and run `ego-browser skill`. This detects missing or outdated EgoLite before any website action.

If `ego-browser` is missing, read `~/.mirasim/skills/ego-browser/references/install.md` and the operating-system guide. Offer these official routes:

1. **Download the app:** [Apple Silicon DMG](https://cdn.ego.app/setup/macos/arm64/egolite-Y7MbxKIuhzFB.dmg), [Intel Mac DMG](https://cdn.ego.app/setup/macos/x64/egolite-Y7MbxKIuhzFB.dmg), or [Windows installer](https://cdn.ego.app/setup/windows/x64/egoliteSetup-Y7MbxKIuhzFB.exe). Opening the app and completing installation writes the EgoLite browser skill to the Agent skill directories on the machine.
2. **Install only the skill:** `npx skills add citrolabs/ego-lite`. The Agent guides the user through app installation the first time a browser task runs.
3. **Let the Agent install the app:** use the official [citrolabs/ego-lite repository](https://github.com/citrolabs/ego-lite) and follow `skills/ego-browser/references/install.md`.

If an official CLI/Bash install command is available, explain its known effects and ask the user to authorize running it; execute only after explicit approval. For example, `npx skills add citrolabs/ego-lite` installs the skill, while the app installation happens on the first browser task. The desktop-app route installs EgoLite and writes the browser skill to the available Agent skill directories.

On first app launch, the user decides whether to migrate Chrome data. Do not make that choice for them; migration can copy login state, cookies, extensions, and bookmarks. The user completes onboarding and any import choice in the GUI, then confirms when done. Afterward, check `command -v ego-browser`; if necessary, explain the platform guide's PATH fix. Confirm readiness with the documented `ego-browser nodejs` check and rerun `ego-browser skill`, then resume the original publish task.

If `ego-browser skill` is unknown but the command exists, follow the install guide for upgrading. Ask before running `ego-browser upgrade` or reinstalling. Do not use unofficial downloads, add flags that suppress safety prompts, manually remove quarantine, import browser data, or accept operating-system permission dialogs on the user's behalf. If an official installer changes quarantine as part of its documented behavior, disclose that before asking for authorization. Stop for any certificate, privacy, or security prompt shown during installation and let the user handle it. Do not silently switch to another browser tool; if installation is blocked or the platform is unsupported, explain the blocker and wait for the user's choice.

## Source preparation

1. Identify the exact source file or URL and the intended destination. If several versions match, compare names, titles, dates, and content; do not guess.
2. Search the destination's posts and drafts for the title and distinctive phrases. Resolve possible duplicates before creating a new post.
3. Read enough of the source to confirm its title, content, images, links, dates, qualifications, and disclaimers. Preserve wording unless the user asks for edits. Do not publish unrelated sample files or assets.
4. For a full WeChat HTML export, use `#activity-name` (or the article heading) as title and `#js_content` as body when present. Exclude the document shell, scripts, sharing controls, and unrelated WeChat UI. If an image has a lazy `data-src` and a placeholder `src`, use the real image URL in the prepared copy. Do not modify the source file.
5. For standalone HTML without WeChat selectors, identify its actual article container and title. Keep relevant inline formatting. Remove external scripts and page-only controls. If the HTML includes global CSS such as `body`, `:root`, or `*`, scope those rules under a unique wrapper so the article cannot restyle the WordPress theme.
6. If a requested HTML file is missing but a same-title Markdown or other source copy exists, compare the title and content. Use it only when it is clearly the same article, then disclose which variant was used.
7. For source web pages, read the exact user-provided URL; do not expand a no-search request into broader research. Summarize in original wording, link the source, and distinguish what the source explicitly says from inference. For example, a partner logo in a roster supports saying the organization is listed as a partner; it does not establish a specific work package or commitment.
8. For medical or other time-sensitive content, retain source dates, qualifications, and disclaimers. Do not imply that old recruitment, event schedules, submission links, or contact details are current. When publishing a past launch notice, label it as a dated announcement or recap. If current status materially changes the meaning and is unknown, leave a draft and ask. If the user explicitly forbids research, do not imply independent fact-checking; preserve the source's warnings and state the limitation when relevant.

## WordPress workflow

1. Open the named site's `/wp-admin/` in EgoLite and verify the destination and signed-in account.
2. In Gutenberg, create a post, set the title from the source, and add content using WordPress's built-in **Custom HTML** block. Do not paste a full fragment into a paragraph block.
3. Choose an existing category that matches the article. Avoid adding categories or tags without need. Save as a draft before review.
4. Preview the draft in a new tab. Confirm the title, article body, formatting, important images and links, source notes, disclaimer, and category. Fix material rendering problems before publication.
5. In the final publish panel, check visibility and timing. Publish publicly and immediately only when the user's request authorizes it.
6. Verify WordPress shows **Published**, open the live page, and confirm its title and rendered content. Report the permalink and material issues. Keep a useful live page open.

## Image handling

WeChat image URLs can return a 140×140 placeholder on another site even when the URL looks valid. Verify actual `naturalWidth` and `naturalHeight` in the draft preview; a small placeholder is not a successful render.

1. Promote lazy `data-src` URLs to `src` in the prepared copy and preview once.
2. If images still fail, try WordPress's media uploader when appropriate. If WordPress reports that it cannot create or move files under `wp-content/uploads`, treat it as a server filesystem permission problem. Do not repeatedly upload, silently change global media settings, or bypass server protections. Restore any temporary setting if one was changed during diagnosis.
3. When the user supplied or authorized the images and media uploads are unavailable, a self-contained image data URI in the Custom HTML block is a workable fallback. Download only the referenced images using normal TLS certificate validation; never disable certificate checks. Convert to WebP, preserve aspect ratio, and resize only as needed (about 1000 px maximum dimension and quality near 88 worked in the observed workflow). Leave the source file untouched.
4. Keep the resulting post payload small. Data URIs increase post content and revision size; a roughly 2.7 MB HTML block saved, while a roughly 5.8 MB block failed with a database insertion error. These are observations, not guaranteed server limits. If saving fails, reduce image dimensions/quality and retry a smaller payload once. If it still fails, leave the post as a draft and report the media-storage limitation. Never inline large video files.
5. Preview again and verify every article image loads at a plausible full dimension. Do not publish with broken or placeholder images unless the user explicitly chooses that outcome.

## Permalinks and verification

- Copy the live URL directly from WordPress's **View article** link after publishing. Do not manually percent-encode Chinese titles; a single wrong byte can make the link fail.
- Open the copied permalink and confirm the title and content. If a user reports that a link does not open, inspect the actual editor link and test the WordPress post-ID URL (`/?p=POST_ID`); it should resolve to the published article. Correct the link you provide instead of assuming the post is missing.
- Verify publication status in the editor. Do not infer success from clicking Publish alone.

## Stop conditions

Stop before publishing if the source is ambiguous, a duplicate is found, critical content is missing, the preview is broken, the destination differs from the request, or a security warning appears. Report the specific condition and ask only for the missing decision or user action.
