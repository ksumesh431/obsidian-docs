# Usecase 1 (firelens routing the ecs container logs)
Firelens sidecar container along with main container. set the log config of the container to send logs to opensearch through firelens
Sample Config:
``` yaml
{
    "taskDefinitionArn": "arn:aws:ecs:us-east-1:857290109902:task-definition/wiki:48",
    "containerDefinitions": [
        {
            "name": "app",
            "image": "098149836275.dkr.ecr.us-east-1.amazonaws.com/tinyco/wiki:v1.0.6.6",
            "cpu": 1024,
            "memory": 2048,
            "portMappings": [
                {
                    "containerPort": 8000,
                    "hostPort": 0,
                    "protocol": "tcp"
                }
            ],
            "essential": true,
            "command": [
                "gunicorn"
            ],
            "environment": [
                {
                    "name": "REDIS_HOST",
                    "value": "master.tc-wiki-prod2.hzrj04.use1.cache.amazonaws.com"
                },
                {
                    "name": "S3_SCENEEDITOR_ROLE_ARN",
                    "value": "arn:aws:iam::098149836275:role/sceneeditor"
                },
                {
                    "name": "DB_SSL_AWS",
                    "value": "True"
                },
                {
                    "name": "SENTRY_DSN",
                    "value": "https://a07eb022dc5140928c67f4067cf0f7fe:e763f27e0a2a462481ed81880219e236@sentry.io/1258206"
                },
                {
                    "name": "ENVIRONMENT_LEVEL",
                    "value": "prod"
                },
                {
                    "name": "REDIS_SSL",
                    "value": "True"
                },
                {
                    "name": "DB_HOST",
                    "value": "wiki-prod.ctyo9e585fvz.us-east-1.rds.amazonaws.com"
                },
                {
                    "name": "STATIC_ROOT",
                    "value": "/srv/wiki_static"
                }
            ],
            "mountPoints": [
                {
                    "sourceVolume": "wiki_static",
                    "containerPath": "/srv/wiki_static"
                }
            ],
            "volumesFrom": [],
            "logConfiguration": {
                "logDriver": "awsfirelens",
                "options": {
                    "Aws_Auth": "On",
                    "Suppress_Type_Name": "On",
                    "Port": "443",
                    "Host": "vpc-opensearch-dev-omtcstmej7zun7nl5qxqt5hnk4.us-east-1.es.amazonaws.com",
                    "Index": "firelens",
                    "tls": "On",
                    "Aws_Region": "us-east-1",
                    "Name": "opensearch",
                    "retry_limit": "2"
                }
            },
            "systemControls": []
        },
        {
            "name": "worker",
            "image": "098149836275.dkr.ecr.us-east-1.amazonaws.com/tinyco/wiki:v1.0.6.6",
            "cpu": 0,
            "portMappings": [],
            "essential": false,
            "entryPoint": [
                "celery"
            ],
            "command": [
                "-A",
                "wiki",
                "worker",
                "-c",
                "2",
                "-l",
                "INFO"
            ],
            "environment": [
                {
                    "name": "REDIS_HOST",
                    "value": "master.tc-wiki-prod2.hzrj04.use1.cache.amazonaws.com"
                },
                {
                    "name": "S3_SCENEEDITOR_ROLE_ARN",
                    "value": "arn:aws:iam::098149836275:role/sceneeditor"
                },
                {
                    "name": "DB_SSL_AWS",
                    "value": "True"
                },
                {
                    "name": "SENTRY_DSN",
                    "value": "https://a07eb022dc5140928c67f4067cf0f7fe:e763f27e0a2a462481ed81880219e236@sentry.io/1258206"
                },
                {
                    "name": "ENVIRONMENT_LEVEL",
                    "value": "prod"
                },
                {
                    "name": "REDIS_SSL",
                    "value": "True"
                },
                {
                    "name": "DB_HOST",
                    "value": "wiki-prod.ctyo9e585fvz.us-east-1.rds.amazonaws.com"
                }
            ],
            "mountPoints": [],
            "volumesFrom": [],
            "workingDirectory": "/usr/src/app",
            "logConfiguration": {
                "logDriver": "awsfirelens",
                "options": {
                    "Aws_Auth": "On",
                    "Suppress_Type_Name": "On",
                    "Port": "443",
                    "Host": "vpc-opensearch-dev-omtcstmej7zun7nl5qxqt5hnk4.us-east-1.es.amazonaws.com",
                    "Index": "firelens",
                    "tls": "On",
                    "Aws_Region": "us-east-1",
                    "Name": "opensearch",
                    "retry_limit": "2"
                }
            },
            "systemControls": []
        },
        {
            "name": "log_router",
            "image": "857290109902.dkr.ecr.us-east-1.amazonaws.com/python-app-logger:firelens-stable",
            "cpu": 0,
            "memoryReservation": 50,
            "portMappings": [],
            "essential": true,
            "environment": [],
            "mountPoints": [],
            "volumesFrom": [],
            "user": "0",
            "logConfiguration": {
                "logDriver": "awslogs",
                "options": {
                    "awslogs-group": "firelens-container",
                    "awslogs-create-group": "true",
                    "awslogs-region": "us-west-2",
                    "awslogs-stream-prefix": "firelens"
                }
            },
            "systemControls": [],
            "firelensConfiguration": {
                "type": "fluentbit",
                "options": {
                    "enable-ecs-log-metadata": "true"
                }
            }
        }
    ],
    "family": "wiki",
    "taskRoleArn": "arn:aws:iam::857290109902:role/wiki-prod",
    "executionRoleArn": "arn:aws:iam::857290109902:role/ecsTaskExecutionRole",
    "networkMode": "bridge",
    "revision": 48,
    "volumes": [
        {
            "name": "wiki_static",
            "host": {}
        }
    ],
    "status": "ACTIVE",
    "requiresAttributes": [
        {
            "name": "com.amazonaws.ecs.capability.logging-driver.awslogs"
        },
        {
            "name": "ecs.capability.execution-role-awslogs"
        },
        {
            "name": "com.amazonaws.ecs.capability.ecr-auth"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.19"
        },
        {
            "name": "ecs.capability.firelens.fluentbit"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.17"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.21"
        },
        {
            "name": "com.amazonaws.ecs.capability.logging-driver.awsfirelens"
        },
        {
            "name": "com.amazonaws.ecs.capability.task-iam-role"
        },
        {
            "name": "ecs.capability.execution-role-ecr-pull"
        },
        {
            "name": "com.amazonaws.ecs.capability.docker-remote-api.1.29"
        }
    ],
    "placementConstraints": [],
    "compatibilities": [
        "EXTERNAL",
        "EC2"
    ],
    "requiresCompatibilities": [
        "EC2"
    ],
    "cpu": "1024",
    "memory": "2048",
    "registeredAt": "2024-07-01T18:33:00.504Z",
    "registeredBy": "arn:aws:iam::857290109902:user/py_vats",
    "tags": []
}

```





