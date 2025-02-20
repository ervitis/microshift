# BUILD STAGE
FROM registry.access.redhat.com/ubi8/go-toolset as builder

ARG ARCH=amd64
ARG MAKE_TARGET=cross-build-linux-$ARCH
ARG BIN_TIMESTAMP
ARG SOURCE_GIT_TAG

USER root

LABEL name=microshift-build

ENV GOPATH=/opt/app-root GOCACHE=/mnt/cache GO111MODULE=on

WORKDIR $GOPATH/src/github.com/redhat-et/microshift

COPY . .

RUN make clean $MAKE_TARGET SOURCE_GIT_TAG=$SOURCE_GIT_TAG BIN_TIMESTAMP=$BIN_TIMESTAMP

# RUN STAGE
FROM registry.access.redhat.com/ubi8/ubi-minimal:8.4

ARG ARCH=amd64

RUN microdnf install -y \
    policycoreutils-python-utils \
    iptables \
    && microdnf clean all
COPY --from=builder /opt/app-root/src/github.com/redhat-et/microshift/_output/bin/linux_$ARCH/microshift /usr/bin/microshift

ENTRYPOINT ["/usr/bin/microshift"]
CMD ["run"]

# To start:
# podman run --privileged --ipc=host --network=host  \
# -v /var/run:/var/run \
# -v /sys:/sys:ro \
# -v /var/lib:/var/lib:rw,rshared \
# -v /lib/modules:/lib/modules \
# -v /etc:/etc \
# -v /run/containers:/run/containers \
# -v /var/log:/var/log
