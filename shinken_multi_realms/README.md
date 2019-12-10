Shinken multi realms
====================

This image contains:
- shinken installed from the most recent git repository master branch, 
- Web UI from a recent branch,
- some useful Shinken modules 
- standard nrpe plugins + few extra ones, nrpe-booster support and a web server (apache2).

**Note:**
The version of the Web UI is the one from my own repository on the `integ` branch. To change this, modify the variables  in the Dockerfile around line 180 :)

It is intended to be started with a docker compose including Graphite, Thruk and MongoDB services.

**Note:**
The `.env` local file contains some environment variables to configure the docker images.

How to run:

    $ git clone https://github.com/mohierf/docker_shinken.git
    $ cd docker_shinken/shinken_multi_realms

    # Set ownership on the shared Mongo data repository
    # May be useful or not according to the docker local installation... 
    $ sudo chmod 777 -R data/
    
    # Build/pull docker images
    $ docker-compose build
    
    $ docker-compose up

Once done, visit these urls (Default credentials - admin/admin):

* Default Shinken WebUI: <http://localhost/>
* Thruk Web Interface: <http://localhost:8008>
* Graphite Web Interface: <http://localhost:8080>
* Graphs for perf data are available in WebUI2. Sample link: <http://localhost/service/docker_shinken/http_port_7770#graphs>
* MongoDB Web Interface: <http://localhost:8081>

Note:

* [custom_configs/](custom_configs/): Add all your configuration files here.
* [custom_configs/htpasswd.users](custom_configs/htpasswd.users): Define user login credentials here.

When files are modified in the configuration, the Shinken arbiter will be restarted automatically thanks to the supervisord configuration.
  
* The nrpe plugins installation directory is /usr/lib/nagios/plugins.
* If you are using custom NRPE plugins, please mount your plugins directory inside docker container at /usr/local/custom_plugins. You need to define resource paths accordingly.

