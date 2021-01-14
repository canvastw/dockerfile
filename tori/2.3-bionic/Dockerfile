# GENERATED FILE, DO NOT MODIFY!
# To update this file please edit the relevant template and run the generation
# task `build/dockerfile_writer.rb --env development --compose-file docker-compose.yml,docker-compose.override.yml --in build/Dockerfile.template --out Dockerfile`

FROM canvastw/ruby-passenger:2.3-bionic
LABEL MAINTAINER Click-AP

USER root
ENV DEBIAN_FRONTEND=noninteractive
#Install SVN client from official repository
#RUN #echo "deb http://opensource.wandisco.com/ubuntu `lsb_release -cs` svn19" >> /etc/apt/sources.list.d/subversion19.list \
    #&& curl -sS http://opensource.wandisco.com/wandisco-debian.gpg | apt-key add - \
RUN apt-get update -q && apt-get install -yq --no-install-recommends subversion \
    && svn co --non-interactive --no-auth-cache --quiet http://192.168.50.164/svn/derberus/trunk /opt/derberus \
    && chown -R docker:staff /opt/derberus \
#    && sed -i 's/Toriredis/localhost/g' /opt/derberus/config/settings.yml \
    && sed -i 's/localhost/Toriredis/g' /opt/derberus/config/settings.yml \
    && apt-get install -yq --no-install-recommends openjdk-8-jre-headless libreoffice unoconv tzdata \
    # Chinese font
    fontconfig xfonts-utils ttf-wqy-microhei \
    # timezone && timedatectl set-timezone Asia/Taipei \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y tzdata && echo "Asia/Taipei" | tee /etc/timezone && dpkg-reconfigure --frontend noninteractive tzdata \
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
    # After ruby-passenger:2.3-bionic
    && gem install mono_logger -v 1.1.0 \
    && gem install unix-crypt -v 1.3.0 \
    #&& gem install markdown -v 1.2.0 \
    && gem install kramdown -v 2.3.0 \
    && gem install mail -v 2.7.1

USER root
# Nginx Configuration
COPY nginx.conf.erb /opt/nginx/conf/nginx.conf.erb
COPY location.d/* /opt/nginx/location.d/
COPY server.d/*   /opt/nginx/server.d/

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

# docker run -d -p80:80 --name Derberus --link Toriredis:redis moodletw/derverus2020:latest or 
# docker run -d -p80:80 --name Derberus --restart=always \
#  -v /home/docside/data:/opt/derberus/public/data   -v /home/docside/drive:/opt/derberus/public/drive \
#  --link Toriredis:redis moodletw/tory-derberus2020:25
#
# After that, You can do smoke test by:
# QUEUES=* rake resque:work --trace 
# RAILS_ENV=development QUEUES=* rake resque:work --trace 
# rackup -o 0.0.0.0
# http://localhost/api/clients/1/docs?access_key=kiqcmr0dogko09ax
# curl -i -XPOST -F 'access_key=z4zftourk6794ywi' 'http://192.168.50.63/api/clients/1/docs/upload' -F 'filepath=@test1.doc'

USER docker

EXPOSE 80
#ENV PASSENGERROOT=`passenger-config --root`
#ENV RUBYBIN=`which ruby`
WORKDIR /opt/derberus
ENTRYPOINT ["/tini", "--", "/usr/src/entrypoint"]