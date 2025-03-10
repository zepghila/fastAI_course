FROM ubuntu:22.04

ARG USERNAME=devuser
ARG USER_UID=1000
ARG USER_GID=1000

# Install system dependencies (removed g++ since only Python is needed)
RUN apt-get update -y && apt-get install -y \
    git \
    sudo \
    python3 \
    python3-pip \
    libgl-dev \
    libsm6 \
    libxrender1 \
    libxext6 \
    libglib2.0-0

# Create a non-root user with sudo privileges
RUN if getent group $USER_GID; then \
        groupname=$(getent group $USER_GID | cut -d: -f1); \
        useradd -ms /bin/bash --uid $USER_UID --gid $USER_GID $USERNAME; \
        else \
    groupadd --gid $USER_GID $USERNAME && \
        useradd -ms /bin/bash --uid $USER_UID --gid $USER_GID $USERNAME; \
        fi && \
        echo "$USERNAME:$USERNAME" | chpasswd && \
        usermod -aG sudo $USERNAME && \
        echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/$USERNAME

# Upgrade pip
RUN pip install --upgrade pip

# Install minimal runtime Python dependencies from requirements
COPY fastai-requirements.txt fastai-requirements.txt
RUN pip install -r fastai-requirements.txt

# Install developement tools dependencies from dev-requirements
COPY dev-requirements.txt dev-requirements.txt
RUN pip install -r dev-requirements.txt

# Install JupyterLab (try jplab, replace with "notebook" if have problems and want to switch to classic Jupyter)
RUN pip install jupyterlab

# Optionally, copy and install development dependencies
# COPY dev-requirements.txt dev-requirements.txt
# RUN pip install -r dev-requirements.txt

USER $USERNAME
WORKDIR /home/$USERNAME

# Expose port 8888 so you can access Jupyter from your host machine
EXPOSE 8888

# Start JupyterLab on container startup
CMD ["jupyter", "lab", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root"]
