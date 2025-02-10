# syntax=docker/dockerfile:1
### It is important to put this syntax specification at the start!!
### https://docs.docker.com/build/dockerfile/frontend/
### https://stackoverflow.com/questions/75309371/dockerfile-with-heredoc-running-in-bash

### ARG and ENV declaration and initialisation
ARG UBUNTU_VER_LATEST=latest
ARG UBUNTU_VER_PENULTIMATE=jammy

### Base image
FROM ubuntu:${UBUNTU_VER_PENULTIMATE} AS base

### Installation phase

###### Utilities install
RUN <<EOF
apt update
apt install -y bash wget zsh
EOF

###### Setup oh-my-zsh (Zsh framework)
RUN <<EOF
bash -c "$(wget https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
echo 'PROMPT=%B%F{blue}[DOCKER]%f%b$PROMPT' >> /root/.zshrc
EOF

#######################################
### Stage 2: Import Python Installation
#######################################
FROM python:3.10.16-bookworm AS python_build
# This image provides a full Python 3.10 environment.
# (We’ll copy its /usr/local directory to get the installed Python binaries and libraries.)

#########################################
### Stage 3: Import Jupyter/Conda Setup
#########################################
FROM jupyter/base-notebook AS jupyter_build
# This image is pre-configured with a Conda environment in /opt/conda
# and comes with Jupyter preinstalled.

#########################################
### Stage 4: Final Image Assembly
#########################################
FROM base AS final

# --- Copy Python from python_build ---
COPY --from=python_build /usr/local /usr/local
ENV PATH="/usr/local/bin:${PATH}"

# --- Copy Conda/Jupyter from jupyter_build ---
COPY --from=jupyter_build /opt/conda /opt/conda
ENV PATH="/opt/conda/bin:${PATH}"

##########################################################
### Section: Install Additional Python Libraries via Pip
##########################################################
COPY requirements.txt /tmp/requirements.txt
RUN pip install -r /tmp/requirements.txt

# Set up auto run commands during ZSH launch
RUN <<EOF
echo 'jupyter notebook --ip=0.0.0.0 --no-browser --allow-root &' >> /root/.zshrc
EOF

### Metadata
LABEL \
    author="jya0 <jyao@student.42abudhabi.ae>" \
    maintainer="jya0 <jyao@student.42abudhabi.ae>" \
    version="1.1" \
    description="This Dockerfile builds a dev environment to ."
