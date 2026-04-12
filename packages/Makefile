#
# Helm chart handler for Kpt packages
F5_AI_PACKAGES:=$(addprefix f5-ai/, moderator scanner redteam)
GATEWAY_API_INFERENCE_EXTENSION_PACKAGES=$(addprefix gateway-api-inference-extension/, body-based-routing inference-pool)


.PHONY: all
all: $(addsuffix /helm-chart.yaml, $(F5_AI_PACKAGES) $(GATEWAY_API_INFERENCE_EXTENSION_PACKAGES))

f5-ai/moderator/helm-chart.yaml:

f5-ai/scanner/helm-chart.yaml:

f5-ai/redteam/helm-chart.yaml:

gateway-api-inference-extension/body-based-routing/helm-chart.yaml:

gateway-api-inference-extension/inference-pool/helm-chart.yaml:

# Build a ready-to-render RenderHelmChart resource containing the upstream chart, with generated secrets.
%/helm-chart.yaml: src/%/helm-chart.yaml
	build_dir="$$(mktemp -d)" && \
		kustomize build -o "$${build_dir}" && \
		rsync -avP "$(<D)/" "$${build_dir}/" && \
		kpt fn source "$${build_dir}" | \
			kpt fn eval - --network \
				--image ghcr.io/krm-functions/source-helm-chart@sha256:865232a46bca8e1294c64d0de35437e18936b049159d8329000b44fd4c84c99f | \
			kpt fn sink "$${build_dir}/built" && \
		cp "$${build_dir}/built/$(<F)" "$@"; \
		rm -rf "$${build_dir}"
