FROM canvastw/core:bionic
LABEL MAINTAINER Click-AP

ENV RUBY_MAJOR 2.3
ENV BUNDLER_VERSION 2.2.4
ENV RUBYGEMS_VERSION 3.2.4

USER root
RUN mkdir -p /usr/src/app
RUN chown docker:staff /usr/src/app

RUN apt-get update \
 && apt-get install -y --no-install-recommends \
      tar wget unzip \
      libcurl4-openssl-dev \
      autoconf \
      libpq-dev \
      libsqlite3-dev \
      libmysqlclient-dev \
      zlib1g-dev \
      libssl-dev \
      libreadline-dev \
      libyaml-dev \
      libxml2-dev \
      libxslt-dev \
      bison \
      qt4-qmake \
      libqtwebkit-dev \
      libffi-dev \
      net-tools \
      libtcmalloc-minimal4 \
    && apt-get install -y --no-install-recommends libssl1.0-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /usr/local
RUN set -ex \
    && git clone git://github.com/RubyClickAP/rbenv.git /usr/local/rbenv \
    && chgrp -R staff /usr/local/rbenv \
    && chmod -R g+rwxXs /usr/local/rbenv && \
    { \
      echo '# rbenv setup'; \
      echo 'export RBENV_ROOT=/usr/local/rbenv'; \
      echo 'export PATH="$RBENV_ROOT/bin:$PATH"'; \
      echo 'eval "$(rbenv init -)"'; \
    } >> /etc/profile.d/rbenv.sh \
    \
    && echo 'export PATH="~/.gem/ruby/2.3.0/bin:$PATH"' >> /etc/profile.d/rbenv.sh \
    && chmod +x /etc/profile.d/rbenv.sh \
    && . /etc/profile.d/rbenv.sh \
    && git clone https://github.com/sstephenson/ruby-build.git /tmp/ruby-build \
    && cd /tmp/ruby-build && ./install.sh \
    && rbenv install 2.3.8 \
    && rbenv global 2.3.8 \
    && chown -R docker /usr/local/rbenv/shims \
    && rbenv rehash \
    && ruby -v \
    && gem --version \
    && gem update --no-document --system $RUBYGEMS_VERSION \
    && gem install --no-document bundler -v $BUNDLER_VERSION

## gem install --no-document -i /var/lib/gems/$RUBY_MAJOR.0 bundler -v $BUNDLER_VERSION
# /usr/local/rbenv/versions/2.3.8/lib/ruby/gems/2.3.0

ENV BUNDLE_APP_CONFIG /home/docker/.bundle

USER docker
RUN . /etc/profile.d/rbenv.sh \
 && export GEM_HOME="/home/docker/.gem/ruby/$RUBY_MAJOR.0" \
 && export PATH=$GEM_HOME/bin:$PATH \
 && mkdir -p /home/docker/.gem/ruby/$RUBY_MAJOR.0/build_info \
             /home/docker/.gem/ruby/$RUBY_MAJOR.0/cache \
             /home/docker/.gem/ruby/$RUBY_MAJOR.0/doc \
             /home/docker/.gem/ruby/$RUBY_MAJOR.0/extensions \
             /home/docker/.gem/ruby/$RUBY_MAJOR.0/gems \
             /home/docker/.gem/ruby/$RUBY_MAJOR.0/specifications \
 && echo "gem: --user-install --no-document" >> ~/.gemrc \
 && gem install rack \
 && gem uninstall rack # smoke test

ENV GEM_HOME /home/docker/.gem/ruby/$RUBY_MAJOR.0
ENV PATH $GEM_HOME/bin:$PATH
WORKDIR /usr/src/app