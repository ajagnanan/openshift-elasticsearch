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

### License
This cartridge is [MIT](http://opensource.org/licenses/MIT) licensed.
