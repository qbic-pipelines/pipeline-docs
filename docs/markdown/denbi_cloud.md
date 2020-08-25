# Running jobs on denbi cloud

## Access to a project

Either request a project to [deNBI cloud](cloud.denbi.de) or ask someone at QBiC if they can create an instance for you in one of their projects.

## Documentation

The documentation on how to create instances and other important things is collected here: https://cloud.denbi.de/wiki/Compute_Center/Tuebingen. This documentation is not great, though, and I found it useful to add a few more notes here.

## Create an instance

Go to the menu: Compute -> Instances -> Launch Instance button.

* Details: add an Instance Name
* Source: select either "Image" for a generic image e.g. CentOS operating system, or "Instance Snapshot" for creating an Instance from a previous snapshot. For running `Nextflow` workflows, you can use the Instance Snapshot `nextflow-singularity` which already has java-jdk12, Nextflow and Singularity and Docker installed (check if Nextflow should be updated with `nextflow self-update`).
* Flavour: select the instance flavour (number of CPUs and RAM).
* Networks: select `denbi_uni_tuebingen_external` network.
* Network Ports: leave empty.
* Security Groups: add `default` AND `external_access`.
* Key Pair: add a new key pair or select yours. Only one Key Pair is allowed per instance and if you lose the private key you will not be able to access the instance any more!
* Rest of fields: leave default.
* Press on `create instance`

You should now see your image being Spawn on the Instance dashboard. It might take several minutes to spawn, especially if created from an Instance Snapshot.

## SSH to an instance

To ssh to an instance, you need the private key of the Key Pair that was used to create the instance, and the instance IP address.

```bash
ssh -i /path/to/private/ssh-key <username>@<IP>
```

The username is the name of the operating system that was used in the image. For the `nextflow-singularity` instance snapshot, it is `centos`.

```bash
ssh -i /path/to/private/ssh-key centos@<IP>
```

## Attach and mount volumes

In order to use an external cinder volume, you need to first create one on the OpenStack interface. Give the volume name and the amount of storage that you need (cannot exceed the total allowed for the project) and create a new empty volume (no image).

* Attach volume: on the volumes dashboard, under Actions for your volume click the arrow and select `manage attachments`. Attach the volume to your running instance.
* Mount volume: Follow the [instructions](https://cloud.denbi.de/wiki/Compute_Center/Tuebingen/#using-cinder-volumes) to mount the attached volume to your instance. Use `sudo` if you get any `permission denied` or `only root can do that` messages. Also you might need to `chown` and `chgrp` the newly created folders to your user and group.
