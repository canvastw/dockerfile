# GENERATED FILE, DO NOT MODIFY!
# To update this file please edit the relevant template and run the generation
# task `build/dockerfile_writer.rb --env development --compose-file docker-compose.yml,docker-compose.override.yml --in build/Dockerfile.template --out Dockerfile`

FROM canvastw/ruby-passenger:2.3-bionic
LABEL MAINTAINER Click-AP

USER root
#Install SVN client from official repository
#RUN #echo "deb http://opensource.wandisco.com/ubuntu `lsb_release -cs` svn19" >> /etc/apt/sources.list.d/subversion19.list \
    #&& curl -sS http://opensource.wandisco.com/wandisco-debian.gpg | apt-key add - \
RUN apt-get update -q && apt-get install -yq --no-install-recommends subversion openjdk-8-jre-headless \
    && svn co --non-interactive --no-auth-cache --quiet http://192.168.50.164/svn/derberus/trunk /opt/derberus \
    && chown -R docker:staff /opt/derberus \
#    && sed -i 's/Toriredis/localhost/g' /opt/derberus/config/settings.yml \
    && sed -i 's/localhost/Toriredis/g' /opt/derberus/config/settings.yml \
    && apt-get install -yq --no-install-recommends redis unoconv \
#    && timedatectl set-timezone Asia/Taipei \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

USER docker
RUN echo 'gem: --user-install --no-document' > ~/.gemrc \
#    if [ -e /usr/local/ lib/gems/$RUBY_MAJOR.0/gems/* ]; then BUNDLER_INSTALL="-i /var/lib/gems/$RUBY_MAJOR.0"; fi \
#    && export RBENV_ROOT=/usr/local/rbenv \
#    && export PATH="$RBENV_ROOT/bin:$PATH" \
#    && eval "$(rbenv init -)" \
    && RBENV_ROOT=/usr/local/rbenv \
    PATH="$GEM_HOME/bin:$RBENV_ROOT/bin:/usr/local/rbenv/shims:$PATH" \
    && gem install dependencies -v 0.0.9 \
    && gem install sinatra -v 2.1.0 \
    && gem install sinatra-contrib -v 2.1.0 \
    && gem install redis -v 4.2.5 \
    && gem install redis-namespace -v 1.6.0 \
    && gem install rest-client -v 2.1.0 \
    && gem install haml -v 5.2.1 \
    && gem install nest -v 3.2.0 \
    && gem install stal -v 0.3.0 \
    && gem install activesupport -v 5.2.4.4 \
    && gem install mono_logger -v 1.1.0 \
    && gem install unix-crypt -v 1.3.0

USER root
# Nginx Configuration
COPY nginx.conf.erb /opt/nginx/conf/nginx.conf.erb
COPY location.d/* /opt/nginx/location.d/
COPY server.d/*   /opt/nginx/server.d/

##RUN git clone https://github.com/RubyClickAP/tori2018.git /usr/src/app
##RUN svn co -q --non-interactive --no-auth-cache --username admin --password Jack5899!  http://192.168.1.166:18080/svn/yakitory/branches/security2018/ /usr/src/app
#RUN svn co --non-interactive --no-auth-cache http://192.168.50.164/svn/yakitory/branches/security2018 /opt/yakitory

# entrypoint
COPY entrypoint /usr/src/entrypoint
RUN mkdir -p /opt/derberus/log \
    && mkdir -p /opt/derberus/public/drive \
    && mkdir -p /opt/derberus/public/data/test_docs \
    && mkdir -p /opt/derberus/public/data/tmp_uploads \
    && chown -R docker:docker /opt/derberus \
    && chown docker:docker /usr/src/entrypoint \
    && chmod +x /usr/src/entrypoint \
    && rm /opt/derberus/Gemfile

#COPY toriqueue /etc/init.d/toriqueue
#RUN chmod +x /etc/init.d/toriqueue \
    # && curl -sfSL -o /etc/init.d/toriqueue "https://gist.githubusercontent.com/RubyClickAP/f36239c59eb9d5b2f3ba4eeb7064b02b/raw/1ac2708b5e88e0b5a9a2cce54e28cfcb55f890bf/etc-init.d-toriqueue-ub" \
    # && chmod +x /etc/init.d/toriqueue \
# Run docker: 
# docker run -d -p80:80 --name tori2derberus --link Toriredis:redis canvastw/tori-derberus:2.3-bionic
# After run, You can do smoke test by:
# QUEUES=* rake resque:work --trace 
# rackup -o 0.0.0.0

USER docker

EXPOSE 80
#ENV PASSENGERROOT=`passenger-config --root`
#ENV RUBYBIN=`which ruby`
WORKDIR /opt/derberus
ENTRYPOINT ["/tini", "--", "/usr/src/entrypoint"]