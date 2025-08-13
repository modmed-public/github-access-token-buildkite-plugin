# github-access-token-buildkite-plugin
A Buildkite Plugin for authenticating buildkite-agents with Github using a Github App and short-lived tokens.

## Purpose
This plugin provides a repeatable and declarative way to add Git credentials authorization between a host running a **buildkite_agent** and the MM Github Enterprise account.  We achieve this by having a Github App -- https://github.com/organizations/modmed/settings/apps/buildkite-auth-app -- that gives the **Contents** permission for each repo we need to be able to clone from Buildkite.

We then use this plugin to run the **pre-checkout** hooks that will contact the Github App and create a temporary access token.  This agent is then updated to use this token for subsequent git operations.

### Requirements
The plugin assumes the following Buildkite Secrets are defined in the Buildkite agent dashboard for the queues running the agent:

- GITHUB_BUILDKITE_AUTH_APP_CLIENT_ID
- GITHUB_BUILDKITE_AUTH_APP_PRIVATE_KEY
- GITHUB_BUILDKITE_AUTH_APP_INSTALL_ID

If these are not available, the plugin will fail to run.

### Usage

Add the following to the pipeline YAML file whose corresponding repo is hosted in the MM Github Enterprise account:

```
steps:
  - label: ":xcode: Build"
    command: xcodebuild | xcbeautify
    env:
      BUILDKITE_PLUGINS_ALWAYS_CLONE_FRESH: "false" # Make true if debugging
    plugins:
      - modmed-public/github-access-token#v1.0.0
    agents:
      queue: "some-queue"
```