FROM python:3.7.6-slim

RUN set -x && \
  apt-get update && \
  apt-get install -y --no-install-recommends g++-8 && \
  ln -s /usr/bin/gcc-8 /usr/bin/gcc && \
  ln -s /usr/bin/g++-8 /usr/bin/g++ && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*

CMD ["bash"]
