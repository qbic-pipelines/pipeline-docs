# Updating igenomes

From time to time, we might need to update the igenomes folder.
The RDDS team leader and sys admin will take care of it so if you are missing some reference files, contact them.

Steps:

* You will need AWS credentials.
* Install miniconda and the `awscli` within miniconda in the `cfc` cluster.
* Configure the AWS cli:

  ```bash
  ~/miniconda/bin/aws configure
  ```

  Provide your AWS key ID and secret.

* Right now we have `10TB` of space for the `igenomes` folder, so please check the size of teh bucket prior to syncing:

  ```bash
  ~/miniconda/bin/aws s3 ls --summarize --human-readable --recursive s3://ngi-igenomes/igenomes/ | grep 'Total Size'
  ```

* Run the syncing:

  ```bash
  cd /nfsmounts/igenomes
  ~/miniconda/bin/aws s3 sync s3://ngi-igenomes/igenomes/ .
  ```
