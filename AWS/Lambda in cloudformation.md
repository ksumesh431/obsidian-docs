# For creating and triggering lambda from CloudFormation and using the lambda output in other CloudFormation resource

``` yaml
  LambdaFunction:

    Type: AWS::Lambda::Function

    DependsOn: LambdaExecutionRoleInlinePolicy

    Properties:

      Role: !GetAtt [LambdaExecutionRole, Arn]

      FunctionName: cfn-neptune-data-fetch

      Runtime: python3.12

      Handler: index.lambda_handler

      Timeout: 10

      Code:

        ZipFile: |

          import boto3

          import cfnresponse

  

          def lambda_handler(event, context):

              # neptune_cluster_name = 'use1-neptune-stage-abs'

              neptune_cluster_name = event['ResourceProperties']['NeptuneClusterName']

  

              try:

                  # Your logic to fetch the ARN

                  resource_id = get_neptune_cluster_resource_id(neptune_cluster_name)

                  # Send a success response to CloudFormation with the output data

                  # IMPORTANT RESPONSE CODE. CUSTOM RESOURCE WILL BE STUCK IN CLOUDFORMATION WITHOUT THIS

                  cfnresponse.send(event, context, cfnresponse.SUCCESS, {"OutputResourceID": resource_id})

  

              except Exception as e:

                  # Send a failure response to CloudFormation

                  cfnresponse.send(event, context, cfnresponse.FAILED, {"Error": str(e)})              

              # return {"NeptuneResourceID": resource_id}

  

          def get_neptune_cluster_resource_id(cluster_name):

              # Create a Neptune client without providing explicit credentials

              neptune_client = boto3.client('neptune')

  

              try:

                  # Describe the Neptune DB clusters

                  response = neptune_client.describe_db_clusters(DBClusterIdentifier=cluster_name)

  

                  # Extract and return the Resource ID

                  if 'DBClusters' in response and len(response['DBClusters']) > 0:

                      return response['DBClusters'][0]['DbClusterResourceId']

                  else:

                      return None

  

              except Exception as e:

                  print(f"Error: {e}")

                  return None

  

  # AWS Lambda-backed custom resource

  MyCustomResource:

    Type: "Custom::MyCustomResource"

    DependsOn: LambdaFunction

    Properties:

      ServiceToken: !GetAtt LambdaFunction.Arn

      NeptuneClusterName: !Ref NeptuneClusterName

  

  # The neptune db resource id can be fetched using "!GetAtt MyCustomResource.OutputResourceID"

```