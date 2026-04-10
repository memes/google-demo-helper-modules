# F5 AI Moderator package

Provides a [Kpt] package for deploying F5 AI Guardrails moderator component from manifests extracted from an embedded
helm chart.

## Quick steps

1. Fetch package to local directory `f5-ai-moderator`

   ```shell
   kpt pkg get --for-deployment https://github.com/memes/google-demo-helper-modules.git/packages/f5-ai/moderator f5-ai-moderator
   ```

1. Edit configuration

   Examine the source files and make changes where necessary. See [Configuration](#configuration) for full details.

1. Deploy the package

   Choose the deployment method that is best suited to your environment.

   1. Deploy with [Skaffold] as Kpt manifest

      To use with [Skaffold] directly or with Cloud Deploy, add a path entry to the package directory under `manifests.kpt`.

      E.g.

      ```yaml
      ---
      apiVersion: skaffold/v4beta13
      kind: Config
      metadata:
        name: f5-ai-moderator
        annotations:
          config.kubernetes.io/local-config: "true"
      build: {}
      manifests:
        kpt:
          - path/to/f5-ai-moderator
      deploy:
        kubectl: {}
      ```

      See the [Skaffold] documentation for other examples, including deploying with Kpt.

   1. Render to YAML manifests

      Generating plain YAML from the Kpt package allows the use of any deployment tool that can work with Kubernetes
      manifests. The manifests can be written to a separate location, preserving the Kpt package sources for further
      iteration as needed.

      ```shell
      kpt fn render f5-ai-moderator -o path/to/contain/generated/manifests
      ```

      Remove the `-o` option to replace the Kpt package in-situ with rendered YAML.
      > NOTE: This removes the Kpt package files by default, leaving just the rendered YAML.

      ```shell
      kpt fn render f5-ai-moderator
      ```

   1. Deploy with [kpt]

      See the [kpt book](https://kpt.dev/book/06-deploying-packages/) for more info on deploying with [kpt].

      ```shell
      kpt live init f5-ai-moderator
      kpt live apply f5-ai-moderator
      ```

## Configuration

1. Edit base settings

   1. Set simple parameter values and apply

      Edit [apply-setters.yaml] and update the values to suit your deployment; this file drives most of the
      customization needs of the package and ensures that environment specific settings are applied to the package. For
      example, the base URL of the F5 AI Console and the PostgreSQL instance host often differ for each deployment.

      Performing a manual apply is the *safest* way to ensure the values declared in [apply-setters.yaml] are updated on
      all resources, including Kpt pipeline definitions, before trying to render or deploy the package.

      ```shell
      (cd f5-ai-moderator && \
          kpt fn eval --image ghcr.io/kptdev/krm-functions-catalog/apply-setters:latest --fn-config ./apply-setters.yaml)
      ```

      > NOTE: Ideally, this step is repeated every time values in [apply-setters.yaml] changes. The declared `kpt`
      > pipeline does include this step, but since the files that drive the pipeline are sourced before it is executed
      > the pipeline cannot update itself prior to rendering.

   1. Review [helm-chart.yaml]

      Step 1 will perform value substitutions for simple, common changes seen in most deployments, but is unsuitable
      for structured entries or configuration options that are rarely changed. You should examine the declared values
      and make any necessary changes.

      E.g. to change the `nodeSelector` value for GKE Autopilot deployments to use any machine-type compatible with
      Performance compute-class:

      ```yaml
      helmCharts:
        - chartArgs:
          ...
          templateOptions:
            ...
            values:
              valuesInline:
                ...
                nodeSelector:
                  # Disabled this selector so any suitable machine-type can be used
                  # cloud.google.com/machine-family: c4
                  cloud.google.com/compute-class: Performance
      ```

1. Gateway API integration

   This package is written to integrate F5 AI Moderator web console with an existing Gateway instance deployed to the
   cluster. If the deployment will not be using a shared Gateway implementation delete the [gateway.yaml] file before rendering.

   The Gateway integration defined in this package attaches a Cloud Armor policy to deployment. If this is not correct
   remove the `GCPBackend` declaration from the [gateway.yaml] declaration.

1. Google Secret Manager integration

   This package assumes that Kubernetes Secret objects will be created from Google Secret Manager secrets via GKE
   SecretSync add-on. The default `kpt` pipeline strips out Helm generated Secret declarations, replacing them with
   SecretStore and SecretSync declarations.

   To remove this assumption and use Helm generated Kubernetes Secrets do the following:

   1. Edit [Kptfile] and remove these entries from `pipeline`:

      ```yaml
      - image: ghcr.io/kptdev/krm-functions-catalog/set-annotations:latest
        configMap:
          config.kubernetes.io/local-config: "true"
          selectors:
            - apiVersion: v1
              kind: Secret
      ```

      ```yaml
      - image: ghcr.io/kptdev/krm-functions-catalog/starlark:latest
        configPath: secrets-sync-generator.yaml
      ```

   1. Delete the files [secrets-sync-generator.yaml] and [secrets-sync.yaml]

1. Review [Kptfile] and add other local pipeline mutators

   Other mutators should be appended to the end of the existing `pipeline` declaration, if needed.
   See [Kpt function catalog](https://catalog.kpt.dev/) for details.

[kpt]: https://kpt.dev
[skaffold]: https://skaffold.dev
[apply-setters.yaml]: ./apply-setters.yaml
[helm-chart.yaml]: ./helm-chart.yaml
[gateway.yaml]: ./gateway.yaml
[Kptfile]: ./Kptfile
[secrets-sync-generator.yaml]: ./secrets-sync-generator.yaml
[secrets-sync.yaml]: ./secrets-sync.yaml
