ARG IMAGE_REPO
FROM ${IMAGE_REPO:-lagoon}/commons as commons
FROM docker.elastic.co/kibana/kibana-x-pack:6.1.1

MAINTAINER amazee.io
ENV LAGOON=kibana

USER root

# Copying commons files
COPY --from=commons /lagoon /lagoon
COPY --from=commons /bin/fix-permissions /bin/ep /bin/docker-sleep /bin/
COPY --from=commons /home /home

RUN curl -sL https://github.com/krallin/tini/releases/download/v0.18.0/tini -o /sbin/tini && chmod a+x /sbin/tini

RUN chmod g+w /etc/passwd \
    && mkdir -p /home

# Reproduce behavior of Alpine: Run Bash as sh
RUN rm -f /bin/sh && ln -s /bin/bash /bin/sh

# When Bash is invoked via `sh` it behaves like the old Bourne Shell and sources a file that is given in `ENV`
# When Bash is invoked as non-interactive (like `bash -c command`) it sources a file that is given in `BASH_ENV`
ENV TMPDIR=/tmp TMP=/tmp HOME=/home ENV=/home/.bashrc BASH_ENV=/home/.bashrc

RUN fix-permissions /usr/share/kibana

# tells the local development environment on which port we are running
ENV LAGOON_LOCALDEV_HTTP_PORT=5601

ENV NODE_OPTIONS="--max-old-space-size=200"

ENTRYPOINT ["/sbin/tini", "--", "/lagoon/entrypoints.bash"]
CMD ["/bin/bash", "/usr/local/bin/kibana-docker"]