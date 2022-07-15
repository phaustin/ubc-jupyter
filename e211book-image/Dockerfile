ARG OWNER=jovyan
ARG BASE_CONTAINER=$OWNER/minimal-notebook:latest
FROM $BASE_CONTAINER
ENV CONDA_ENV=base
ENV NB_PYTHON_PREFIX=${CONDA_DIR}/envs/${CONDA_ENV}

# set up notebook config for single notebook server
RUN echo "Moving jupyter notebook config to /etc/jupyter" \
    &&  mkdir -p /etc/jupyter
COPY ./jupyter_notebook_config.py /etc/jupyter/

RUN echo "Copying bashrc_conda.txt and condarc.yml into the image" 
COPY --chown=${NB_USER}:${NB_GID} ./bashrc_conda.txt /tmp
COPY --chown=${NB_USER}:${NB_GID} ./condarc.yml /opt/conda/.condarc
COPY --chown=${NB_USER}:${NB_GID} ./condarc.yml ${HOME}/.condarc

RUN echo "make sure work directory is owned by jovyan" \
   && rm -rf ${HOME}/work

# Create "notebook" conda environment
RUN echo "Copying conda-linux-64.lock into homedir..."
COPY --chown=${NB_USER}:${NB_GID} ./conda-linux-64.lock ./requirements.txt ${HOME}/

USER ${NB_UID}

RUN echo "create work directory owned by jovyan" \
   && mkdir -p ${HOME}/work

RUN echo "conda activate ${CONDA_ENV}" >> ${HOME}/.bashrc

RUN echo "install environment from conda-linux-64.lock..." \
    && mamba install--name base --file conda-linux-64.lock \
    && mamba clean -yaf \
    && find ${CONDA_DIR} -follow -type f -name '*.a' -delete \
    && find ${CONDA_DIR} -follow -type f -name '*.pyc' -delete 

# Install pip packages
# remove cache https://github.com/pypa/pip/pull/6391 ?
RUN echo "Installing python packages using pip from requirements.txt..." \
    && ${NB_PYTHON_PREFIX}/bin/pip install --no-cache-dir -r ${HOME}/requirements.txt \
    && rm conda-linux-64.lock requirements.txt

## document exposed port 8888 for jupyter notebook
EXPOSE 8888
