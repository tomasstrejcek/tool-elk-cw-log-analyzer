
# Docker ELK local log tool

#### requirements
- docker
- aws cli
- gunzip
- git :)

#### how to

export cloudwatch logs to s3 bucket
(filter your log group in cloudwatch, check it, click action button > export to s3)

using aws cli download logs from s3 bucket to local machine - I suggest using just smaller portion of logs (max hundreds of megabytes)
```
aws s3 sync s3://bucket-name ./logs --profile profile-name
```
files will be gzipped without file extension (that doesnt matter) in folder called bucket name inside logs, gunzip them
```
gunzip -r ./logs
```

now, you have to update the path to log files accordingly in `logstash/pipeline/logstash.conf`

`/usr/share/logstash/data/**/*` is related to `logs/ip_master/00000` logfile

after that, just ran `docker-compose up` and in few minutes, on localhost:5061 you will have running kibana showing your logs, now you can search

##### reset
just run `docker-composer down` - this will remove all docker machines from this stack

after that you need to remove volume with the elasticsearch data, so its no restored when you rung docker-composer up again
run `docker volume rm cloudwatchelk_esdata1` (or use `docker volume ls` to fund the volume name)

### logs prefiltering
in `logstash/pipeline/logstash.conf` is commented out filtering module
```
if ([message] !~ "message-not-contains1" and [message] !~ "message-not-contains2"  ) {
    drop { }
}
```
Logs not containing specific keywords are dropped, this can be obviously tuned away to drop unneccessary stuff
