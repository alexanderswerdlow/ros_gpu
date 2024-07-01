FROM silvesterhsu/ros_gpu:noetic-base

#
# ========================== Jupyter ==========================
#
# RUN pip3 install tornado -U
# RUN pip3 install jupyter_contrib_nbextensions jupyter_nbextensions_configurator ipywidgets \
#     && jupyter contrib nbextension install --user \
#     && jupyter nbextensions_configurator enable --user \
#     && jupyter nbextension enable --py widgetsnbextension \
#     && jupyter nbextension enable splitcell/splitcell \
#     && jupyter nbextension enable codefolding/main \
#     && jupyter nbextension enable execute_time/ExecuteTime \
#     && jupyter nbextension enable snippets_menu/main \
#     && jupyter nbextension enable toggle_all_line_numbers/main

#
# ========================== Tool ==========================
#
RUN curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -
RUN sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/3bf863cc.pub

RUN apt-get update \
    && apt-get install -y --no-install-recommends software-properties-common curl \
    && apt-get update \
    && apt-get install -y --no-install-recommends --allow-unauthenticated \
        supervisor \
        openssh-server pwgen sudo vim-tiny \
        net-tools \
        lxde x11vnc xvfb \
        gtk2-engines-murrine ttf-ubuntu-font-family \
        firefox \
        nginx \
        python3-dev build-essential \
        python-dev \
        mesa-utils libgl1-mesa-dri \
        gnome-themes-standard gtk2-engines-pixbuf gtk2-engines-murrine pinta arc-theme \
        dbus-x11 x11-utils \
        terminator \
    && apt-get autoclean \
    && apt-get autoremove \
    && rm -rf /var/lib/apt/lists/*

# user tools
RUN apt-get update && apt-get install -y \
    terminator \
    gedit \
    okular \
    vim \
    nano \
    wget \
    && rm -rf /var/lib/apt/lists/*

# tini for subreap
ENV TINI_VERSION v0.9.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /bin/tini
RUN chmod +x /bin/tini

ADD image /
RUN apt update && sudo apt install python2.7 -y && \
    curl https://bootstrap.pypa.io/pip/2.7/get-pip.py --output get-pip.py && \
    python2 get-pip.py && \
    pip2 install setuptools wheel && pip2 install -r /usr/lib/web/requirements.txt && \
    rm -rf /var/lib/apt/lists/*

RUN cp /usr/share/applications/terminator.desktop /root/Desktop

RUN rm /usr/bin/python && ln -s /usr/bin/python3 /usr/bin/python && \
    rm /usr/local/bin/pip && rm /usr/bin/pip && ln -s /usr/bin/pip3 /usr/bin/pip

RUN pip install networkx==2.8.8
RUN pip install torch==2.2.2 torchvision==0.17.2 torchaudio==2.2.2 --index-url https://download.pytorch.org/whl/cu118
RUN apt-get update && apt-get install -y \
    ripgrep \
    tmux \
    git \
    && rm -rf /var/lib/apt/lists/*

RUN pip install jaxtyping==0.2.19 git+https://github.com/alexanderswerdlow/image_utils.git
RUN pip install lovely-tensors
RUN cat /profile.sh >> /root/.bashrc

# =======================================================================
# TensorBoard
EXPOSE 6006
# Jupyter
EXPOSE 8888
# VNC
EXPOSE 80

WORKDIR /workspace/repos

RUN apt update && apt install -y ros-noetic-grid-map inetutils-ping

RUN pip install scipy gym rosdep cupy-cuda11x einx tqdm diffusers transformers nuplan-devkit ipdb shapely
RUN pip install tensordict --no-deps
RUN python -m cupyx.tools.install_library --cuda 11.x --library cutensor

# USER seel
WORKDIR "/workspace"
ENV HOME=/root SHELL=/bin/bash
SHELL ["/bin/bash", "-c"]
ENTRYPOINT ["/startup.sh"]
