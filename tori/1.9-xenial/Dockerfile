# task `rake generate:ruby-passenger`
# Tori 1.9-xenial

FROM canvastw/ruby-passenger:1.9-xenial
LABEL MAINTAINER Click-AP

USER root
##Install SVN client from official repository
#RUN echo "deb http://opensource.wandisco.com/ubuntu `lsb_release -cs` svn19" >> /etc/apt/sources.list.d/subversion19.list
#RUN curl -sS http://opensource.wandisco.com/wandisco-debian.gpg | apt-key add - \
RUN apt-get update \
 && apt-get install -y ffmpeg \   
#    && apt-get install -y subversion \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Nginx Configuration
COPY main.d/* /opt/nginx/main.d/
COPY nginx.conf.erb /opt/nginx/conf/nginx.conf.erb

##RUN git clone https://github.com/RubyClickAP/tori2018.git /usr/src/app
##RUN svn co -q --non-interactive --no-auth-cache --username admin --password Jack5899!  http://192.168.1.166:18080/svn/yakitory/branches/security2018/ /usr/src/app
#RUN svn co --non-interactive --no-auth-cache http://192.168.50.164/svn/yakitory/branches/security2018 /opt/yakitory

# entrypoint
COPY entrypoint /usr/src/entrypoint
RUN mkdir -p /opt/yakitory/log \
 && mkdir -p /opt/yakitory/public/data/tmp_uploads \
 && chown -R docker:docker /opt/yakitory \
 && chown docker:docker /usr/src/entrypoint \
 && chmod +x /usr/src/entrypoint

#COPY toriqueue /etc/init.d/toriqueue
RUN mkdir -p /opt/nginx/conf.d \
 && mkdir -p /opt/nginx/main.d \
 && chown -R docker /opt/nginx 
# && chmod +x /etc/init.d/toriqueue
#RUN curl -fSL -o /etc/init.d/toriqueue "https://gist.githubusercontent.com/RubyClickAP/f36239c59eb9d5b2f3ba4eeb7064b02b/raw/1ac2708b5e88e0b5a9a2cce54e28cfcb55f890bf/etc-init.d-toriqueue-ub" \
# && chmod +x /etc/init.d/toriqueue \

USER docker

EXPOSE 80

WORKDIR /opt/yakitory
ENTRYPOINT ["/tini", "--", "/usr/src/entrypoint"]
