FROM nvidia/cuda:10.0-cudnn7-devel-ubuntu16.04

RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections

# Install some basic utilities
RUN apt-get update && apt-get install -y \
    build-essential \
    bzip2 \
    ca-certificates \
    git \
    curl \
    libx11-6 \
    sudo \
 && rm -rf /var/lib/apt/lists/*

# Create a working directory
RUN mkdir /work
WORKDIR /work

# Create a non-root user and switch to it
RUN adduser --disabled-password --gecos '' --shell /bin/bash user \
 && chown -R user:user /work
RUN echo "user ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/90-user
USER user

# All users can use /home/user as their home directory
ENV HOME=/home/user
RUN chmod 777 /home/user

# Install Miniconda and Python 3.7
ENV CONDA_AUTO_UPDATE_CONDA=false
ENV PATH=/home/user/miniconda/bin:$PATH
RUN curl -sLo ~/miniconda.sh https://repo.continuum.io/miniconda/Miniconda3-4.7.12.1-Linux-x86_64.sh \
 && chmod +x ~/miniconda.sh \
 && ~/miniconda.sh -b -p ~/miniconda \
 && rm ~/miniconda.sh \
 && conda install -y python==3.7.7 \
 && conda clean -ya

# CUDA 10.0-specific steps
RUN conda install -y -c pytorch \
    cudatoolkit=10.0 \
    "pytorch=1.3.1=py3.7_cuda10.0.130_cudnn7.6.3_0" \
    "torchvision=0.4.2=py37_cu100" \
 && conda clean -ya

# Install SMOKE
RUN mkdir -p $HOME/smoke
COPY --chown=user:user smoke $HOME/smoke/smoke
COPY --chown=user:user setup.py $HOME/smoke/setup.py
RUN cd $HOME/smoke && python setup.py build develop
