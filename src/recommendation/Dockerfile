
FROM python:3.12-slim-bookworm AS base

WORKDIR /usr/src/app

# Copy requirements file
COPY ./src/recommendation/requirements.txt ./

# Install Python dependencies
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -r requirements.txt

# Copy recommendation application source
COPY ./src/recommendation/ ./

# Install OpenTelemetry instrumentation
RUN opentelemetry-bootstrap -a install

# Application port
ENV RECOMMENDATION_PORT=1010

# Start application
ENTRYPOINT ["python", "recommendation_server.py"]

