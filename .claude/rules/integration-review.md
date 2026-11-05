# Review checks for this integration

Use these checks when you review a change in this repo. The CI review and
`@claude` read this file. The severity names are the review headings.

## Home Assistant and the FireBoard API

- Blocking: a blocking (sync) I/O call runs in the Home Assistant event
  loop.
- Blocking: a change can go above the FireBoard rate limit of 200 calls
  per hour.
- Blocking: a password, token or other secret can go into a log.
- Blocking: an entity ID or name is hardcoded. Names come from the API
  data.
- Important: the 401 retry or the 429 `UpdateFailed` behavior changes
  with no reason.
- Important: `manifest.json` lists a requirement that Home Assistant
  already supplies.

## Tests

- Important: new functionality has no test, or coverage falls below 80%.
