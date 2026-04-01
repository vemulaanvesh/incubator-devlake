# Internal-only Dockerfile — intentionally NOT part of the upstream community PR.
# Compiles only the patched argocd plugin and layers it on top of the official
# DevLake image, avoiding full image rebuild (no libgit2, no mocks/swag).
FROM --platform=linux/amd64 golang:1.20.5-bookworm AS argocd-plugin-builder

ARG GOPROXY=

RUN apt-get update && apt-get install -y gcc

WORKDIR /app
COPY . /app

RUN go mod download

RUN CGO_ENABLED=1 go build -buildmode=plugin \
    -o /app/bin/plugins/argocd/argocd.so \
    ./plugins/argocd/*.go

# Layer the patched plugin on top of the official DevLake image.
# All other binaries, Python environment, and config are preserved exactly.
FROM apache/devlake:v1.0.3-beta9

COPY --from=argocd-plugin-builder /app/bin/plugins/argocd/argocd.so /app/bin/plugins/argocd/argocd.so
