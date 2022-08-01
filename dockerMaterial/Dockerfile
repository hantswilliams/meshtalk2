# syntax=docker/dockerfile:1
# PyTorch3D supports up to PyTorch 1.11, 22.02 is the last image to use 1.11 before they
# upgrade to 1.12

FROM nvcr.io/nvidia/pytorch:22.02-py3
WORKDIR /app

ARG DEBIAN_FRONTEND=noninteractive
ENV TZ=Europe/Amsterdam

# Get the basic Linux packages upgraded and get the development tools setup
RUN apt-get update \
    && apt-get upgrade -y\
    && apt-get install -y wget \
    build-essential \
    manpages-dev \
    ca-certificates \
    g++ \
    git \
    libglfw3-dev \
    libgles2-mesa-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && gcc --version \
    && conda upgrade --all -y

# Install PyTorch3D and it's dependencies via Conda
RUN conda install --freeze-installed -c defaults -c fvcore -c iopath -c conda-forge fvcore iopath \
    && git clone https://github.com/facebookresearch/pytorch3d.git \
    && cd pytorch3d \
    && pip install -e .

# Some of our code is using older Jupyter Notebook tooling
# this doesn't work well with some Jupyter Lab bits
RUN conda install -c conda-forge jupyterlab \
    && conda install nodejs \
    && conda install -c defaults -c conda-forge ipympl

# Conda upgrade --all likes to downgrade our numpy install from 1.22 to 1.21
# Because the included PyTorch requires 1.22 to be present to work properly, we need to just force
# reinstall to get 1.22
RUN pip install --force-reinstall numpy==1.22.2

# TODO: Find fix for tensorboard not working on Jupyter Lab 3.x
RUN jupyter labextension update --all \
    && jupyter labextension install @jupyter-widgets/jupyterlab-manager \
    && jupyter labextension install jupyter-matplotlib \
    && jupyter nbextension enable --py widgetsnbextension \
    && jupyter lab build

RUN conda install -c conda-forge pyvista \
    && conda install -c conda-forge ipyvtklink

EXPOSE 8080 8888 8081

CMD tail -f /dev/null


