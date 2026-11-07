# Hosted macOS guard

Fails a job immediately when a **private** brightdigit repository runs it on a
**GitHub-hosted macOS** runner.

The brightdigit org is on GitHub's Free plan. Private repositories share 2,000
included Actions minutes per month, and macOS minutes count **10×**. Private
repos should run macOS jobs on the self-hosted Macs (`[self-hosted, macOS]`).

## Usage

Add it as the first step of every macOS job:

```yaml
jobs:
  build-macos:
    runs-on: [self-hosted, macOS]
    steps:
      - uses: brightdigit/.github/actions/hosted-macos-guard@main
      - uses: actions/checkout@v6
      # ...
```

## Behavior

| Runner | Repository | Result |
|---|---|---|
| GitHub-hosted macOS | private | ❌ fails with an error annotation |
| GitHub-hosted macOS | public | ✅ passes (public repos are free) |
| GitHub-hosted macOS | unknown (no `repository.private` in the event payload) | ❌ fails closed |
| Self-hosted macOS | any | ✅ step skipped |
| Linux / Windows | any | ✅ step skipped |

To allow hosted macOS deliberately in one job:

```yaml
      - uses: brightdigit/.github/actions/hosted-macos-guard@main
        with:
          allow: 'true'
```

## Limits

- A blocked job still bills its first hosted-macOS minute (10 included
  minutes), but it stops within seconds instead of running a full build.
- Only jobs that include this step are protected.
