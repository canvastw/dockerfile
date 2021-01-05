FROM canvastw/ruby:2.3-bionic
LABEL MAINTAINER Click-AP

USER root
ENV DEBIAN_FRONTEND=noninteractive
#Install Nginx with Passenger from official repository
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    unzip sudo \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* 
    
#RUN echo 'docker ALL=(ALL) NOPASSWD: SETENV: /opt/nginx/sbin/nginx' >> /etc/sudoers

USER docker
# Monk & Dependencies
RUN set -ex \
    && . /etc/profile.d/rbenv.sh \
# Ruby-Passenger
    && gem install bundler -v 2.2.4 \
    && gem install rack-protection -v 2.1.0 \
    && gem install rack -v 2.2.3 \
    #&& gem install passenger \
# Other gem
    && gem install activesupport -v 5.2.4.4 \
    && gem install mime-types -v 3.3.1 \
    && gem install unix-crypt -v 1.3.0 \
# for resque-1.27.4
    && gem install mono_logger -v 1.1.0 \
    && gem install multi_json -v 1.15.0 \
    && gem install tilt -v 2.0.10 \
    # 3Jan2021, Because Multi-dependancy, so gem install insted dependencies
    && gem install ohm -v 3.1.1 \
    && gem install aasm -v 5.1.1 \
    && gem install rest-client -v 2.1.0 \
    && gem install unix-crypt -v 1.3.0

    #&& passenger-install-nginx-module --auto --languages=ruby --prefix=/opt/nginx --nginx-source-dir=/usr/src/nginx-1.15.2 --extra-configure-flags="--with-http_secure_link_module --add-module='/usr/src/upload-module' --add-module='/usr/src/rtmp-module' --add-module='/usr/src/echo-module' --add-module='/usr/src/headers-more-module' " \
    #&& mv /opt/nginx/conf/nginx.conf /opt/nginx/conf/nginx.conf.bk

#RUN passenger-config build-native-support

# Nginx Configuration
USER root
#COPY nginx.conf.erb /opt/nginx/conf/nginx.conf.erb
#COPY main.d/* /opt/nginx/main.d/
#RUN curl -fSL -o /opt/nginx/conf/nginx.conf "https://gist.githubusercontent.com/RubyClickAP/45760ee9c11fab4a7e40371bd524b8b3/raw/6c62aa3ab5d76d12d23cb1fadab8b3315a273a82/mvideo2-conf" \
#RUN curl -fSL -o /etc/init.d/mvideo2 "https://gist.githubusercontent.com/RubyClickAP/8b814c7f8a71c0f428baa74ac6fe3426/raw/18938bda11b361950493bbc9ed73f58801160515/etc-init.d-mvideo2-ub" \
# &&chmod +x /etc/init.d/mvideo2 
#RUN mkdir -p /opt/nginx/conf.d \
# && mkdir -p /opt/nginx/main.d \
# && chown -R docker /opt/nginx \
# && ln -sf /dev/stdout /opt/nginx/logs/access.log \
# && ln -sf /dev/stderr /opt/nginx/logs/error.log 

USER docker
EXPOSE 80
WORKDIR /usr/src/app
#CMD ["/tini", "--", "/usr/src/entrypoint"]
