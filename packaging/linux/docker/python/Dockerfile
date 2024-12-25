ARG BASE_IMAGE=keybaseio/client:latest
FROM $BASE_IMAGE AS base

# TINI setup
ENV TINI_VERSION v0.18.0
COPY packaging/linux/docker/tini_key.asc /tini_key.asc
RUN curl -fsSL https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini -o /tini && \
    curl -fsSL https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini.asc -o /tini.asc && \
    gpg --import /tini_key.asc && \
    rm /tini_key.asc && \
    gpg --batch --verify /tini.asc /tini && \
    chmod +x /tini && \
    rm /tini.asc

# GOSU setup
ENV GOSU_VERSION 1.11
COPY packaging/linux/docker/gosu_key.asc /gosu_key.asc
RUN curl -fsSL https://github.com/tianon/gosu/releases/download/${GOSU_VERSION}/gosu-amd64 -o /usr/local/bin/gosu && \
    curl -fsSL https://github.com/tianon/gosu/releases/download/${GOSU_VERSION}/gosu-amd64.asc -o /usr/local/bin/gosu.asc && \
    gpg --import /gosu_key.asc && \
    rm /gosu_key.asc && \
    gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu && \
    chmod +x /usr/local/bin/gosu && \
    rm /usr/local/bin/gosu.asc

# Copy scripts and binaries from base image
COPY --from=base /usr/bin/entrypoint.sh /usr/bin/entrypoint.sh
RUN chmod +x /usr/bin/entrypoint.sh

RUN useradd --create-home --shell /bin/bash keybase
VOLUME [ "/home/keybase/.config/keybase", "/home/keybase/.cache/keybase" ]

COPY --from=base /usr/bin/keybase /usr/bin/keybase
COPY --from=base /usr/bin/keybase.sig /usr/bin/keybase.sig
COPY --from=base /usr/bin/kbfsfuse /usr/bin/kbfsfuse
COPY --from=base /usr/bin/kbfsfuse.sig /usr/bin/kbfsfuse.sig
COPY --from=base /usr/bin/git-remote-keybase /usr/bin/git-remote-keybase
COPY --from=base /usr/bin/git-remote-keybase.sig /usr/bin/git-remote-keybase.sig
ENTRYPOINT ["/tini", "--", "/usr/bin/entrypoint.sh"]
