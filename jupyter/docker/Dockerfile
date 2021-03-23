FROM jupyter/scipy-notebook:3395de4db93a AS jupyter-pinot

LABEL org.opencontainers.image.source=https://github.com/timebertt/adm-pinot

COPY requirements.txt .

RUN pip install -r requirements.txt
