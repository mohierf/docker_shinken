# Shinken Docker installation using pip (latest)
FROM        debian:jessie
MAINTAINER  Frédéric Mohier

# Install Shinken, Nagios plugins, apache2 and supervisord
RUN         apt-get update && apt-get install -y --no-install-recommends \
                python-pip \
                python-pycurl \
                python-cherrypy3 \
                nagios-plugins \
                nagios-nrpe-plugin \
                nagios-snmp-plugins \
                libsys-statistics-linux-perl \
                apache2 \
                libapache2-mod-proxy-html \
                supervisor \
                libssl-dev \
                inotify-tools \
                sudo \
                curl \
                git \
                vim \
                ntp

RUN         apt-get autoremove && apt-get clean

## Install and configure thruk
## todo: remove this... make it a separate image
#RUN         curl -s "https://labs.consol.de/repo/stable/RPM-GPG-KEY" | sudo apt-key add - && \
#               echo "deb http://labs.consol.de/repo/stable/debian jessie main" > /etc/apt/sources.list.d/labs-consol-stable.list && \
#               apt-get update && \
#               apt-get install -y thruk && \
#               apt-get clean
#ADD         thruk/thruk_local.conf /etc/thruk/thruk_local.conf
#
## Install and configure Nagvis
## todo: remove this... make it a separate image
#RUN         cd /tmp && \
#               curl "http://www.nagvis.org/share/nagvis-1.9.17.tar.gz" --output /tmp/nagvis-1.9.17.tar.gz && \
#               tar xvzf nagvis-1.9.17.tar.gz && \
#               cd /tmp/nagvis-1.9.17 && \
#               chmod +x install.sh && \
#               ./install.sh && \
#               rm -rf /tmp/nagvis-1.9.17
#
# Configure apache
ADD         shinken/shinken_apache.conf /etc/apache2/conf-available/shinken_apache.conf
RUN         ln -sf /etc/apache2/conf-available/shinken* /etc/apache2/conf-enabled/

# Enable some useful apache modules
RUN         a2enmod proxy_html
RUN         a2enmod proxy_http
RUN         a2enmod xml2enc
RUN         a2enmod slotmem_shm

RUN         update-rc.d -f apache2 remove

# Define some environment variables
ENV SHINKEN_GROUP "shinken"
ENV SHINKEN_USER  "shinken"

# Create shinken system user/group for Shinken application
# Note that the group is set a gid (1024), this to allow easy sharing with the Docker host
ARG SHINKEN_SHARE_ID=1024
RUN groupadd --system --gid $SHINKEN_SHARE_ID $SHINKEN_GROUP
RUN useradd --system $SHINKEN_USER --uid $SHINKEN_SHARE_ID --gid $SHINKEN_SHARE_ID

# ----------
# Location for the git repositories
RUN mkdir -p /repos

# Get and configure Shinken from the git repository. pip module is not up-to-date!
# ----------
ARG MODULE_NAME=shinken
ARG MODULE_GIT_REPO=naparuba/$MODULE_NAME
ARG MODULE_GIT_BRANCH=master
RUN cd /repos && \
      git clone https://github.com/$MODULE_GIT_REPO && \
      cd $MODULE_NAME && \
      git checkout $MODULE_GIT_BRANCH && \
      pip install -r requirements.txt && \
      pip install .

# Get and configure Shinken modules from the git repositories. Many modules are not up-to-date in the shinken.io :/
# ----------
# Location for the git repositories
RUN mkdir -p /repos

# Get the auth-htpasswd module
ARG MODULE_NAME=mod-booster-nrpe
ARG MODULE_GIT_REPO=shinken-monitoring/$MODULE_NAME
ARG MODULE_GIT_BRANCH=master
RUN cd /repos && \
    git clone https://github.com/$MODULE_GIT_REPO && \
    cd $MODULE_NAME && \
    git checkout $MODULE_GIT_BRANCH && \
