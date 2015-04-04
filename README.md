OpenShift Elasticsearch Cartridge
=================================
This cartridge provides an Elasticsearch cluster as a standalone application with a Kibana 4.0 dashboard. Kibana is served on the root `/` and Elasticsearch on `/elasticsearch`

To create your Elasticsearch app, run:

    rhc app-create https://cartreflect-claytondev.rhcloud.com/github/ajagnanan/openshift-elasticsearch -a <app>

If you want to create a Elasticsearch cluster, append the flag `--scaling`:

    rhc app-create https://cartreflect-claytondev.rhcloud.com/github/ajagnanan/openshift-elasticsearch -a <app> --scaling

### Adding extra nodes to cluster
To add more nodes to the cluster, simply add more gears:

    rhc cartridge-scale -a <app> elasticsearch <number of total gears you want>

### Plugins
To install Elasticsearch plugins, edit the `plugins.txt` file, commit, and push your changes.

    cd elasticsearch
    vim plugins.txt
    git commit -a -m 'add plugin x'
    git push

You can also install plugins from a .zip file. Simply place it inside dir `plugins/`, git add, commit and push.

Plugin urls will be served off of `/elasticsearch/_plugin/<name>/`.

### Configuration

#### Elasticsearch
Elasticsearch configuration is built on-the-fly with the file `config/elasticsearch.yml.erb`, concatenated with any other files found in that same dir (except for `logging.yml` and `elasticsearch.in.sh`). Files ending with `.erb` will be pre-processed using ruby's erb command.

#### Nginx
Nginx is configured by editing the file `nginx.conf.erb` from the git repo's root.

#### Kibana
Kibana is configured by editing the file `kibana.yml.erb` from the git repo's root.

#### Security

Authentication and authorization is handled by elastic.co. The two plugins needed are `license` and `shield`. The steps to enable security are:

  - uncomment these two plugins in your repo's `plugins.txt` file

    ```
    elasticsearch/license/latest
    elasticsearch/shield/latest
    ```
  - uncomment the credential lines in `kibana.yml.erb`
    ```
    kibana_elasticsearch_username: kibana
    kibana_elasticsearch_password: elastic
    ```
  - push repo
    ```
    git commit -a -m 'adding authentication and authorization'
    git push
    ```

The `esusers` utility can be run with the following command:

  
  ```
  $OPENSHIFT_ELASTICSEARCH_DIR/usr/bin/shield/esusers
  e.g. $OPENSHIFT_ELASTICSEARCH_DIR/usr/bin/shield/esusers useradd admin -r admin
  ```

A default `kibana` user is bootstrapped so that Kibana will work.

Caveat:
The `config` directory that elasticsearch is using is located in `$OPENSHIFT_REPO_DIR/config`, but the `esusers` script updates the `$OPENSHIFT_ELASTICSEARCH_DIR/usr/config` directory. For now, the following can be done in order for credentials to be picked up:

  ```
  cp -R $OPENSHIFT_ELASTICSEARCH_DIR/usr/config/shield $OPENSHIFT_REPO_DIR/config
  ```

Do note that this will overwrite the bootstrapped configs, so re-adding the `kibana` user is necessary.

### Updates

Only Kibana is upgradeable. The setup looks at an environment variable to handle the upgrade.

The steps are as follows:

  - rhc set-env KIBANA_VERSION=4.0.2 -a <app> --namespace <domain>
  - trigger a `deploy` by pushing a change with git

Note: only Kibana 4 is supported by this cartridge

### License
This cartridge is [MIT](http://opensource.org/licenses/MIT) licensed.
