# builds Haxe 4.3.x from a source tree mounted at /src:
#   docker build -t haxe-build-4.3 extra/docker-build
#   docker run --rm -v "$PWD":/src -w /src haxe-build-4.3 opam exec -- make -s -j8 STATICLINK=1 haxe
FROM ocaml/opam:ubuntu-22.04-ocaml-5.0
USER root
RUN apt-get update && apt-get install -y --no-install-recommends \
      libpcre2-dev zlib1g-dev libmbedtls-dev ninja-build pkg-config curl ca-certificates git make m4 \
    && rm -rf /var/lib/apt/lists/*
# neko, as the CI installs it: from the Haxe Foundation's builds
RUN curl -sSL https://build.haxe.org/builds/neko/linux64/neko_latest.tar.gz -o /tmp/neko.tgz \
    && mkdir -p /opt/neko /usr/local/lib/neko && tar xzf /tmp/neko.tgz -C /opt/neko --strip-components=1 && rm /tmp/neko.tgz \
    && ln -s /opt/neko/neko /opt/neko/nekoc /opt/neko/nekoml /opt/neko/nekotools /usr/local/bin/ \
    && ln -s /opt/neko/libneko.* /usr/local/lib/ && ln -s /opt/neko/include/* /usr/local/include/ \
    && ln -s /opt/neko/*.ndll /usr/local/lib/neko/ && ldconfig
USER opam
WORKDIR /src
COPY --chown=opam haxe.opam /tmp/deps/haxe.opam
RUN opam update -y && cd /tmp/deps && opam pin add haxe . --no-action -y && opam install haxe --deps-only -y && opam list
