Create a slack workfklow, use **SubscribeURL** variable to get sns notification subscription. Then change it to **Message** to show message payload from sns

Can use lambda function in between to format the message payload

https://hooks.slack.com/triggers/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


**Sample workflow description:**

Alarm Name: DAX-cpu-metric-alarm

Description:
- Namespace: AWS/DAX
- Metric Name: CPUUtilization
- Statistic: Average
- Comparison Operator: Greater Than Threshold
- Threshold: 16.0%
- Period: 1 minute
- Dimensions: ClusterName = teamsdaxcluster
- Unit: Percent

Alarm : `Message` *SET AS VARIABLE*