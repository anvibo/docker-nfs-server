FROM bitnami/minideb

ENV GCSFUSE_REPO gcsfuse-stretch

RUN set -x && \
    apt-get update && apt-get install -qq -y nfs-kernel-server curl gnupg ca-certificates && \
    rm -rf /var/lib/apt/lists/* && \
    apt-get -qy autoremove && \
    apt-get clean && \
    mkdir /exports

RUN echo "deb http://packages.cloud.google.com/apt $GCSFUSE_REPO main"| tee /etc/apt/sources.list.d/gcsfuse.list
RUN curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

RUN apt-get -q update && apt-get -qq -y install --no-install-recommends gcsfuse \
    && apt-get -qy autoremove \
    && apt-get clean \
    && rm -r /var/lib/apt/lists/*

COPY docker-entrypoint.sh /usr/local/bin/
RUN chmod +rx /usr/local/bin/docker-entrypoint.sh

VOLUME /exports

EXPOSE 2049/tcp
EXPOSE 20048/tcp

ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]
CMD ["/exports"]