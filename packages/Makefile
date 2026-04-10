#
# Helm chart handler for Kpt packages
F5_AI_PACKAGES:=moderator scanner redteam

.PHONY: all
all: $(addprefix f5-ai/, $(addsuffix /helm-chart.yaml, $(F5_AI_PACKAGES)))

f5-ai/moderator/helm-chart.yaml:

f5-ai/scanner/helm-chart.yaml:

f5-ai/redteam/helm-chart.yaml:

# Build a ready-to-render RenderHelmChart resource containing the upstream chart
%/helm-chart.yaml: src/%/helm-chart.yaml
	build_dir="$$(mktemp -d)" && \
		kustomize build -o "$${build_dir}" && \
		rsync -avP "$(<D)/" "$${build_dir}/" && \
		kpt fn source "$${build_dir}" | \
			kpt fn eval - --network --image  ghcr.io/krm-functions/source-helm-chart:latest | \
			kpt fn sink "$${build_dir}/built" && \
		cp "$${build_dir}/built/$(<F)" "$@"; \
		rm -rf "$${build_dir}"
