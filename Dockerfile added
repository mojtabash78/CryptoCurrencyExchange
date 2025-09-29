# ---------- Build Stage ----------
FROM python:3.12-slim AS builder

# Set environment variables
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Install build dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy only requirements first (layer caching)
COPY requirements.txt .

# Install dependencies into /install
RUN pip install --no-cache-dir --prefix=/install -r requirements.txt

# Copy project files
COPY ./Exchange /app/Exchange

# ---------- Final Stage ----------
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Copy installed Python packages from builder
COPY --from=builder /install /usr/local

# Copy project files
COPY --from=builder /app/Exchange /app/Exchange

WORKDIR /app/Exchange

EXPOSE 8000

# Run Django dev server (for simplicity)
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
