FROM golang:latest AS builder
ARG XRAY_UI_REPO="https://github.com/jvdi/xray-ui"
RUN git clone ${XRAY_UI_REPO} --depth=1
WORKDIR /go/xray-ui
RUN go build

FROM debian:11-slim
LABEL org.opencontainers.image.authors="https://github.com/jvdi"
COPY --from=builder /go/xray-ui/xray-ui /usr/local/bin/xray-ui

ENV DEBIAN_FRONTEND=noninteractive, TZ=Asia/Tehran
RUN apt-get update \
    && apt-get install -y --no-install-recommends -y ca-certificates \
    && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ARG TARGETARCH
COPY --from=teddysun/xray /usr/bin/xray /usr/local/bin/bin/xray-linux-${TARGETARCH}
COPY --from=teddysun/xray /usr/share/xray/ /usr/local/bin/bin/

VOLUME [ "/etc/xray-ui" ]
WORKDIR /usr/local/bin
CMD [ "xray-ui" ]
