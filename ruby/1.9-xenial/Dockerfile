FROM canvastw/core:xenial
LABEL MAINTAINER Click-AP

ENV RUBY_MAJOR 1.9
ENV RUBY_VERSION 1.9.3-p547
ENV RUBYGEMS_VERSION 1.9.1
ENV BUNDLER_VERSION 1.16.1

USER root
RUN mkdir -p /usr/src/app
RUN chown docker:docker /usr/src/app

RUN apt-get update \
 && apt-get install -y \
      libtool \
      autoconf \
      libtcmalloc-minimal4 \
    && curl -fSL -o ruby.tar.gz "https://github.com/RubyClickAP/ruby-install/archive/v0.4.3.tar.gz" \
    && tar -xzf ruby.tar.gz -C /usr/src \
    && rm ruby.tar.gz \
    && cd /usr/src/ruby-install-0.4.3 \
    && make install \
    && ruby-install -p https://gist.github.com/RubyClickAP/2870213e303a98de07216305949f615f/raw/74b8d615550d46a865921ccc8391fd02b01d2de8/railsexpress-1.9.3-p547.patch ruby 1.9.3-p547 -- --enable-shared CFLAGS="-O3" \
    #&& chmod a-w -R /opt/rubies/ruby-1.9.3-p547 \
    && chmod go-w -R /opt/rubies/ruby-1.9.3-p547/bin \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

#RUN gem update --system $RUBYGEMS_VERSION \
# && gem install -i /var/lib/gems/$RUBY_MAJOR.0 bundler -v $BUNDLER_VERSION
#ENV BUNDLE_APP_CONFIG /home/docker/.bundle

USER docker
RUN echo 'gem: --no-document' >> /home/docker/.gemrc
# && mkdir -p /home/docker/.gem/ruby/$RUBY_MAJOR.0/build_info \
#             /home/docker/.gem/ruby/$RUBY_MAJOR.0/cache \
#             /home/docker/.gem/ruby/$RUBY_MAJOR.0/doc \
#             /home/docker/.gem/ruby/$RUBY_MAJOR.0/extensions \
#             /home/docker/.gem/ruby/$RUBY_MAJOR.0/gems \
#             /home/docker/.gem/ruby/$RUBY_MAJOR.0/specifications

ENV GEM_HOME /opt/rubies/ruby-1.9.3-p547
ENV BUNDLE_PATH="$GEM_HOME" BUNDLE_BIN="$GEM_HOME/bin" BUNDLE_SILENCE_ROOT_WARNING=1 BUNDLE_APP_CONFIG="$GEM_HOME"
ENV PATH $BUNDLE_BIN:$PATH
#RUN mkdir -p "$GEM_HOME" "$BUNDLE_BIN" && chmod 777 "$GEM_HOME" "$BUNDLE_BIN"

WORKDIR /usr/src/app
