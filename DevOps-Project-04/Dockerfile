# Stage 1: Builder
FROM python:3.9-slim AS builder

ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY requirements.txt .
RUN pip install --upgrade pip && pip install -r requirements.txt

# Stage 2: Production
FROM python:3.9-slim AS production

ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    DJANGO_SETTINGS_MODULE=hello_world_django_app.settings

RUN apt-get update && apt-get install -y --no-install-recommends \
    libpq5 \
    && rm -rf /var/lib/apt/lists/*

RUN useradd -m django

WORKDIR /app

# Copy installed packages from builder
COPY --from=builder /usr/local/lib/python3.9 /usr/local/lib/python3.9
COPY --from=builder /usr/local/bin /usr/local/bin

# Copy project
COPY . .

# Fix permissions
RUN chown -R django:django /app

USER django

EXPOSE 8000

CMD ["gunicorn", "hello_world_django_app.wsgi:application", "--bind", "0.0.0.0:8000", "--workers", "3"]