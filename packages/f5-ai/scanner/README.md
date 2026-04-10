# F5 AI Scanner package

Provides a [Kpt] package for deploying F5 AI Guardrails components from manifests extracted from an embedded helm chart.

## Quick steps

1. Fetch package to local directory `f5-ai-scanner`

   ```shell
   kpt pkg get --for-deployment https://github.com/memes/google-demo-helper-modules.git/packages/f5-ai/scanner f5-ai-scanner
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
        name: f5-ai-scanner
        annotations:
          config.kubernetes.io/local-config: "true"
      build: {}
      manifests:
        kpt:
          - path/to/f5-ai-scanner
      deploy:
        kubectl: {}
      ```

      See the [Skaffold] documentation for other examples, including deploying with Kpt.

   1. Render to YAML manifests

      Generating plain YAML from the Kpt package allows the use of any deployment tool that can work with Kubernetes
      manifests. The manifests can be written to a separate location, preserving the Kpt package sources for further
      iteration as needed.

      ```shell
      kpt fn render f5-ai-scanner -o path/to/contain/generated/manifests
      ```

      Remove the `-o` option to replace the Kpt package in-situ with rendered YAML.
      > NOTE: This removes the Kpt package files by default, leaving just the rendered YAML.

      ```shell
      kpt fn render f5-ai-scanner
      ```

   1. Deploy with [kpt]

      See the [kpt book](https://kpt.dev/book/06-deploying-packages/) for more info on deploying with [kpt].

      ```shell
      kpt live init f5-ai-scanner
      kpt live apply f5-ai-scanner
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
      (cd f5-ai-scanner && \
          kpt fn eval --image ghcr.io/kptdev/krm-functions-catalog/apply-setters:latest --fn-config ./apply-setters.yaml)
      ```

      > NOTE: Ideally, this step is repeated every time values in [apply-setters.yaml] changes. The declared `kpt`
      > pipeline does include this step, but since the files that drive the pipeline are sourced before it is executed
      > the pipeline cannot update itself prior to rendering.

   1. Review [helm-chart.yaml]

      Step 1 will perform value substitutions for simple, common changes seen in most deployments, but is unsuitable
      for structured entries or configuration options that are rarely changed. You should examine the declared values
      and make any necessary changes.

      E.g. to change the `nodeSelector` value for `nvidia-gpu-l4` resource profile so that deployed models are
      not tied to Spot VMs, which are often unavailable:

      ```yaml
      helmCharts:
        - chartArgs:
          ...
          templateOptions:
            ...
            values:
              valuesInline:
                ...
                kubeai:
                  resourceProfiles:
                    ...
                    nvidia-gpu-l4:
                      nodeSelector:
                        cloud.google.com/gke-accelerator: "nvidia-tesla-a100"
                        # Disabled this selector so model is provisioned when spot VMs are unavailable
                        # cloud.google.com/gke-spot: "true"
      ```

1. Review [Kptfile] and add other local pipeline mutators

   Other mutators should be appended to the end of the existing `pipeline` declaration, if needed.
   See [Kpt function catalog](https://catalog.kpt.dev/) for details.

[kpt]: https://kpt.dev
[skaffold]: https://skaffold.dev
[apply-setters.yaml]: ./apply-setters.yaml
[helm-chart.yaml]: ./helm-chart.yaml
[Kptfile]: ./Kptfile
