# Google Cloud Demo Helpers

![Maintenance](https://img.shields.io/maintenance/yes/2025)
[![Contributor Covenant](https://img.shields.io/badge/Contributor%20Covenant-2.1-4baaaa.svg)](CODE_OF_CONDUCT.md)

Many of the demos I've presented to customers or deployed for Google Cloud Next have depended on a common set of helper
applications and terraform/tofu modules. In an attempt to bring this sprawl under control I'm pulling them together in
this single repo.

## Rules for inclusion

* The application or module has been used in at least one demo, and is expected to be reuseable with minimal change; i.e.
  suitable for `kustomization` or `kpt`, or behaviour is adjustable through `terraform` vars.
