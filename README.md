# Web Publisher

`web-publish` is a Codex skill for preparing, previewing, and publishing user-provided articles and notices through website CMSes, especially WordPress. It is intended to support maintenance and article publishing for the XYB (小胰宝) official website.

## What it does

- Uses EgoLite (`ego-browser`) for browser workflows.
- Checks for duplicate posts, preserves source dates and attribution, and previews content before publication.
- Handles WeChat HTML exports and lazy-loaded images.
- Verifies that a post is published and that its public permalink opens correctly.
- Provides a self-contained WebP image fallback when WordPress media uploads are unavailable, while keeping credentials and session data out of the post and repository.

## Install

Install this skill from the public repository:

```bash
npx skills add opencare-skillhub/web-publisher
```

Browser tasks require EgoLite. The skill checks for `ego-browser` and guides installation using the official EgoLite app downloads or setup instructions. When an install command is available, it asks for authorization before running it. The user completes onboarding and chooses whether to import browser data.

## Use for XYB site maintenance

Invoke the skill in Codex with `$web-publish`. For XYB official website maintenance, request the required password from site administrator Tian Laoshi (田老师). This repository contains no username, password, cookies, or session data. Do not commit credentials or browser state.

## License

MIT. See [LICENSE](LICENSE).
