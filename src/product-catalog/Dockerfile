
# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0

FROM golang:1.22-alpine AS builder

WORKDIR /usr/src/app/

# Copy Go dependency files
COPY ./src/product-catalog/go.mod ./
COPY ./src/product-catalog/go.sum ./

# Download Go dependencies
RUN go mod download

# Copy application source code
COPY ./src/product-catalog/ ./

# Build application
RUN go build -o product-catalog .

################################################

FROM alpine:latest AS release

WORKDIR /usr/src/app/

# Copy compiled binary
COPY --from=builder /usr/src/app/product-catalog ./

# Copy products directory if required by the application
COPY ./src/product-catalog/products/ ./products/

EXPOSE 8080

ENTRYPOINT ["./product-catalog"]
