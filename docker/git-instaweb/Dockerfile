FROM alpine:3.20

RUN apk add --no-cache git lighttpd

WORKDIR /srv/git

EXPOSE 8080

CMD ["/bin/sh", "-lc", "git --version && lighttpd -v && exec /bin/sh"]
