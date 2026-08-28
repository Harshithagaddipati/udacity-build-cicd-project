FROM python:3.10-bookworm-slim

ENV FLASK_RUN_HOST=0.0.0.0
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    gcc \
    libpcre3 \
    libpcre3-dev \
    libffi-dev \
    libssl-dev \
    && rm -rf /var/lib/apt/lists/*

# Install Pipenv
RUN pip install --no-cache-dir pipenv

# Copy dependency files first
COPY Pipfile Pipfile.lock ./

# Install Python dependencies
RUN pipenv install --system --deploy

# Copy application source code
COPY . .

# Create non-root user
RUN useradd --create-home --uid 1000 app \
    && chown -R app:app /app

USER app

EXPOSE 5000

CMD ["uwsgi", "--http", ":5000", "--master", "--enable-threads", "-s", "/tmp/app.sock", "--manage-script-name", "--mount", "/=app:app"]
