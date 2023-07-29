


go to infrastructure, aws, create role as specified

copy role arn
arn:aws:iam::895884664845:role/NewRelicInfrastructure-Integrations

select :"create with cfn"
![](Images/Pasted%20image%2020230620221541.png)


cfn
https://console.aws.amazon.com/cloudformation/home?#/stacks/quickcreate?templateURL=https://nr-downloads-main.s3.amazonaws.com/cloud_integrations/aws/cloudformation/MetricStreams_CloudFormation.yml&stackName=NewRelic-Metric-Stream&param_NewRelicDatacenter=US




<br/>

---

<br/>

``` 
resource "newrelic_nrql_alert_condition" "cpuspikealert" {
  account_id = 3977145
  policy_id = 4458291
  type = "static"
  name = "cpu-spike-alert"
  enabled = true
  violation_time_limit_seconds = 259200

  nrql {
    query = "SELECT average(cpuPercent) FROM SystemSample FACET CASES (WHERE (entityGuid = 'Mzk3NzE0NXxJTkZSQXxOQXwtODAyMjI0MzI0NjAzMTcxOTY1') AS 'ip-172-31-2-198')"
  }

  critical {
    operator = "above"
    threshold = 12
    threshold_duration = 60
    threshold_occurrences = "at_least_once"
  }
  fill_option = "none"
  aggregation_window = 30
  aggregation_method = "event_flow"
  aggregation_delay = 50
}


```