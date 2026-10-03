# Fork test builds

This fork tests portable frontend improvements before upstream acceptance.
Feature branches must start from freshly fetched `upstream/main`; never merge
fork main into a contribution branch. Preserve upstream defaults and use
synthetic public fixtures, never household configuration or screenshots.

```yaml
fork-profile:
  upstream: AlexandrErohin/home-assistant-flightradar24
  upstream-default-branch: main
  fork-owner: Zensqrl
  deploy-target: HACS integration custom repository; flightradar24.zip release asset
  version-scheme: increment patch above upstream HACS release; no build metadata
  overlay:
    - AGENTS.md
  verify:
    - node --check custom_components/flightradar24/frontend/flightradar24-card.js
    - node --test tests/test_card_navigation.cjs
    - python -m pytest
    - python -m flake8
  release: merge feature with --no-ff into main; bump manifest version; verify; tag and publish ZIP
```

Version fields change only on main. Include neither this fork profile nor a
version bump in upstream PRs. Sync upstream into main using merge commits.
The initial fork build v2.2.3 is based on upstream v2.2.2; the release title
must identify it as a fork touchscreen test build. Do not reuse published tags.

HACS manages one `custom_components/flightradar24` directory. Do not leave
both upstream and fork marked installed: their update sources would compete.
Before replacement, retain an HA backup and exact integration/dashboard
readbacks outside Git. Remove only upstream HACS files/installation metadata,
retain the existing Flightradar24 config entry, and download the fork before
any restart. If download fails, reinstall the recorded upstream release
immediately. Restart only with action-time user approval. Roll back using the
reverse HACS replacement and restore the card's previous options.

Physical touchscreen acceptance is separate from browser emulation. Record
pinch, drag, zoom, selection/dismissal, Reset view and live-update behavior;
never claim physical verification from an emulated device.
