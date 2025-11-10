# frontend/Dockerfile  — build context: ./frontend
FROM python:3.11-slim

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

# crear un usuario no-root
RUN useradd -m streamlituser

# instalar dependencias del sistema necesarias para algunos paquetes científicos
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential gcc gfortran git curl ca-certificates \
    libopenblas-dev liblapack-dev libatlas-base-dev libblas-dev \
    libffi-dev libssl-dev libxml2-dev libxslt-dev libjpeg-dev zlib1g-dev \
    pkg-config wget \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# copiar sólo requirements primero para aprovechar la cache docker
COPY requirements.txt /app/requirements.txt

# actualizar pip y instalar dependencias básicas; evitar compilar paquetes pesados en Render comentándolos en requirements si causa fallos
RUN python -m pip install --upgrade pip wheel setuptools \
 && python -m pip install --no-cache-dir numpy==1.26.4 \
 && python -m pip install --no-cache-dir scipy==1.11.4 \
 && python -m pip install --no-cache-dir -r /app/requirements.txt

# copiar TODO el contenido del frontend (construir con contexto = ./frontend)
COPY . /app


ARG PORT=8501
ENV PORT=${PORT}

CMD ["bash", "-lc", "streamlit run app.py --server.port ${PORT} --server.address 0.0.0.0"]
