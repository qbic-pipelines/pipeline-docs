# Nextflow tower

To be able to follow the Nextflow workflow rusn via tower, you can add Tower access credentials in your Nextflow configuration file (`~/.nextflow/config`) using the following snippet:

```console
tower {
  accessToken = '<YOUR TOKEN>'
  workspaceId = '<YOUR WORKSPACE ID>'
  endpoint = 'http://cfgateway1.zdv.uni-tuebingen.de/api'
  enabled = true
}
```

To submit a pipeline to a different Workspace using the Nextflow command line tool, you can provide the workspace ID as an environment variable. For example

```console
export TOWER_WORKSPACE_ID=000000000000000
```

The workspace ID can be found on the organisation's Workspaces overview page.

For more info on how to use tower please refere to the [Tower docs](https://help.tower.nf/).