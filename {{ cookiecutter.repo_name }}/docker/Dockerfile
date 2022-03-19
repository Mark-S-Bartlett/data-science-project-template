# Start from a core stack version
FROM jupyter/scipy-notebook:2022-01-03
#399cbb986c6b

LABEL authors="Mark S. Bartlett"
LABEL maintainer="bartlett <mark.bartlettjr@Stantec.com>"

COPY docker/stantec-ca.crt /usr/local/share/ca-certificates/
COPY docker/stantec-ca.crt /etc/ssl/certs/ca-certificates/
COPY --chown=${NB_UID}:${NB_GID} docker/requirements.txt /tmp/
COPY --chown=${NB_UID}:${NB_GID} docker/requirements_pip.txt /tmp/

RUN conda config --set ssl_verify /usr/local/share/ca-certificates/stantec-ca.crt
ENV REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt

USER root

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends --no-install-suggests \
    build-essential \
    grass \
    grass-dev \
    python3-distutils \
    subversion \
    ca-certificates \
    curl \
    apt-transport-https \
    lsb-release gnupg \
    && apt-get autoremove \
    && apt-get clean

RUN apt-get update && apt-get install -y default-jdk 

RUN chmod -R a+rwx $HOME
RUN chown -R $NB_USER .

RUN chmod 644 /usr/local/share/ca-certificates/stantec-ca.crt \
    && chmod 644 /etc/ssl/certs/ca-certificates/stantec-ca.crt \
    && update-ca-certificates
    
RUN curl --insecure -sL https://aka.ms/InstallAzureCLIDeb | sudo bash \
    && apt-get autoremove \
    && apt-get clean

# switch the user
USER $NB_USER

RUN conda config --set ssl_verify /usr/local/share/ca-certificates/stantec-ca.crt

RUN pip install --cert /usr/local/share/ca-certificates/stantec-ca.crt --requirement /tmp/requirements_pip.txt && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

RUN conda install --yes --file /tmp/requirements.txt && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

RUN pip install git+https://github.com/zarch/grass-session.git && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

RUN mkdir -p $HOME/grassdata \
    && grass -c epsg:2163  $HOME/grassdata/test -e \
    && grass $HOME/grassdata/test/PERMANENT --exec g.extension extension=r.basin url=https://trac.osgeo.org/grass/browser/grass-addons/grass7/raster/r.basin?format=zip \
    && grass $HOME/grassdata/test/PERMANENT --exec g.extension extension=r.stream.snap url=https://trac.osgeo.org/grass/browser/grass-addons/grass7/raster/r.stream.snap?format=zip \
    && grass $HOME/grassdata/test/PERMANENT --exec g.extension extension=r.stream.distance url=https://trac.osgeo.org/grass/browser/grass-addons/grass7/raster/r.stream.distance?format=zip \
    && grass $HOME/grassdata/test/PERMANENT --exec g.extension extension=r.stream.basins url=https://trac.osgeo.org/grass/browser/grass-addons/grass7/raster/r.stream.basins \
    && grass $HOME/grassdata/test/PERMANENT --exec g.extension extension=r.hypso url=https://trac.osgeo.org/grass/browser/grass-addons/grass7/raster/r.hypso?format=zip \
    && grass $HOME/grassdata/test/PERMANENT --exec g.extension extension=r.stream.stats url=https://trac.osgeo.org/grass/browser/grass-addons/grass7/raster/r.stream.stats?format=zip \
    && grass $HOME/grassdata/test/PERMANENT --exec g.extension extension=r.stream.order url=https://trac.osgeo.org/grass/browser/grass-addons/grass7/raster/r.stream.order?format=zip \
    && grass $HOME/grassdata/test/PERMANENT --exec g.extension extension=r.width.funct url=https://trac.osgeo.org/grass/browser/grass-addons/grass7/raster/r.width.funct?format=zip \
    && grass $HOME/grassdata/test/PERMANENT --exec g.extension extension=r.traveltime url=https://trac.osgeo.org/grass/browser/grass-addons/grass7/raster/r.traveltime?format=zip

RUN npm config set cafile /usr/local/share/ca-certificates/stantec-ca.crt --global

# Trigger download of delta lake spark jar files
RUN echo "from pyspark.sql import SparkSession" > /tmp/init-delta.py && \
    echo "from delta import *" >> /tmp/init-delta.py && \
    echo 'spark = configure_spark_with_delta_pip(SparkSession.builder).config("spark.jars.packages", ",".join(["io.delta:delta-core_2.12:1.1.0", "org.apache.hadoop:hadoop-azure:3.3.1", "org.apache.hadoop:hadoop-azure-datalake:3.3.1"])).getOrCreate()' >> /tmp/init-delta.py && \
    python /tmp/init-delta.py && \
    rm /tmp/init-delta.py
