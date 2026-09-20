# Pinned to Bookworm (Debian 12) for a hardened, well-patched base
FROM python:3.11-slim-bookworm

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# Apply all security patches and upgrade pip
RUN apt-get update && apt-get upgrade -y && rm -rf /var/lib/apt/lists/*
RUN pip install --no-cache-dir --upgrade pip setuptools wheel

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# pip is only needed at build time. Removing it also drops the copies of msgpack
# and setuptools that pip vendors internally, which Trivy flags and which cannot
# be patched from here.
RUN python -m pip uninstall -y pip

COPY main.py .

EXPOSE 5000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]
