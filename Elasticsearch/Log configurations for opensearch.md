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

`curl -fsSL https://toolbelt.treasuredata.com/sh/install-ubuntu-jammy-fluent-package5-lts.sh | sh` (Ubuntu jammy)

### Install opensearch and concat plugins

sudo fluent-gem install fluent-plugin-concat
sudo fluent-gem install fluent-plugin-opensearch

### Must ensure that the `_fluentd` user can access the log path defined in config
`sudo usermod -a -G adm _fluentd`


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


# nginxaccess_json
<source>
  @type tail
  path /var/log/nginx/*.access.json*
  path_key log_path
  pos_file /var/log/fluent/nginx_access_json.pos
  tag nginx.access.json
  <parse>
    @type json
  </parse>
  exclude_path ["/var/log/nginx/*.gz"]
</source>

<filter nginx.access.json>
  @type record_transformer
  enable_ruby true
  <record>
    message ${record.to_json}
    fields {"indexname": "nginx-access-json-*", "document_type": "nginxaccess_json", "game": "stairway", "application": "stairway-app", "environment": "dev"}
    agent {"type": "fluentd", "hostname": "${hostname}", "version": "#{Fluent::VERSION}"}
    host {"name": "${hostname}"}
    input {"type": "tail"}
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
  logstash_prefix nginx-access-json
  logstash_dateformat %Y.%m.%d
  <buffer>
    flush_interval 5s
  </buffer>
</match>

##################################################################################

# nginxerror
<source>
  @type tail
  path /var/log/nginx/error.log*
  path_key log_path
  pos_file /var/log/fluent/nginx_error_log.pos
  tag nginx.error
  <parse>
    @type none
  </parse>
  exclude_path ["/var/log/nginx/*.gz"]
</source>

<filter nginx.error>
  @type parser
  key_name message
  <parse>
    @type regexp
    expression /^(?<time>\d{4}\/\d{2}\/\d{2} \d{2}:\d{2}:\d{2}) \[(?<severity>\w+)\] (?<pid>\d+)\#(?<tid>\d+): \*(?<connection_id>\d+) (?<errormessage>.*)/
    time_key time
    time_format %Y/%m/%d %H:%M:%S
  </parse>
</filter>

<filter nginx.error>
  @type record_transformer
  enable_ruby true
  <record>
    "@timestamp" ${time.strftime('%Y-%m-%dT%H:%M:%S.%L%z')}
    fields {"game": "stairway", "indexname": "nginxerror", "document_type": "nginxerror", "environment": "dev", "application": "stairway-app"}
    agent {"id": "#{SecureRandom.uuid}", "version": "#{Fluent::VERSION}", "name": "${hostname}", "ephemeral_id": "#{SecureRandom.uuid}", "type": "fluentd", "hostname": "${hostname}"}
    input {"type": "filestream"}
    host {"name": "${hostname}"}
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
  logstash_prefix nginxerror
  logstash_dateformat %Y.%m.%d
  <buffer>
    flush_interval 5s
  </buffer>
</match>

##################################################################################

# # testing

# <source>
#   @type tail
#   path /var/log/nginx/*.access.json*
#   pos_file /var/log/fluent/test_logs.pos
#   tag testing.index
#   <parse>
#     @type json
#   </parse>
#   exclude_path ["/var/log/nginx/*.gz"]
# </source>

# <filter testing.index>
#   @type record_transformer
#   enable_ruby true
#   <record>
#     message ${record.to_json}
#     fields {"indexname": "testing-format-index-*", "document_type": "nginxaccess_json", "game": "stairway", "application": "stairway-app", "environment": "dev"}
#     agent {"type": "fluentd", "hostname": "${hostname}", "version": "#{Fluent::VERSION}"}
#     host {"name": "${hostname}"}
#     input {"type": "tail"}
#     log {"file": {"path": "/var/log/nginx/access.json"}}
#   </record>
# </filter>

# <match testing.index>
#   @type opensearch
#   host vpc-opensearch-dev-omtcstmej7zun7nl5qxqt5hnk4.us-east-1.es.amazonaws.com
#   port 443
#   scheme https
#   include_tag_key true
#   type_name _doc
#   logstash_format true
#   logstash_prefix testing-format-index
#   logstash_dateformat %Y.%m.%d
#   <buffer>
#     flush_interval 5s
#   </buffer>
# </match>


##################################################################################

# kern.log and syslog

# Kernel logs
<source>
  @type tail
  path /var/log/kern.log
  pos_file /var/log/fluent/kern.log.pos
  path_key log_path
  tag kern.log
  <parse>
    @type none
  </parse>
</source>

# Syslog
<source>
  @type tail
  path /var/log/syslog
  pos_file /var/log/fluent/syslog.pos
  path_key log_path
  tag syslog
  <parse>
    @type none
  </parse>
</source>

<filter kern.log>
  @type record_transformer
  enable_ruby true
  <record>
    message ${record["message"]}
    fields {"indexname": "syslog", "document_type": "kern.log", "game": "stairway", "application": "stairway-app", "environment": "dev"}
    agent {"type": "fluentd", "hostname": "${hostname}", "version": "#{Fluent::VERSION}"}
    host {"name": "${hostname}"}
    input {"type": "filestream"}
  </record>
</filter>

<filter syslog>
  @type record_transformer
  enable_ruby true
  <record>
    message ${record["message"]}
    fields {"indexname": "syslog", "document_type": "syslog", "game": "stairway", "application": "stairway-app", "environment": "dev"}
    agent {"type": "fluentd", "hostname": "${hostname}", "version": "#{Fluent::VERSION}"}
    host {"name": "${hostname}"}
    input {"type": "filestream"}
  </record>
</filter>

<match kern.log>
  @type opensearch
  host vpc-opensearch-dev-omtcstmej7zun7nl5qxqt5hnk4.us-east-1.es.amazonaws.com
  port 443
  scheme https
  include_tag_key true
  type_name _doc
  logstash_format true
  logstash_prefix syslog
  logstash_dateformat %Y.%m.%d
  <buffer>
    flush_interval 5s
  </buffer>
</match>

<match syslog>
  @type opensearch
  host vpc-opensearch-dev-omtcstmej7zun7nl5qxqt5hnk4.us-east-1.es.amazonaws.com
  port 443
  scheme https
  include_tag_key true
  type_name _doc
  logstash_format true
  logstash_prefix syslog
  logstash_dateformat %Y.%m.%d
  <buffer>
    flush_interval 5s
  </buffer>
</match>


##################################################################################

# logstash.json logs
<source>
  @type tail
  path /srv/hp-dev/shared/log/logstash.json.*
  path_key log_path
  pos_file /var/log/fluent/hp-dev_logstash.pos
  tag hp-dev_logstash.json
  <parse>
    @type json
  </parse>
  exclude_path ["/srv/hp-dev/shared/log/logstash.json.*.gz"]
</source>

<filter hp-dev_logstash.json>
  @type record_transformer
  enable_ruby true
  <record>
    message ${record["message"]}
    fields {"indexname": "tapservice", "document_type": "log", "game": "stairway", "application": "stairway-app", "environment": "dev"}
    agent {"type": "fluentd", "hostname": "${hostname}", "version": "#{Fluent::VERSION}"}
    host {"name": "${hostname}"}
    input {"type": "filestream"}
  </record>
</filter>

<match hp-dev_logstash.json>
  @type opensearch
  host vpc-opensearch-dev-omtcstmej7zun7nl5qxqt5hnk4.us-east-1.es.amazonaws.com
  port 443
  scheme https
  include_tag_key true
  type_name _doc
  logstash_format true
  logstash_prefix tapservice
  logstash_dateformat %Y.%m.%d
  <buffer>
    flush_interval 5s
  </buffer>
</match>
```

### `systemctl restart fluentd`
### Check fluentd logs
`tail -f /var/log/fluent/fluentd.log`

### Add index filter in opensearch 