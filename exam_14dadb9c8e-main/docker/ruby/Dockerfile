FROM python:3.7.6-slim

ARG ruby_ver="2.6.5"
ARG build_pkgs="curl make g++-8"
RUN set -x && \
  apt-get update && \
  apt-get install -y --no-install-recommends ${build_pkgs} && \
  ln -s /usr/bin/gcc-8 /usr/bin/gcc && \
  ln -s /usr/bin/g++-8 /usr/bin/g++ && \
  curl -o ruby-source.tar.gz https://cache.ruby-lang.org/pub/ruby/${ruby_ver%.*}/ruby-${ruby_ver}.tar.gz && \
  tar -xzf ruby-source.tar.gz && \
  cd ruby-${ruby_ver} && \
  ./configure --disable-install-doc && make -j $(nproc) && make install && \
  cd .. && \
  rm -rf ruby-${ruby_ver} ruby-source.tar.gz && \
  apt-get -y remove --purge ${build_pkgs} && \
  apt-get autoremove -y && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*

CMD ["bash"]
