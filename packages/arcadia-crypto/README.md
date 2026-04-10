# Arcadia Crypto

![GitHub release](https://img.shields.io/github/v/release/memes/google-demo-helper-modules?sort=semver&filter=*arcadia-crypto)

## Description

## Usage

### Fetch the package

```shell
kpt pkg get --for-deployment https://github.com/memes/google-demo-helper-modules.git/applications/arcadia-crypto arcadia-crypto
```

Details: https://kpt.dev/reference/cli/pkg/get/

### View package content

```shell
kpt pkg tree arcadia-crypto
```

Details: https://kpt.dev/reference/cli/pkg/tree/

### Apply the package

```shell
kpt live init arcadia-crypto
kpt live apply arcadia-crypto --reconcile-timeout=2m --output=table
```

Details: https://kpt.dev/reference/cli/live/
