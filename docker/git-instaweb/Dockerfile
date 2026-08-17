FROM debian:bookworm-slim

# Use a Debian-based image with Git and the lighttpd runtime needed for smart HTTP.
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      git \
      lighttpd \
      perl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /srv/git

EXPOSE 8080

CMD ["/bin/sh", "-lc", "exec lighttpd -D -f /etc/lighttpd/lighttpd.conf"]
