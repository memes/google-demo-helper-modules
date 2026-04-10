# Google Cloud OTEL Collector for GKE

## Usage

```shell
kpt pkg get --for-deployment https://github.com/memes/google-demo-helper-modules.git/applications/gcp-otlp-k8s-ingest/ applications/gcp-otlp-k8s-ingest
```

Modify the `${GOOGLE_CLOUD_PROJECT}` value on line 143 of [upstream/1_configmap.yaml](upstream/1_configmap.yaml) to be match the target project with logging, monitoring and cloud trace enabled.
