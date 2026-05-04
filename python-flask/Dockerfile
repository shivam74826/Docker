# ============================================================
# Multi-stage Dockerfile: Python Flask Production Image
# Author: Shivam Kumar Pandey (shivam74826)
# ============================================================

# Stage 1: Builder — install Python deps into a virtual environment
FROM python:3.11-slim AS builder

WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .

RUN python -m venv /opt/venv && \
    /opt/venv/bin/pip install --no-cache-dir --upgrade pip && \
    /opt/venv/bin/pip install --no-cache-dir -r requirements.txt

# ============================================================
# Stage 2: Production — lean runtime image
FROM python:3.11-slim AS production

# Security: non-root user
RUN groupadd -r appgroup && useradd -r -g appgroup appuser

WORKDIR /app

# Copy only the virtual environment (no build tools in final image)
COPY --from=builder /opt/venv /opt/venv

# Copy application source
COPY --chown=appuser:appgroup . .

ENV PATH="/opt/venv/bin:$PATH"
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

USER appuser

EXPOSE 5000

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
    CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:5000/health')" || exit 1

CMD ["gunicorn", "--bind", "0.0.0.0:5000", "--workers", "4", "--timeout", "120", "app:app"]
