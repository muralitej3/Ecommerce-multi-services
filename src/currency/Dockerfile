
# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

FROM alpine:3.18 AS builder

WORKDIR /build

# Install build dependencies
RUN apk add --no-cache \
    git \
    cmake \
    make \
    g++ \
    grpc-dev \
    protobuf-dev \
    linux-headers

# Receive OpenTelemetry C++ version
ARG OPENTELEMETRY_CPP_VERSION

# Fail early if version is missing
RUN test -n "$OPENTELEMETRY_CPP_VERSION" || \
    (echo "ERROR: OPENTELEMETRY_CPP_VERSION is not set" && exit 1)

# Build OpenTelemetry C++ library
RUN git clone --depth 1 \
    --branch "v${OPENTELEMETRY_CPP_VERSION}" \
    https://github.com/open-telemetry/opentelemetry-cpp \
    && cd opentelemetry-cpp \
    && mkdir build \
    && cd build \
    && cmake .. \
        -DCMAKE_CXX_STANDARD=17 \
        -DCMAKE_POSITION_INDEPENDENT_CODE=ON \
        -DCMAKE_BUILD_TYPE=Release \
        -DBUILD_TESTING=OFF \
        -DWITH_EXAMPLES=OFF \
        -DWITH_OTLP_GRPC=ON \
        -DWITH_ABSEIL=ON \
    && make -j"$(nproc)" install

# Copy Currency application source
COPY ./src/currency /currency
COPY ./pb/demo.proto /currency/proto/demo.proto

# Build Currency application
WORKDIR /currency

RUN mkdir -p build \
    && cd build \
    && cmake .. \
    && make -j"$(nproc)" install


####################################################

FROM alpine:3.18 AS release

# Install runtime dependencies
RUN apk add --no-cache grpc-dev protobuf-dev

WORKDIR /

# Copy compiled libraries and executable
COPY --from=builder /usr/local /usr/local

# Currency service port
ARG CURRENCY_PORT=7000
ENV CURRENCY_PORT=${CURRENCY_PORT}

EXPOSE 7000

ENTRYPOINT ["/usr/local/bin/currency"]
