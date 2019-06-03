# GENERATED FILE, DO NOT MODIFY!
# To update this file please edit the relevant template and run the generation
# task `rake generate:ruby-passenger`
# Ruby-Tory 1.9-xenial

FROM canvastw/ruby:1.9-xenial
MAINTAINER Click-AP

USER root

#Install Nginx with Passenger from official repository
RUN apt-get update \
    && apt-get install -y unzip libcurl4-openssl-dev \
    && curl -fSL -o nginx.tar.gz "http://nginx.org/download/nginx-1.15.2.tar.gz" \
    && mkdir -p /usr/src \
    && tar xvfz nginx.tar.gz -C /usr/src \
    && rm nginx.tar.gz \
#   
    && curl -fSL -o upload-module.zip "https://github.com/RubyClickAP/nginx-upload-module/archive/2.2.zip" \
    && unzip upload-module.zip -d /usr/src \
    && mv /usr/src/nginx-upload-module-* /usr/src/nginx-upload-module \
    && rm upload-module.zip \
#
    && curl -fSL -o rtmp-module.zip "https://github.com/RubyClickAP/nginx-rtmp-module/archive/v1.2.1.zip" \
    && unzip rtmp-module.zip -d /usr/src \
    && mv /usr/src/nginx-rtmp-module-* /usr/src/nginx-rtmp-module \
    && rm rtmp-module.zip \
#
    && curl -fSL -o echo-module.tar.gz "https://github.com/openresty/echo-nginx-module/archive/v0.61.tar.gz" \
    && tar xvfz echo-module.tar.gz -C /usr/src \
    && mv /usr/src/echo-nginx-module-* /usr/src/echo-nginx-module \
    && rm echo-module.tar.gz \
# Monk & Dependencies    
    && gem install --no-ri --no-rdoc monk -v 0.0.7 \
# Ruby-Passenger
    && gem install --no-ri --no-rdoc bundler -v 1.17.3 \
    && gem install --no-ri --no-rdoc rack -v 1.6.11 \
    && gem install --no-ri --no-rdoc passenger \
# Other gem
    && gem install --no-ri --no-rdoc activesupport -v 4.2.6 \
    && gem install --no-ri --no-rdoc mime-types -v 2.99.3 \
    && gem install --no-ri --no-rdoc unix-crypt -v 1.3.0 \
    && passenger-install-nginx-module --auto --languages=ruby --prefix=/opt/nginx --nginx-source-dir=/usr/src/nginx-1.15.2 --extra-configure-flags="--with-http_secure_link_module --add-module='/usr/src/nginx-upload-module' --add-module='/usr/src/nginx-rtmp-module' --add-module='/usr/src/echo-nginx-module' " \
    && chmod -w -R /opt/rubies/ruby-1.9.3-p547
    
USER docker
RUN passenger-config build-native-support

# Nginx Configuration
USER root

COPY entrypoint /usr/src/entrypoint
COPY /opt/nginx/conf/nginx.conf /opt/nginx/conf/nginx.conf.1
#COPY nginx.conf /opt/nginx/conf/nginx.conf
RUN curl -fSL -o /opt/nginx/conf/nginx.conf "https://gist.githubusercontent.com/RubyClickAP/45760ee9c11fab4a7e40371bd524b8b3/raw/6c62aa3ab5d76d12d23cb1fadab8b3315a273a82/mvideo2-conf" \ 
  && curl -fSL -o /etc/init.d/mvideo2 "https://gist.githubusercontent.com/RubyClickAP/8b814c7f8a71c0f428baa74ac6fe3426/raw/18938bda11b361950493bbc9ed73f58801160515/etc-init.d-mvideo2-ub" \
  && chmod +x /etc/init.d/mvideo2 \
  && curl -fSL -o /etc/init.d/toriqueue "https://gist.githubusercontent.com/RubyClickAP/f36239c59eb9d5b2f3ba4eeb7064b02b/raw/93dcd34de9d02361b8aa4f34d3747b07b7cd9e54/etc-init.d-toriqueue-ub" \
  && chmod +x /etc/init.d/toriqueue

#COPY nginx.conf.erb /usr/src/nginx/nginx.conf.erb
#COPY main.d/* /usr/src/nginx/main.d/
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
 && ln -sf /dev/stderr /var/log/nginx/error.log \
 && chown docker:docker -R /opt/nginx

USER docker

EXPOSE 80
CMD ["/tini", "--", "/usr/src/entrypoint"]
