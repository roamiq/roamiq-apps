ARG BUILD_FROM
FROM ${BUILD_FROM}
ARG BUILD_VERSION=0.6.41
ENV BUILD_VERSION=${BUILD_VERSION}

# HA Supervisor injects BUILD_ARCH for the target arch of this build. We use it
# to select the matching pre-compiled .so binaries — one add-on repo entry,
# correct binaries on aarch64 / amd64 / armv7.
ARG BUILD_ARCH

RUN apk add --no-cache wget curl ca-certificates && update-ca-certificates && rm -rf /var/cache/apk/*

WORKDIR /app

COPY requirements.txt .
RUN pip3 install --no-cache-dir --break-system-packages -r requirements.txt

# Select this arch's compiled modules. lib/<arch>/ holds run.so + all imports.
COPY lib/${BUILD_ARCH}/ /app/
COPY web/ ./web/

CMD ["/usr/bin/with-contenv", "python3", "-u", "-c", "import run"]
