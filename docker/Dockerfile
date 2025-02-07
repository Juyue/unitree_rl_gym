# https://docs.nvidia.com/deeplearning/frameworks/pytorch-release-notes/rel-23-01.html. Ubuntu 20.04, Cuda 12.1
FROM nvcr.io/nvidia/pytorch:23.01-py3

## Add personal settings
ENV LANG=en_US.UTF-8
# Prevent stop building ubuntu at time zone selection.
ENV DEBIAN_FRONTEND=noninteractive

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN echo "Acquire { https::Verify-Peer false }" > /etc/apt/apt.conf.d/99verify-peer.conf \
    && apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --allow-unauthenticated ca-certificates \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        curl \
        git \
        zsh \
        neovim \
        unzip \
        wget \
        rsync \
        vim \
        ninja-build \
        cmake \
        build-essential \
        xauth \
        openssh-server \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*


# Install fzf
RUN git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf && /root/.fzf/install --all

# Install oh-my-zsh and Install Powerlevel10k and zsh-syntax-highlighting
# hadolint ignore=SC2086
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended && \
    git clone --depth=1 https://github.com/romkatv/powerlevel10k.git "${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k" && \
    git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions && \
    git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

RUN curl -o ~/Miniforge3-$(uname)-$(uname -m).sh -L https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-$(uname)-$(uname -m).sh && \
    bash ~/Miniforge3-$(uname)-$(uname -m).sh -b -p /opt/conda

ENV PATH=/opt/conda/bin:${PATH}
RUN conda update -n base conda && \
    conda install -n base conda-libmamba-solver && \
    conda config --set solver libmamba

# Create unitree-rl environment
COPY ./environment.yml /tmp/environment.yml
RUN conda env create -f /tmp/environment.yml
RUN conda init bash

RUN wget https://developer.nvidia.com/isaac-gym-preview-4 && \
    tar -xvf isaac-gym-preview-4 && \
    cd isaacgym/python && \
    conda run -n unitree-rl pip install -e .

# Install rsl_rl
RUN git clone https://github.com/leggedrobotics/rsl_rl.git && \
    cd rsl_rl && \
    git checkout v1.0.2 && \
    conda run -n unitree-rl pip install -e .


# Install unitree_rl_gym
RUN git clone https://github.com/unitreerobotics/unitree_rl_gym.git && \
    cd unitree_rl_gym && \
    conda run -n unitree-rl pip install -e .

# Install unitree_sdk2py
RUN git clone https://github.com/unitreerobotics/unitree_sdk2_python.git && \
    cd unitree_sdk2_python && \
    conda run -n unitree-rl pip install -e .

# # Install glfw and xvfb for polyscope
# RUN apt-get update && \
#     DEBIAN_FRONTEND=noninteractive apt-get install -y --allow-unauthenticated ca-certificates && \
#     DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends && \
#     apt-get install -y libglfw3-dev && \
#     apt-get install -y xvfb
# ENV DISPLAY=:99
# RUN Xvfb :99 -screen 0 1024x768x24 > /dev/null 2>&1 &

# WORKDIR /root
