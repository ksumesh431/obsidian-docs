``` yaml
AWSTemplateFormatVersion: "2010-09-09"
Metadata:
  LICENSE: Apache License, Version 2.0
  AWS::CloudFormation::Interface:
    ParameterGroups:
      - Label:
          default: GitHub configuration
        Parameters:
          - GitHubUsername
          - GitHubOAuthToken
          - GitHubOwner
          - GitHubRepoName
          - SourceBranch
          - ReleaseBranch
      - Label:
          default: AWS Solution configuration
        Parameters:
          - Name
          - ClusterName
          - EnvironmentType
          - BuildspecFileName
          - AwsRegion
          - EcrRepoUrl
          - ServiceRoleArn
    # ParameterLabels:
    #   GitHubUsername:
    #     default: User name
    #   GitHubOAuthToken:
    #     default: Personal access token
    #   GitHubOwner:
    #     default: Repository owner
    #   GitHubRepoName:
    #     default: Repository name
    #   SourceBranch:
    #     default: Source branch name
    #   ReleaseBranch:
    #     default: Release branch name
    #   QSS3BucketName:
    #     default: AWS Solution S3 bucket name
    #   QSS3KeyPrefix:
    #     default: AWS Solution S3 key prefix
Parameters:
  GitHubUsername:
    Type: String
    Description: >-
      GitHub user name to use as the continuous integration (CI) service account.
  GitHubOAuthToken:
    Type: String
    Description: >-
      GitHub personal access token for the continuous integration (CI) service account user. This token must be granted the "repo" and "admin:repo_hook" permission sets. Refer to https://github.com/settings/tokens.
    NoEcho: true
  GitHubOwner:
    Type: String
    Description: >-
      GitHub owner (user name or organization) where the repository is located. Refer to https://docs.github.com/en/repositories/creating-and-managing-repositories/about-repositories.
  GitHubRepoName:
    Type: String
    Description: Name of the source repository to monitor.
  SourceBranch:
    Type: String
    Description: Name of the source branch to monitor.
    Default: develop
  ReleaseBranch:
    Type: String
    Description: >-
      Release branch name. Commits in the source branch merge into this branch.
    Default: main

  Name:
    Type: String
    Description: Enter a name for the service
    Default: "webgraph"
    
  ClusterName:
    Type: String
    Description: Enter a name for the ECS cluster
    Default: "dev10-cr-essentials"

  ECSServiceName:
    Type: String
    Description: Enter a name for the ECS service
    Default: "web-graphs-service"


  EnvironmentType:
    Type: String
    Description: Enter the type of enviroment  
    Default: "dev"

  BuildspecFileName:
    Type: String
    Description: Enter the name of the buildspec file in Github 
    Default: "buildspec.yml"

  AwsRegion:
    Type: String
    Description: Select the AWS region for deployment
    Default: "us-east-1"

Description: ""
Resources:
  # CodebuildServiceRole:
  #   Type: 'AWS::IAM::Role'
  #   Properties:
  #     Path: /service-role/
  #     RoleName: !Join 
  #       - '-'
  #       - - !Ref Name
  #         - !Ref ClusterName
  #         - !Ref EnvironmentType
  #         - iam
  #         - service
  #         - role
  #     AssumeRolePolicyDocument:
  #       Version: 2012-10-17
  #       Statement:
  #         - Effect: Allow
  #           Principal:
  #             Service: codebuild.amazonaws.com
  #           Action: 'sts:AssumeRole'
  #     Policies:
  #       - PolicyName: service-role-policy
  #         PolicyDocument:
  #           Version: 2012-10-17
  #           Statement:
  #             - Sid: CloudWatchLogsPolicy
  #               Effect: Allow
  #               Action:
  #                 - 'logs:CreateLogGroup'
  #                 - 'logs:CreateLogStream'
  #                 - 'logs:PutLogEvents'
  #               Resource: '*'
  #             - Sid: CodeCommitPolicy
  #               Effect: Allow
  #               Action:
  #                 - 'codecommit:GitPull'
  #               Resource: '*'
  #             - Sid: S3GetObjectPolicy
  #               Effect: Allow
  #               Action:
  #                 - 's3:GetObject'
  #                 - 's3:GetObjectVersion'
  #               Resource: '*'
  #             - Sid: S3PutObjectPolicy
  #               Effect: Allow
  #               Action:
  #                 - 's3:PutObject'
  #               Resource: '*'
  #             - Sid: S3BucketIdentity
  #               Effect: Allow
  #               Action:
  #                 - 's3:GetBucketAcl'
  #                 - 's3:GetBucketLocation'
  #               Resource: '*'
  #     MaxSessionDuration: 3600
  #     ManagedPolicyArns:
  #       - 'arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryFullAccess'
  #       - 'arn:aws:iam::aws:policy/AmazonRDSFullAccess'
  #       - 'arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess'
  #       - 'arn:aws:iam::aws:policy/AmazonElasticContainerRegistryPublicReadOnly'

  ArtifactBucket:
    Type: AWS::S3::Bucket
    Properties:
      AccessControl: Private
      LifecycleConfiguration:
        Rules:
          - NoncurrentVersionExpirationInDays: 30
            Status: Enabled
      VersioningConfiguration:
        Status: Enabled

  CodeBuildServiceRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: !Join 
        - '-'
        - - !Ref Name
          - !Ref ClusterName
          - !Ref EnvironmentType
          - service
          - role      
      Path: /
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Action: sts:AssumeRole
            Principal:
              Service: codebuild.amazonaws.com
      ManagedPolicyArns:
        - !Sub arn:${AWS::Partition}:iam::aws:policy/AdministratorAccess



  ECRRepository:
    Type: "AWS::ECR::Repository"
    Properties:
      RepositoryName: !Join ['-', [!Ref Name, !Ref ClusterName, !Ref EnvironmentType,"ecr","repo"]]
  
  CodePipelineServiceRole:
    Type: AWS::IAM::Role
    Properties:
      Path: /
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Action: sts:AssumeRole
            Principal:
              Service: codepipeline.amazonaws.com
      Policies:
        - PolicyName: !Join ['-', [!Ref Name, !Ref ClusterName, !Ref EnvironmentType,"codePipeline","policy"]]
          PolicyDocument:
            Version: 2012-10-17
            Statement:
              - Sid: ListArtifact
                Effect: Allow
                Action:
                  - s3:GetBucketVersioning
                  - s3:ListBucket
                  - s3:ListBucketVersions
                Resource: !GetAtt ArtifactBucket.Arn
              - Sid: PutArtifacts
                Effect: Allow
                Action:
                  - s3:GetObject
                  - s3:GetObjectVersion
                  - s3:PutObject
                Resource: !Sub ${ArtifactBucket.Arn}/*
              - Sid: ManageCloudFormation
                Effect: Allow
                Action:
                  - cloudformation:CreateChangeSet
                  - cloudformation:CreateStack
                  - cloudformation:DeleteChangeSet
                  - cloudformation:DeleteStack
                  - cloudformation:DescribeChangeSet
                  - cloudformation:DescribeStacks
                  - cloudformation:ExecuteChangeSet
                  - cloudformation:SetStackPolicy
                  - cloudformation:UpdateStack
                  - cloudformation:ValidateTemplate
                Resource: !Sub arn:${AWS::Partition}:cloudformation:*:*:*
              - Sid: PassRoleToCloudFormation
                Effect: Allow
                Action: iam:PassRole
                Resource: '*'
                Condition:
                  StringEquals:
                    iam:PassedToService: cloudformation.amazonaws.com
              - Sid: StartBuilds
                Effect: Allow
                Action:
                  - codebuild:BatchGetBuilds
                  - codebuild:StartBuild
                Resource: !GetAtt CodeBuildProject.Arn
              # - Sid: ECSDeployPermissions
              #   Effect: Allow
              #   Action:
              #     - "ecs:SubmitTaskStateChange"
              #     - "ecs:UpdateCluster"
              #     - "ecs:UpdateClusterSettings"
              #     - "ecs:ListAttributes"
              #     - "ecs:ExecuteCommand"
              #     - "ecs:DeleteService"
              #     - "ecs:DeleteCluster"
              #     - "ecs:RegisterContainerInstance"
              #     - "ecs:DescribeClusters"
              #     - "ecs:SubmitAttachmentStateChanges"
              #     - "ecs:UpdateService"
              #     - "ecs:ListTagsForResource"
              #     - "ecs:CreateService"
              #     - "ecs:SubmitContainerStateChange"
              #     - "ecs:DescribeServices"
              #     - "ecs:ListContainerInstances"
              #     - "ecs:UpdateServicePrimaryTaskSet"
              #     - "ecs:DeregisterContainerInstance"
              #     - "ecs:PutClusterCapacityProviders"
              #   # Resource:
              #   #   - arn:aws:ecs:us-east-1:240087729059:service/dev10-cr-essentials/web-graphs-service
              # - Sid: ECSReadPermissions
              #   Effect: Allow
              #   Action:
              #     - "ecs:ListServicesByNamespace"
              #     - "ecs:DiscoverPollEndpoint"
              #     - "ecs:PutAccountSettingDefault"
              #     - "ecs:CreateCluster"
              #     - "ecs:DescribeTaskDefinition"
              #     - "ecs:PutAccountSetting"
              #     - "ecs:ListServices"
              #     - "ecs:CreateCapacityProvider"
              #     - "ecs:DeregisterTaskDefinition"
              #     - "ecs:ListAccountSettings"
              #     - "ecs:DeleteAccountSetting"
              #     - "ecs:ListTaskDefinitionFamilies"
              #     - "ecs:RegisterTaskDefinition"
              #     - "ecs:ListTaskDefinitions"
              #     - "ecs:CreateTaskSet"
              #     - "ecs:ListClusters"
              #   Resource: "*"
              # - Sid: ECRReadPermissions
              #   Effect: Allow
              #   Action:
              #     - "ecr:GetDownloadUrlForLayer"
              #     - "ecr:BatchGetImage"
              #   Resource:
              #     - !GetAtt ECRRepository.Arn
      ManagedPolicyArns:
        - !Sub arn:${AWS::Partition}:iam::aws:policy/AWSCodeCommitFullAccess
        - arn:aws:iam::aws:policy/AmazonECS_FullAccess
        - arn:aws:iam::aws:policy/AmazonElasticContainerRegistryPublicFullAccess


  CodeBuildProject:
    Type: "AWS::CodeBuild::Project"
    Properties:
        Name: !Join ['-', [!Ref Name, !Ref ClusterName, !Ref EnvironmentType ]]
        Source: 
            BuildSpec: !Ref BuildspecFileName
            InsecureSsl: false
            Type: "CODEPIPELINE"
        Artifacts: 
            EncryptionDisabled: false
            Name: "dev-cr-essentials-migration-project"
            Packaging: "NONE"
            Type: "CODEPIPELINE"
        Cache: 
            Type: "NO_CACHE"
        Environment: 
            ComputeType: "BUILD_GENERAL1_SMALL"
            EnvironmentVariables: 
              - 
                Name: "CI_REGION"
                Type: "PLAINTEXT"
                Value: !Ref AwsRegion
              - 
                Name: "REPO_URL"
                Type: "PLAINTEXT"
                Value: !GetAtt ECRRepository.RepositoryUri
              - 
                Name: "DOCKER_USERNAME"
                Type: "PLAINTEXT"
                Value: "behaviorsoft"
              - 
                Name: "DOCKER_PASSWORD"
                Type: "PLAINTEXT"
                Value: "WHW9pcw6tyu-wev7mqy"
              - 
                Name: "REPO_ARN"
                Type: "PLAINTEXT"
                Value: !Sub "${AWS::AccountId}.dkr.ecr.${AWS::Region}.amazonaws.com"
            Image: "aws/codebuild/standard:4.0"
            ImagePullCredentialsType: "CODEBUILD"
            PrivilegedMode: true
            Type: "LINUX_CONTAINER"
        ServiceRole: !Ref CodeBuildServiceRole
        TimeoutInMinutes: 60
        QueuedTimeoutInMinutes: 480
        #EncryptionKey: !Sub "arn:aws:kms:${AWS::Region}:240087729059:alias/aws/s3"
        VpcConfig: {}
        BadgeEnabled: false
        LogsConfig: 
            CloudWatchLogs: 
                Status: "ENABLED"
            S3Logs: 
                Status: "DISABLED"
                EncryptionDisabled: false
        Visibility: "PRIVATE"

#   CodeDeployServiceRole:
#       Type: "AWS::IAM::Role"
#       Properties:
#           Path: "/"
#           RoleName: !Join ['-', [!Ref Name, !Ref ClusterName, !Ref EnvironmentType, "CodeDeploy","ServiceRole" ]]
#           AssumeRolePolicyDocument: "{\"Version\":\"2012-10-17\",\"Statement\":[{\"Sid\":\"\",\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"codedeploy.amazonaws.com\"},\"Action\":\"sts:AssumeRole\"}]}"
#           MaxSessionDuration: 3600
#           ManagedPolicyArns: 
#             - "arn:aws:iam::aws:policy/AWSCodeDeployRoleForECS"
#           Description: "Allows CodeDeploy to read S3 objects, invoke Lambda functions, publish to SNS topics, and update ECS services on your behalf."


# # Code deploy
#   CodeDeployApplication:
#       Type: "AWS::CodeDeploy::Application"
#       Properties:
#           ApplicationName: !Join ['-', [!Ref Name, !Ref ClusterName, !Ref EnvironmentType,"code","deploy"]]
#           ComputePlatform: "ECS"

#   CodeDeployDeploymentConfig:
#       Type: "AWS::CodeDeploy::DeploymentConfig"
#       Properties:
#           DeploymentConfigName: !Join ['-', [!Ref Name, !Ref ClusterName, !Ref EnvironmentType,"deployment","config"]]
#           MinimumHealthyHosts:
#             Type: "FLEET_PERCENT"
#             Value: 75  # Adjust the percentage value as needed          

#   CodeDeployDeploymentGroup:
#       Type: "AWS::CodeDeploy::DeploymentGroup"
#       Properties:
#         ApplicationName: !Ref CodeDeployApplication
#         DeploymentGroupName: !Join ['-', [!Ref Name, !Ref ClusterName, !Ref EnvironmentType,"deploment","group"]]
#         DeploymentConfigName: "CodeDeployDefault.HalfAtATime"
#         ServiceRoleArn: !GetAtt CodeDeployServiceRole.Arn
#         AlarmConfiguration: 
#           Enabled: false
#           IgnorePollAlarmFailure: false
#         AutoRollbackConfiguration: 
#           Enabled: true
#           Events: 
#           - "DEPLOYMENT_FAILURE"
#         DeploymentStyle: 
#           DeploymentType: "BLUE_GREEN"
#           DeploymentOption: "WITH_TRAFFIC_CONTROL"
#         LoadBalancerInfo: 
#           TargetGroupInfoList: 
#             - Name: "web-graphs-tg"


  CodePipeline:
    Type: AWS::CodePipeline::Pipeline
    Properties:
      ArtifactStore:
        Type: S3
        Location: !Ref ArtifactBucket
      RoleArn: !GetAtt CodePipelineServiceRole.Arn
      Stages:
        - Name: Source
          Actions:
            - Name: GitHub
              InputArtifacts: []
              ActionTypeId:
                Category: Source
                Owner: ThirdParty
                Version: 1
                Provider: GitHub
              OutputArtifacts:
                - Name: Source
              Configuration:
                Owner: !Ref GitHubOwner
                Repo: !Ref GitHubRepoName
                Branch: !Ref SourceBranch
                OAuthToken: !Ref GitHubOAuthToken
              RunOrder: 1
        - Name: Build
          Actions:
            - Name: CodeBuild
              InputArtifacts:
                - Name: Source
              ActionTypeId:
                Category: Build
                Owner: AWS
                Version: 1
                Provider: CodeBuild
              OutputArtifacts:
                - Name: "BuildArtifact"
              Configuration:
                ProjectName: !Ref CodeBuildProject
              RunOrder: 2

        # - Name: "Deploy"
        #   Actions: 
        #     - Name: "Deploy"
        #       ActionTypeId: 
        #           Category: "Deploy"
        #           Owner: "AWS"
        #           Provider: "CodeDeployToECS"
        #           Version: "1"
        #       Configuration: 
        #           AppSpecTemplateArtifact: "BuildArtifact"
        #           ApplicationName: !Ref CodeDeployApplication
        #           DeploymentGroupName:  !Ref CodeDeployDeploymentGroup
        #           Image1ArtifactName: "BuildArtifact"
        #           Image1ContainerName: "imageUri"
        #           TaskDefinitionTemplateArtifact: "BuildArtifact"
        #       InputArtifacts: 
        #         - 
        #           Name: "BuildArtifact"
        #       Region: !Ref AWS::Region
        #       Namespace: "DeployVariables"
        #       RunOrder: 3
        - Name: "Webgraph-deployment"
          Actions: 
            - Name: "Deploy"
              ActionTypeId:         
                Category: "Deploy"
                Owner: "AWS"
                Provider: "ECS"
                Version: "1"
              Configuration: 
                ClusterName: !Ref ClusterName
                DeploymentTimeout: "20"
                FileName: "imagedefinitions.json"
                ServiceName: !Ref ECSServiceName
              InputArtifacts: 
              - Name: "BuildArtifact"
              Region: !Ref AWS::Region
              RunOrder: 3


Outputs:
  CodePipelineURL:
    Description: The URL of the created pipeline.
    Value: !Sub https://${AWS::Region}.console.aws.amazon.com/codepipeline/home?region=${AWS::Region}#/view/${CodePipeline}
  TaskCatReports:
    Description: Path to the TaskCat report. Each report is named as CODEBUILD_BUILD_ID.zip.
    Value: !Sub s3://${ArtifactBucket}

```