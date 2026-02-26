# nic-plus

## Description
sample description

## Usage

### Fetch the package
`kpt pkg get REPO_URI[.git]/PKG_PATH[@VERSION] nic-oss`
Details: https://kpt.dev/reference/cli/pkg/get/

### View package content
`kpt pkg tree nic-oss`
Details: https://kpt.dev/reference/cli/pkg/tree/

### Apply the package
```
kpt live init nic-oss
kpt live apply nic-oss --reconcile-timeout=2m --output=table
```
Details: https://kpt.dev/reference/cli/live/
