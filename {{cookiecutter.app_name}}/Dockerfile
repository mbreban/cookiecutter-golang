# Build Stage
FROM {{cookiecutter.docker_build_image}} AS build-stage

# Because of https://github.com/docker/docker/issues/14914
ENV PATH=$PATH:$GOROOT/bin:$GOPATH/bin

ADD . /go/src/github.com/{{cookiecutter.github_username}}/{{cookiecutter.app_name}}
WORKDIR /go/src/github.com/{{cookiecutter.github_username}}/{{cookiecutter.app_name}}

RUN apk add --no-cache \
    build-base \
    git

RUN make build-alpine

# Final Stage
FROM {{cookiecutter.docker_image}}

ARG GIT_COMMIT
ARG VERSION
LABEL REPO="https://{{cookiecutter.__module_path}}"
LABEL GIT_COMMIT=$GIT_COMMIT
LABEL VERSION=$VERSION

RUN apk add --no-cache --update \
    dumb-init

# Because of https://github.com/docker/docker/issues/14914
ENV PATH=$PATH:/opt/{{cookiecutter.app_name}}/bin

WORKDIR /opt/{{cookiecutter.app_name}}/bin

COPY --from=build-stage /go/src/{{cookiecutter.__module_path}}/bin/{{cookiecutter.app_name}} /opt/{{cookiecutter.app_name}}/bin/
RUN chmod +x /opt/{{cookiecutter.app_name}}/bin/{{cookiecutter.app_name}}

# Create appuser
RUN adduser -D -g '' {{cookiecutter.app_name}}
USER {{cookiecutter.app_name}}

ENTRYPOINT ["/usr/bin/dumb-init", "--"]

CMD ["/opt/{{cookiecutter.app_name}}/bin/{{cookiecutter.app_name}}"]
