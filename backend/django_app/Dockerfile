# backend/django_app/Dockerfile
FROM python:3.13-slim

ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# OS deps for psycopg2 and build tools
RUN apt-get update \
 && apt-get install -y --no-install-recommends build-essential libpq-dev gcc \
 && rm -rf /var/lib/apt/lists/*

# Copy requirements and install
COPY requirements.txt .
RUN pip install --upgrade pip
RUN pip install -r requirements.txt

# Copy project
COPY . /app

# Collect static files (ensure settings configured to use STATIC_ROOT)
RUN python manage.py collectstatic --noinput || true

# Use a non-root user (optional, safer)
RUN useradd --create-home appuser && chown -R appuser /app
USER appuser

EXPOSE 8000
CMD ["gunicorn", "sneakchat_project.wsgi:application", "--bind", "0.0.0.0:8000", "--workers", "3", "--threads", "2", "--access-logfile", "-"]
