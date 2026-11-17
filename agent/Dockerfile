# The Kelos Codex worker with the toolchains `make verify` needs in hearthctl repositories.
FROM ghcr.io/kelos-dev/codex:v0.64.0@sha256:3edc0f857addc5967a87cb21e24e9acf8fdd71e16e75a58cd5c3f9c4514a2f14

USER root

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    lz4 \
    python3 \
    python3-venv \
    && rm -rf /var/lib/apt/lists/*

ARG TARGETARCH
ARG HELM_VERSION=v3.19.2
RUN TARBALL="helm-${HELM_VERSION}-linux-${TARGETARCH}.tar.gz" \
    && curl -fsSL -o "/tmp/${TARBALL}" "https://get.helm.sh/${TARBALL}" \
    && curl -fsSL -o "/tmp/${TARBALL}.sha256sum" "https://get.helm.sh/${TARBALL}.sha256sum" \
    && (cd /tmp && sha256sum -c "${TARBALL}.sha256sum") \
    && tar -C /usr/local/bin -xzf "/tmp/${TARBALL}" --strip-components=1 "linux-${TARGETARCH}/helm" \
    && rm "/tmp/${TARBALL}" "/tmp/${TARBALL}.sha256sum"

COPY --from=ghcr.io/astral-sh/uv:0.13.9 /uv /uvx /usr/local/bin/

USER agent
