FROM public.ecr.aws/docker/library/python:3.11-slim

WORKDIR /app

# libatomic1: required by Playwright's bundled Node driver (used by browser-use)
# curl: used by the HEALTHCHECK below
RUN apt-get update && apt-get install -y --no-install-recommends libatomic1 curl \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Create non-root user
RUN useradd -m -u 1000 bedrock_agentcore
USER bedrock_agentcore

# Disable browser-use anonymous telemetry (its posthog client errors on every call)
ENV ANONYMIZED_TELEMETRY=false

EXPOSE 8080
EXPOSE 8000

COPY . .

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:8080/ping || exit 1

CMD ["opentelemetry-instrument", "python", "-m", "weather_agent"]
