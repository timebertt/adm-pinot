# JupyterLab docker image

Custom JupyterLab Docker image including needed Python libraries/packages for our notebook.

## Build

```bash
cd jupyter/docker
docker build -t ghcr.io/timebertt/adm-pinot/jupyter-pinot:$(git rev-parse HEAD | head -c12) --target jupyter-pinot .
```
