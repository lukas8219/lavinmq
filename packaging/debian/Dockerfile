ARG build_image=84codes/crystal:latest-ubuntu-24.04

FROM $build_image AS builder
RUN apt-get update && apt-get install -y devscripts help2man lintian debhelper liblz4-dev
ARG version
WORKDIR /usr/src/lavinmq_${version}
COPY Makefile README.md LICENSE NOTICE CHANGELOG.md shard.yml shard.lock ./
COPY extras/lavinmq.service extras/lavinmq.ini extras/
COPY static/ static/
COPY views/ views/
COPY src/ src/
RUN tar -czf ../lavinmq_${version}.orig.tar.gz -C /usr/src lavinmq_${version}
COPY packaging/debian/ debian/
RUN sed -i -E "s/^(lavinmq) \(.*\)/\1 \(${version}-1\)/" debian/changelog
ARG DEB_BUILD_OPTIONS="parallel=2"
RUN debuild -us -uc

FROM ubuntu:24.04 AS test
COPY --from=builder /usr/src/*deb .
RUN apt-get update && apt-get install -y ./*.deb
RUN lavinmq --version

# Copy the deb package to a scratch image, that then can be exported
FROM scratch
ARG version
COPY --from=builder /usr/src/*deb .
