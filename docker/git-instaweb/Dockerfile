FROM debian:bookworm-slim

# Alpine's git package does not include the `git instaweb` subcommand,
# so use a Debian-based image to ensure the command is available.
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
      git \
      lighttpd \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /srv/git

EXPOSE 8080

CMD ["/bin/sh", "-lc", "git --version && lighttpd -v && exec /bin/sh"]
