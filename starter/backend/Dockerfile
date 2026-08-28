FROM python:3.10-slim

ENV FLASK_RUN_HOST=0.0.0.0
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Install system dependencies required to build Python packages
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        build-essential \
        gcc \
        libpcre3 \
        libpcre3-dev \
        libffi-dev \
        openssl \
        && rm -rf /var/lib/apt/lists/*

# Install pipenv
RUN pip install --no-cache-dir --upgrade pip pipenv

# Copy dependency files first for Docker layer caching
COPY Pipfile Pipfile.lock ./

# Install Python dependencies
RUN pipenv install --system --deploy

# Copy application source code
COPY . .

# Create application user
RUN useradd --create-home --uid 1000 appuser && \
    chown -R appuser:appuser /app

USER appuser

EXPOSE 5000

# Start backend using uWSGI
CMD [
    "uwsgi",
    "--http",
    ":5000",
    "--master",
    "--enable-threads",
    "-s",
    "/tmp/app.sock",
    "--manage-script-name",
    "--mount",
    "/=app:app"
]
