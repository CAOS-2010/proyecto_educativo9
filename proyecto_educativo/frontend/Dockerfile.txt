# frontend/Dockerfile
FROM python:3.11-slim

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

ENV STREAMLIT_SERVER_HEADLESS=true
ENV STREAMLIT_SERVER_ENABLE_CORS=false
ENV STREAMLIT_SERVER_ENABLE_WEBSOCKET_COMPRESSION=false

RUN useradd -m streamlituser

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    gcc \
    gfortran \
    git \
    curl \
    ca-certificates \
    libopenblas-dev \
    liblapack-dev \
    libatlas-base-dev \
    libblas-dev \
    libffi-dev \
    libssl-dev \
    libxml2-dev \
    libxslt-dev \
    libjpeg-dev \
    zlib1g-dev \
    pkg-config \
    wget \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY frontend/requirements.txt /app/requirements.txt

RUN python -m pip install --upgrade pip wheel setuptools \
 && python -m pip install --no-cache-dir numpy==1.26.4 \
 && python -m pip install --no-cache-dir scipy==1.11.4 \
 && python -m pip install --no-cache-dir -r /app/requirements.txt

COPY frontend /app

RUN chown -R streamlituser:streamlituser /app

USER streamlituser

ARG PORT=8501
ENV PORT=${PORT}

CMD ["bash", "-lc", "streamlit run app.py --server.port ${PORT} --server.address 0.0.0.0"]
