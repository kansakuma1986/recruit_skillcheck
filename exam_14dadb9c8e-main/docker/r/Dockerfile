FROM python:3.7.6-slim

ENV SNAPSHOT_DATE 2020-01-20
ENV CRAN_SOURCE https://mran.microsoft.com/snapshot/${SNAPSHOT_DATE}
ENV RETICULATE_PYTHON /usr/local/bin/python
ENV KERAS_IMPLEMENTATION keras
COPY install_pkgs.R .
COPY install_r.sh .
RUN set -x \
  && apt-get update -y \
  && apt-get install -y --no-install-recommends \
    r-cran-tidyverse \
    libxml2-dev \
    libcairo2-dev \
    libsqlite3-dev \
    liblapack-dev \
    libblas-dev \
    gfortran \
    make \
    cmake \
    g++ \
    git \
    libgsl-dev \
  && echo "options(repos = c(CRAN='${CRAN_SOURCE}'), download.file.method = 'libcurl')" >> /usr/lib/R/etc/Rprofile.site \
  && sh install_r.sh \
  && apt-get -y remove --purge make cmake g++ git \
  && apt-get autoremove -y \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

CMD ["bash"]
