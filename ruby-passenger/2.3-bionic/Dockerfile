FROM canvastw/ruby:2.3-bionic
LABEL MAINTAINER Click-AP

USER root

ENV GEM_HOME /usr/local/rbenv/versions/2.3.8/lib/ruby/gems/2.3.0
ENV PATH $GEM_HOME/bin:$PATH
ENV DEBIAN_FRONTEND=noninteractive

#Install Nginx with Passenger from official repository
RUN apt-get update -q \
    && apt-get install -yq --no-install-recommends sudo \
    # Get nginx 
    && curl --silent -fSL -o nginx.tar.gz "http://nginx.org/download/nginx-1.18.0.tar.gz" \
    && tar xfz nginx.tar.gz -C /usr/src \
    && rm nginx.tar.gz \
# upload-module
    && curl --silent -fSL -o upload-module.zip "https://codeload.github.com/fdintino/nginx-upload-module/zip/2.3.0" \
    && unzip -qq upload-module.zip -d /usr/src \
    && mv /usr/src/nginx-upload-module-* /usr/src/upload-module \
    && rm upload-module.zip \
# rtmp-module
    && curl --silent -fSL -o rtmp-module.zip "https://github.com/RubyClickAP/nginx-rtmp-module/archive/v1.2.1.zip" \
    && unzip -qq rtmp-module.zip -d /usr/src \
    && mv /usr/src/nginx-rtmp-module-* /usr/src/rtmp-module \
    && rm rtmp-module.zip \
# echo-module
    && curl --silent -fSL -o echo-module.tar.gz "https://github.com/openresty/echo-nginx-module/archive/v0.62.tar.gz" \
    && tar xfz echo-module.tar.gz -C /usr/src \
    && mv /usr/src/echo-nginx-module-* /usr/src/echo-module \
    && rm echo-module.tar.gz \
# headers-more-module
    && curl --silent -fSL -o headers-more-module.tar.gz "https://github.com/openresty/headers-more-nginx-module/archive/v0.33.tar.gz" \
    && tar zxf headers-more-module.tar.gz -C /usr/src \
    && mv /usr/src/headers-more-nginx-module-* /usr/src/headers-more-module \
    && rm headers-more-module.tar.gz \
# curl and openssl
    && apt-get install -yq --no-install-recommends libcurl4-openssl-dev \
    && apt-get install -yq --no-install-recommends libssl-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* 

RUN echo 'docker ALL=(ALL) NOPASSWD: SETENV: /opt/nginx/sbin/nginx' >> /etc/sudoers

#USER docker
USER root
# Ruby Dependencies
RUN set -ex \
    && . /etc/profile.d/rbenv.sh \
    && export GEM_HOME="/usr/local/rbenv/versions/2.3.8/lib/ruby/gems/2.3.0" \
    && export PATH=$GEM_HOME/bin:$PATH \
# # gem packages
    && gem install --no-document bundler -v 2.2.4 \
#     && gem install rack -v 2.2.3 \
#     && gem install rack-protection -v 2.1.0 \
# # Other gem
#     && gem install activesupport -v 5.2.4.4 \
#     && gem install mime-types -v 3.3.1 \
#     && gem install unix-crypt -v 1.3.0 \
# # for resque-1.27.4
#     && gem install mono_logger -v 1.1.0 \
#     && gem install multi_json -v 1.15.0 \
#     && gem install tilt -v 2.0.10 \
#     # 3Jan2021, Because Multi-dependancy, so gem install insted dependencies
#     && gem install ohm -v 3.1.1 \
#     && gem install aasm -v 5.1.1 \
#     && gem install rest-client -v 2.1.0 \
#     && gem install unix-crypt -v 1.3.0 \
# Passenger-build
    && gem install --no-document passenger -v 6.0.7 \
    && passenger-install-nginx-module --auto --languages=ruby --prefix=/opt/nginx --nginx-source-dir=/usr/src/nginx-1.18.0 --extra-configure-flags="--with-http_secure_link_module --add-module='/usr/src/upload-module' --add-module='/usr/src/rtmp-module' --add-module='/usr/src/echo-module' --add-module='/usr/src/headers-more-module' " 

#USER docker
#RUN passenger-config build-native-support

# Nginx Configuration
USER root
COPY entrypoint /usr/src/entrypoint
COPY nginx.conf.erb /opt/nginx/conf/nginx.conf.erb
COPY main.d/* /opt/nginx/main.d/
#RUN curl -fSL -o /opt/nginx/conf/nginx.conf "https://gist.githubusercontent.com/RubyClickAP/45760ee9c11fab4a7e40371bd524b8b3/raw/6c62aa3ab5d76d12d23cb1fadab8b3315a273a82/mvideo2-conf" \
#RUN curl -fSL -o /etc/init.d/mvideo2 "https://gist.githubusercontent.com/RubyClickAP/8b814c7f8a71c0f428baa74ac6fe3426/raw/18938bda11b361950493bbc9ed73f58801160515/etc-init.d-mvideo2-ub" \
# &&chmod +x /etc/init.d/mvideo2 
RUN mv /opt/nginx/conf/nginx.conf /opt/nginx/conf/nginx.conf.bk \
    && mkdir -p /opt/nginx/conf.d \
    && mkdir -p /opt/nginx/main.d \
    && mkdir -p /opt/nginx/server.d \
    && mkdir -p /opt/nginx/location.d \
    && chown -R docker /opt/nginx \
    && chown -R docker /usr/src \
    && chmod +x /usr/src/entrypoint \
    && ln -sf /dev/stdout /opt/nginx/logs/access.log \
    && ln -sf /dev/stderr /opt/nginx/logs/error.log 

USER docker

EXPOSE 80
CMD ["/tini", "--", "/usr/src/entrypoint"]
