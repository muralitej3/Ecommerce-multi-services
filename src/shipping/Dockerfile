Copyright The OpenTelemetry Authors
SPDX-License-Identifier: Apache-2.0

FROM --platform=$BUILDPLATFORM rust:1.76 AS builder

ARG TARGETARCH ARG TARGETPLATFORM ARG BUILDPLATFORM

RUN echo "Building on ${BUILDPLATFORM} for ${TARGETPLATFORM}"

Install dependencies for native or cross-compilation

RUN if [ "
𝑇
𝐴
𝑅
𝐺
𝐸
𝑇
𝑃
𝐿
𝐴
𝑇
𝐹
𝑂
𝑅
𝑀
"
=
"
{BUILDPLATFORM}" ]; then
apt-get update && apt-get install -y --no-install-recommends
g++ libc6-dev libprotobuf-dev protobuf-compiler ca-certificates wget;
elif [ "${TARGETPLATFORM}" = "linux/arm64" ]; then
apt-get update && apt-get install -y --no-install-recommends
g++-aarch64-linux-gnu libc6-dev-arm64-cross
libprotobuf-dev protobuf-compiler ca-certificates wget &&
rustup target add aarch64-unknown-linux-gnu;
elif [ "${TARGETPLATFORM}" = "linux/amd64" ]; then
apt-get update && apt-get install -y --no-install-recommends
g++-x86-64-linux-gnu libc6-amd64-cross
libprotobuf-dev protobuf-compiler ca-certificates wget &&
rustup target add x86_64-unknown-linux-gnu;
else
echo "Unsupported platform: ${TARGETPLATFORM}";
exit 1;
fi && rm -rf /var/lib/apt/lists/*

WORKDIR /app

Copy application source and protobuf files

COPY ./src/shipping/ ./ COPY ./pb/ ./proto/

Build application

RUN if [ "
𝑇
𝐴
𝑅
𝐺
𝐸
𝑇
𝑃
𝐿
𝐴
𝑇
𝐹
𝑂
𝑅
𝑀
"
=
"
{BUILDPLATFORM}" ]; then
cargo build --release --features="dockerproto";
elif [ "${TARGETPLATFORM}" = "linux/arm64" ]; then
CARGO_TARGET_AARCH64_UNKNOWN_LINUX_GNU_LINKER=aarch64-linux-gnu-gcc
CC_aarch64_unknown_linux_gnu=aarch64-linux-gnu-gcc
CXX_aarch64_unknown_linux_gnu=aarch64-linux-gnu-g++
cargo build --release --features="dockerproto"
--target aarch64-unknown-linux-gnu &&
cp target/aarch64-unknown-linux-gnu/release/shipping target/release/shipping;
elif [ "${TARGETPLATFORM}" = "linux/amd64" ]; then
CARGO_TARGET_X86_64_UNKNOWN_LINUX_GNU_LINKER=x86_64-linux-gnu-gcc
CC_x86_64_unknown_linux_gnu=x86_64-linux-gnu-g++
CXX_x86_64_unknown_linux_gnu=x86_64-linux-gnu-g++
cargo build --release --features="dockerproto"
--target x86_64-unknown-linux-gnu &&
cp target/x86_64-unknown-linux-gnu/release/shipping target/release/shipping;
else
echo "Unsupported platform: ${TARGETPLATFORM}";
exit 1;
fi

Install gRPC health probe

ENV GRPC_HEALTH_PROBE_VERSION=v0.4.24

RUN wget -qO /bin/grpc_health_probe
https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-${TARGETARCH} &&
chmod +x /bin/grpc_health_probe

Runtime image

FROM debian:bookworm-slim AS release

WORKDIR /app

COPY --from=builder /app/target/release/shipping /app/shipping COPY --from=builder /bin/grpc_health_probe /bin/grpc_health_probe

EXPOSE ${SHIPPING_PORT}

ENTRYPOINT ["/app/shipping"]
