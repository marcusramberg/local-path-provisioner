# syntax=docker/dockerfile:1.7.0

FROM --platform=${BUILDPLATFORM:-linux/amd64} golang:1.24.0-bullseye AS builder

ARG TARGETPLATFORM
ARG BUILDPLATFORM
ARG TARGETOS
ARG TARGETARCH


WORKDIR /app/
ADD . .

RUN CGO_ENABLED=0 GOOS=${TARGETOS} GOARCH=${TARGETARCH} go build -ldflags="-w -s" -o local-path-provisioner-${TARGETARCH} .

FROM alpine

ARG TARGETPLATFORM
ARG TARGETARCH
RUN if [ "$TARGETPLATFORM" != "linux/amd64" ] && [ "$TARGETPLATFORM" != "linux/arm64" ] && [ $TARGETPLATFORM != "linux/arm64/v8" ] && [ "$TARGETPLATFORM" != "linux/arm/v7" ] && [ "$TARGETPLATFORM" != "linux/riscv64" ]; then \
    echo "Error: Unsupported TARGETPLATFORM: $TARGETPLATFORM" && \
    exit 1; \
    fi

RUN apk update
RUN apk upgrade --no-cache busybox zlib

COPY --from=builder /app/local-path-provisioner-${TARGETARCH} /usr/bin/local-path-provisioner
CMD ["local-path-provisioner"]
