aws-backup
==========

Simple python based backup script for AWS linux instances using EBS volumes and snapshots.
Meant to be used with cron (or similar scheduler) for scheduling of backups.

Works with any filesystems support the `fsfreeze` command, MySQL, and MongoDB.

Dependencies
------------

- Filesystem(s) supporting `fsfreeze`
- Python 3
- boto3

I use [pyenv](https://github.com/yyuu/pyenv) for my python environment. I am using python v3.4.5 at the monment. Once pyenv is setup and I have installed python v3.4.5, getting boto3 is easy via pip.
```
pyenv shell 3.4.5
pip install boto3
```

AWS Credentials
---------------

In order to use boto3, you need to setup your [AWS credentials](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html). Basically, I setup a directory and files in my home directory.
```
mkdir ~/.aws
cat > ~/.aws/credentials <<EOF
[default]
aws_access_key_id=<YOUR_ACCESS_KEY>
aws_secret_access_key=<YOUR_ACCESS_KEY_SERCET>
EOF
cat > ~/.aws/config <<EOF
[default]
region=<YOUR_AWS_REGION>
output=json
EOF
```

Invoking Manually
-----------------
If using pyenv, first
```
pyenv shell 3.4.5
```
Now that the python environment is setup, just invoke the script.
```
aws_backup \
    --volume-ids <VOLUME_ID1> <VOLUME_ID2> \
    --mounts / /var/log \
    --keep 5 \
    --mysql \
    --mysql-credentials ~/.aws_mysql.json \
    --tags frequency=daily
```
This will backup two volumes: `VOLUME_ID1` and `VOLUME_ID2`. It will also freeze mount points `/` and `/var/log` and flush and lock the mysql tables before creating the snapshot. It will retain up to 5 snapshots sorted by date. Once the snapshot is taken, filesystems are unfroze and databases are unlocked before waiting for snapshot to complete.

Tags is technically any arbitrary tags supported by EC2, but it is logical to use something related to the frequency of backup.

If you don't want to use `fsfreeze` or are using a filesystem that does not support `fsfreeze`, just do not specify any mounts with the `--mounts` option.

Cron Setup
----------
