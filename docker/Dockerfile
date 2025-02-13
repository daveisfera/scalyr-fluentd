FROM fluent/fluentd:v1.17.1-debian-1.2 AS build

USER root

RUN --mount=type=cache,target=/var/cache/apt/,sharing=locked --mount=type=cache,target=/var/lib/apt/lists/,sharing=locked \
    apt-get update \
 && apt-get install -y --no-install-recommends \
        build-essential \
		ruby-dev \
        zlib1g-dev

WORKDIR /usr/src/app

COPY fluent-plugin-scalyr.gemspec Gemfile Gemfile.lock VERSION /usr/src/app/
RUN bundle install

COPY test lib Rakefile .rubocop.yml /usr/src/app/
RUN bundle exec rubocop
RUN bundle exec rake build
RUN bundle exec rake test

ARG VERSION=
RUN gem install pkg/fluent-plugin-scalyr-${VERSION}.gem


FROM fluent/fluentd:v1.17.1-debian-1.2

USER root

COPY --from=build /usr/local/bundle/ /usr/local/bundle/

USER fluent
