#This dockerfile also supports tensorflow/tensorflow:latest-jupyter and ubuntu-latest
ARG BASE_IMAGE=pytorch/pytorch:latest
FROM ${BASE_IMAGE}

# Update the system and install necessary software
RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive TZ=Etc/UTC apt-get -y install tzdata
RUN apt-get install -y \
    python3-pip \
    neovim \
    gosu \
    xdg-utils \
    inotify-tools \
    && rm -rf /var/lib/apt/lists/*

# Add Neovim configuration settings
RUN mkdir -p ~/.config/nvim && \
    echo -e 'set expandtab\nset tabstop=4\nset shiftwidth=4' >> ~/.config/nvim/init.vim

# Install Jupyter Notebook and other useful stuff.
# TODO add your additional python dependencies here
RUN pip install \
	jupyter \
	pandas \
	numpy \
	python-docx \
	matplotlib \
	seaborn \
	scikit-learn \
	xgboost \
	lightgbm \
	scipy \
	statsmodels \
	scikit-llm \
	transformers \
	datasets \
	tokenizers \
	huggingface_hub \
	accelerate \
    micrograd \
    tinygrad

# optional installs that cause problems go here (like torchtext) 
ARG OPTIONAL_INSTALLS=false
ENV OPTIONAL_INSTALLS=${OPTIONAL_INSTALLS}
COPY install/optional_deps.sh /optional_deps.sh
RUN chmod +x /optional_deps.sh
RUN /optional_deps.sh 

# Set up the working directory
WORKDIR /workspace

# Change the ownership and permissions of /.local and other directories
# This helps Jupyter notebooks run smoothly
RUN mkdir -p /.local /.jupyter /.config
RUN chmod -R 777 /.local /.config /.jupyter /workspace

# Setup the entrypoint, this is to help open-interpreter run smoothly
# since it wants to be installed in userland
ARG SETUP_ENTRYPOINT=false
ENV SETUP_ENTRYPOINT=${SETUP_ENTRYPOINT}
COPY install/entrypoint.sh /entrypoint.sh
COPY install/install_interpreter.sh /install_interpreter.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

# When the container launches, start a Jupyter Notebook server
CMD ["jupyter", "notebook", "--ip='*'", "--port=8888", "--no-browser"]

