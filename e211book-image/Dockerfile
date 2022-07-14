ARG OWNER=jupyter
ARG BASE_CONTAINER=$OWNER/minimal-notebook:latest
FROM $BASE_CONTAINER
ENV CONDA_ENV=notebook 
ENV NB_PYTHON_PREFIX=${CONDA_DIR}/envs/${CONDA_ENV}

# set up notebook config for single notebook server
RUN mkdir -p /etc/jupyter
COPY ./jupyter_notebook_config.py /etc/jupyter/

RUN echo "Copying bashrc_conda.txt and condarc.yml into the image" 
COPY --chown=${NB_USER}:${NB_GID} ./bashrc_conda.txt /tmp
COPY --chown=${NB_USER}:${NB_GID} ./condarc.yml /opt/conda/.condarc
COPY --chown=${NB_USER}:${NB_GID} ./condarc.yml ${HOME}/.condarc

# Create "notebook" conda environment
RUN echo "Copying conda-linux-64.lock into homedir..."
COPY --chown=${NB_USER}:${NB_GID} ./conda-linux-64.lock ./requirements.txt ${HOME}

RUN echo "conda activate ${CONDA_ENV}" >> ${HOME}/.bashrc

RUN echo "Moving jupyter notebook config to /etc/jupyter" \

RUN echo "Creating environment from conda-linux-64.lock..." \
    && mamba create --name ${CONDA_ENV} --file conda-linux-64.lock \
    && mamba clean -yaf \
    && find ${CONDA_DIR} -follow -type f -name '*.a' -delete \
    && find ${CONDA_DIR} -follow -type f -name '*.pyc' -delete \
    && rm conda-linux-64.lock requirements.txt

# Install pip packages
# remove cache https://github.com/pypa/pip/pull/6391 ?
RUN echo "Installing python packages using pip from requirements.txt..." \
    && ${NB_PYTHON_PREFIX}/bin/pip install --no-cache-dir -r requirements.txt

## document exposed port 8888 for jupyter notebook
EXPOSE 8888
