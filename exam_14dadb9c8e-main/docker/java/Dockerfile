FROM python:3.7.6-slim

RUN set -x && \
  apt-get update && \
  mkdir -p /usr/share/man/man1/ && \
  apt-get install -y --no-install-recommends openjdk-11-jdk-headless && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*

CMD ["bash"]
