# =============================================================================
# Django Application Dockerfile
# =============================================================================
# This Dockerfile creates a production-ready container for Django applications
# optimized for AWS ECS deployment with security and performance best practices
# =============================================================================

# ------------------------------------------------------------------------------
# Multi-stage build for optimized production image
# ------------------------------------------------------------------------------

#stage-1 builder
FROM python:3.9-slim as builder

#Setting environment variables for Python
ENV PYTHONDONTWRITEBYTECODE 1 \
    PYTHONUNBUFFERED 1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Install build dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt --no-cache-dir

#stage-2 production image
FROM python:3.9-slim as production

# Set environment variables for Python
ENV PYTHONDONTWRITEBYTECODE 1 \
    PYTHONUNBUFFERED 1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

#Install runtime dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    libpq-dev \
    curl \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean

#craete non-root user for security
RUN groupadd -r django && useradd -r -g django django

# Set working directory
WORKDIR /app

#copy application code with appropriate permissions
COPY --chown=django:django . .

#create neccessary directories and set permissions
RUN mkdir -p /app/static /app/media /app/logs  \
    && chown -R django:django /app

#Switch to non-root user
USER django

#Exposse application port
EXPOSE 8000

# Command to run the application using Gunicorn
CMD ["gunicorn", "myproject.wsgi:application", "--bind", "0.0.0.0:8000", "--workers", "3"]