#    pip install -r requirements.txt && \
    mkdir -p /var/lib/shinken/modules/booster-nrpe && \
    cp -r booster_nrpe/* /var/lib/shinken/modules/booster-nrpe

# Get the graphite module
ARG MODULE_NAME=mod-graphite
ARG MODULE_GIT_REPO=mohierf/$MODULE_NAME
ARG MODULE_GIT_BRANCH=master
RUN cd /repos && \
    git clone https://github.com/$MODULE_GIT_REPO && \
    cd $MODULE_NAME && \
    git checkout $MODULE_GIT_BRANCH && \
#    pip install -r requirements.txt && \
    mkdir -p /var/lib/shinken/modules/graphite && \
    cp -r module/* /var/lib/shinken/modules/graphite

# Get the ui-graphite module
ARG MODULE_NAME=mod-ui-graphite
ARG MODULE_GIT_REPO=mohierf/$MODULE_NAME
ARG MODULE_GIT_BRANCH=master
RUN cd /repos && \
    git clone https://github.com/$MODULE_GIT_REPO && \
    cd $MODULE_NAME && \
    git checkout $MODULE_GIT_BRANCH && \
#    pip install -r requirements.txt && \
    mkdir -p /var/lib/shinken/modules/ui-graphite && \
    ls -al && \
    cp -r module/* /var/lib/shinken/modules/ui-graphite

# Get the retention-mongo module
ARG MODULE_NAME=mod-retention-mongodb
ARG MODULE_GIT_REPO=shinken-monitoring/$MODULE_NAME
ARG MODULE_GIT_BRANCH=master
RUN cd /repos && \
    git clone https://github.com/$MODULE_GIT_REPO && \
    cd $MODULE_NAME && \
    git checkout $MODULE_GIT_BRANCH && \
#    pip install -r requirements.txt && \
    mkdir -p /var/lib/shinken/modules/retention-mongodb && \
    cp -r module/* /var/lib/shinken/modules/retention-mongodb

# Get the livestatus module
ARG MODULE_NAME=mod-livestatus
ARG MODULE_GIT_REPO=shinken-monitoring/$MODULE_NAME
ARG MODULE_GIT_BRANCH=master
RUN cd /repos && \
    git clone https://github.com/$MODULE_GIT_REPO && \
    cd $MODULE_NAME && \
    git checkout $MODULE_GIT_BRANCH && \
#    pip install -r requirements.txt && \
    mkdir -p /var/lib/shinken/modules/livestatus && \
    cp -r module/* /var/lib/shinken/modules/livestatus

# Get the logstore-mongo module
ARG MODULE_NAME=mod-logstore-mongodb
ARG MODULE_GIT_REPO=mohierf/$MODULE_NAME
ARG MODULE_GIT_BRANCH=pymongo3
RUN cd /repos && \
    git clone https://github.com/$MODULE_GIT_REPO && \
    cd $MODULE_NAME && \
    git checkout $MODULE_GIT_BRANCH && \
#    pip install -r requirements.txt && \
    mkdir -p /var/lib/shinken/modules/logstore-mongodb && \
    cp -r module/* /var/lib/shinken/modules/logstore-mongodb

# Get the logs mongo module
# Not necessary if livestatus and logstore-mongodb are installed
# but I install to make some tests... :)
ARG MODULE_NAME=mod-mongo-logs
ARG MODULE_GIT_REPO=shinken-monitoring/$MODULE_NAME
ARG MODULE_GIT_BRANCH=master
RUN cd /repos && \
    git clone https://github.com/$MODULE_GIT_REPO && \
    cd $MODULE_NAME && \
    git checkout $MODULE_GIT_BRANCH && \
#    pip install -r requirements.txt && \
    mkdir -p /var/lib/shinken/modules/mongo-logs && \
    cp -r module/* /var/lib/shinken/modules/mongo-logs

# Get the WebUI module
RUN pwd
ARG MODULE_NAME=mod-webui
# Main repository
#ARG MODULE_GIT_REPO=shinken-monitoring/$MODULE_NAME
#ARG MODULE_GIT_BRANCH=develop
# My (Fred's) repository
ARG MODULE_GIT_REPO=mohierf/$MODULE_NAME
ARG MODULE_GIT_BRANCH=integ
RUN cd /repos && \
    git clone https://github.com/$MODULE_GIT_REPO && \
    cd $MODULE_NAME && \
    git checkout $MODULE_GIT_BRANCH && \
    mkdir -p /var/lib/shinken/modules/webui2 && \
    pip install -r requirements.txt && \
    cp -r module/* /var/lib/shinken/modules/webui2

# Configure Shinken daemons
# ----------
RUN         chown -R shinken:shinken /etc/shinken/

ADD         shinken/shinken.cfg /etc/shinken/shinken.cfg
# Update default configuration
ADD         shinken/daemons/arbiter-master.cfg /etc/shinken/arbiters/arbiter-master.cfg
ADD         shinken/daemons/broker-master.cfg /etc/shinken/brokers/broker-master.cfg
ADD         shinken/daemons/poller-master.cfg /etc/shinken/pollers/poller-master.cfg
ADD         shinken/daemons/reactionner-master.cfg /etc/shinken/reactionners/reactionner-master.cfg
ADD         shinken/daemons/receiver-master.cfg /etc/shinken/receivers/receiver-master.cfg

# Declare new daemons (add a scheduler)
ADD         shinken/daemons/arbiter-*.cfg /etc/shinken/arbiters/
ADD         shinken/daemons/scheduler-*.cfg /etc/shinken/schedulers/
ADD         shinken/daemons/broker-*.cfg /etc/shinken/brokers/
ADD         shinken/daemons/poller-*.cfg /etc/shinken/pollers/
ADD         shinken/daemons/reactionner-*.cfg /etc/shinken/reactionners/
ADD         shinken/daemons/receiver-*.cfg /etc/shinken/receivers/

ADD         shinken/daemons/*.ini /etc/shinken/daemons/

# Configure Shinken modules
# ----------
RUN         chown -R shinken:shinken /etc/shinken/
RUN         chown -R shinken:shinken /var/lib/shinken/modules

COPY        shinken/modules/*.cfg /etc/shinken/modules/
#COPY        shinken/webui_configuration/webui2_worldmap.cfg /var/lib/shinken/modules/webui2/plugins/worldmap/plugin.cfg
RUN         mkdir -p /etc/shinken/custom_configs /usr/local/custom_plugins && \
                ln -sf /etc/shinken/custom_configs/htpasswd.users /etc/shinken/htpasswd.users && \
#                rm -f /etc/thruk/htpasswd && \
#                ln -sf /etc/shinken/htpasswd.users /etc/thruk/htpasswd && \
                chown -R shinken:shinken /etc/shinken/

# Add shinken config watcher to restart arbiter, when changes happen
# ----------
ADD         shinken/watch_shinken_config.sh /usr/bin/watch_shinken_config.sh
RUN         chmod 755 /usr/bin/watch_shinken_config.sh

# Copy extra NRPE plugins and fix permissions
ADD         extra_plugins/* /usr/lib/nagios/plugins/
RUN         cd /usr/lib/nagios/plugins/ && \
                chmod a+x * && \
                chmod u+s check_apt restart_service check_ping check_icmp check_fping apt_update

# Define mountable directories
VOLUME      ["/etc/shinken/custom_configs", "/usr/local/custom_plugins"]

# configure supervisor
ADD         supervisor/conf.d/*.conf /etc/supervisor/conf.d/

# Expose port 80 (apache2)
EXPOSE 80

# Default docker process
CMD ["supervisord", "-c", "/etc/supervisor/supervisord.conf", "-n"]
