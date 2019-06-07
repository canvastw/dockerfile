# task `rake generate:ruby-passenger`
# Tori 1.9-xenial

FROM canvastw/ruby-passenger:1.9-xenial
MAINTAINER Click-AP

USER root

#Install SVN client from official repository
RUN echo "deb http://opensource.wandisco.com/ubuntu `lsb_release -cs` svn19" >> /etc/apt/sources.list.d/subversion19.list
RUN curl -sS http://opensource.wandisco.com/wandisco-debian.gpg | apt-key add - \
    && apt-get update \
    && apt-get install -y subversion \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Nginx Configuration
USER root

COPY nginx.conf.erb /opt/nginx/conf/nginx.conf.erb
COPY main.d/* /opt/nginx/main.d/

#RUN git clone https://github.com/RubyClickAP/tori2018.git /usr/src/app
#RUN svn co -q --non-interactive --no-auth-cache --username admin --password Jack5899!  http://192.168.1.166:18080/svn/yakitory/branches/security2018/ /usr/src/app
RUN svn co --non-interactive --no-auth-cache http://192.168.50.164/svn/yakitory/branches/security2018 /usr/src/app

# entrypoint
COPY entrypoint /usr/src/entrypoint
RUN mkdir -p /opt/yakitory \
 && mkdir -p /opt/yakitory/log \
 && mkdir -p /usr/src/app/log \
 && mkdir -p /opt/yakitory/public/data/tmp_uploads \
 && mkdir -p /usr/src/app/public/data/tmp_uploads \
 && chown -R docker:docker /opt/yakitory \
 && chown -R docker:docker /usr/src/app \
 && chown docker:docker /usr/src/entrypoint \
 && chmod +x /usr/src/entrypoint

RUN mkdir -p /opt/nginx/conf.d \
 && mkdir -p /opt/nginx/main.d \
 && chown -R docker /opt/nginx/

USER docker

EXPOSE 80

WORKDIR /opt/yakitory

#ENTRYPOINT sudo -E /etc/init.d/mvideo2 start && QUEUES=* rake resque:work
CMD ["/tini", "--", "/usr/src/entrypoint"]

