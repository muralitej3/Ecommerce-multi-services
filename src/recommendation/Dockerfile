
FROM python:3.12-slim-bookworm AS base

WORKDIR /usr/src/app

# Copy requirements from recommendation directory
COPY ./src/recommendation/requirements.txt ./

# Install dependencies
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -r requirements.txt

# Fix pkg_resources compatibility
RUN pip install --no-cache-dir "setuptools<81"

# Copy application source
COPY ./src/recommendation/ ./

# Install OpenTelemetry instrumentation
RUN opentelemetry-bootstrap -a install

ENV RECOMMENDATION_PORT=1010

ENTRYPOINT ["python", "recommendation_server.py"]