---

# Usecase 2 (Tradtional method)

Install filebeat which can read logs and forward to logstash which in turn forwards to opensearch

logs --> filebeat (forwarding) --> logstash (process logs) ----> opensearch



---

# Usecase 3

Install fluentd service which can read logs and send directly to opensearch

### Install fluentd
`curl -fsSL https://toolbelt.treasuredata.com/sh/install-ubuntu-focal-fluent-package5-lts.sh | sh` (Ubuntu focal)

### Install opensearch and concat plugins

sudo fluent-gem install fluent-plugin-concat
sudo fluent-gem install fluent-plugin-opensearch

### Must ensure that the `_fluentd` user can access the log path defined in config


### Add config (/etc/fluent/fluentd.conf)

``` config
<source>
  @type tail
  path /var/log/nginx/access.log
  pos_file /var/log/fluent/error.log.pos
  tag nginx.access
  <parse>
    @type nginx
  </parse>
</source>

<filter nginx.access>
  @type record_transformer
  <record>
    game stairway
    environment dev
    application stairway-app
    document_type nginxaccess_json
  </record>
</filter>

<match nginx.access>
  @type opensearch
  host vpc-opensearch-dev-omtcstmej7zun7nl5qxqt5hnk4.us-east-1.es.amazonaws.com
  port 443
  scheme https
  include_tag_key true
  type_name _doc
  logstash_format true
  logstash_prefix fluentd-nginx-access-
  logstash_dateformat %Y%m%d
  <buffer>
    flush_interval 5s
  </buffer>
</match>

<filter nginx.access>
  @type concat
  key log_message
  multiline_end_regexp /\n$/
  separator ""
  flush_interval 5s
  <parse>
    @type none
  </parse>
</filter>

```

#### More example configs
``` config

<source>
  @type tail
  path /var/log/nginx/*.access.json*
  pos_file /var/log/fluent/nginx_access_json.pos
  tag nginx.access.json
  <parse>
    @type json
  </parse>
  exclude_path ["/var/log/nginx/*.gz"]
</source>

<filter nginx.access.json>
  @type record_transformer
  <record>
    game stairway
    environment dev
    application stairway-app
    document_type nginxaccess_json
  </record>
</filter>

<match nginx.access.json>
  @type opensearch
  host vpc-opensearch-dev-omtcstmej7zun7nl5qxqt5hnk4.us-east-1.es.amazonaws.com
  port 443
  scheme https
  include_tag_key true
  type_name _doc
  logstash_format true
  logstash_prefix fluentd-nginx-access-json-
  logstash_dateformat %Y%m%d
  <buffer>
    flush_interval 5s
  </buffer>
</match>

<source>
  @type tail
  path /var/log/nginx/error.log*
  pos_file /var/log/fluent/nginx_error_log.pos
  tag nginx.error
  <parse>
    @type none
  </parse>
  exclude_path ["/var/log/nginx/*.gz"]
</source>

<filter nginx.error>
  @type record_transformer
  <record>
    game stairway
    environment dev
    application stairway-app
    document_type nginxerror
  </record>
</filter>

<match nginx.error>
  @type opensearch
  host vpc-opensearch-dev-omtcstmej7zun7nl5qxqt5hnk4.us-east-1.es.amazonaws.com
  port 443
  scheme https
  include_tag_key true
  type_name _doc
  logstash_format true
  logstash_prefix fluentd-nginx-error-
  logstash_dateformat %Y%m%d
  <buffer>
    flush_interval 5s
  </buffer>
</match>

```

### `systemctl restart fluentd`
### Check fluentd logs
`tail -f /var/log/fluent/fluentd.log`

### Add index filter in opensearch 