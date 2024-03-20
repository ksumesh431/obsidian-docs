
## DynamoDB
create a table with a primary key

## IAM role for lambda
Add cloudwatch full access for logging and dynamodb full access for inmterating with dynamodb

## Lambda
Create a function with the iam role created above
change configuration to 500 mb ram and 1 min timeout 


## API Gateway
Create a new REST API. 
Create a resource like health and inside the resource, create PUT or GET methods that route to the lambda function