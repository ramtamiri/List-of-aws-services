#!/bin/bash
#######################
# Author: Tamiri Ram Kumar
# Date: 15-Sep-24
# Version: v2
# This script will list all the AWS resources being used
#######################

# Enable debugging for development; disable for production
set +x

# Redirect all output to a log file
#exec > aws_resources_audit.log 2>&1

#########################################################################

# Check if jq is installed
if ! command -v jq &> /dev/null; then
    echo "jq could not be found. Please install jq to parse JSON output."
    exit 1
fi

# AWS CLI Configuration Check
aws sts get-caller-identity > /dev/null 2>&1
if [ $? -ne 0 ]; then
  echo "AWS CLI is not configured properly. Please check your credentials."
  exit 1
fi

#########################################################################

# AWS Compute Resources

list_ec2_instances() {
    echo "List of EC2 Instances"
    result=$(aws ec2 describe-instances --output json)
    if echo "$result" | jq -e '.Reservations[].Instances | length > 0' > /dev/null; then
        echo "$result" | jq '.Reservations[].Instances[].InstanceId'
    else
        echo "No EC2 instances found."
    fi
}

list_lambda_functions() {
    echo "List of Lambda Functions"
    result=$(aws lambda list-functions --output json)
    if echo "$result" | jq -e '.Functions | length > 0' > /dev/null; then
        echo "$result" | jq '.Functions[].FunctionName'
    else
        echo "No Lambda functions found."
    fi
}

list_ecs_services() {
    echo "List of ECS Services"
    clusters=$(aws ecs list-clusters --query "clusterArns[]" --output text)

    if [ -z "$clusters" ]; then
        echo "No ECS clusters found."
        return
    fi

    for cluster in $clusters; do
        echo "Cluster: $cluster"
        services=$(aws ecs list-services --cluster "$cluster" --query "serviceArns[]" --output text)

        if [ -z "$services" ]; then
            echo "  No services found in this cluster."
        else
            echo "  Services:"
            for service in $services; do
                echo "    $service"
            done
        fi
    done
}

##########################################################################

# AWS Storage Resources

list_s3_buckets() {
    echo "List of S3 Buckets"
    result=$(aws s3api list-buckets --output json)
    if echo "$result" | jq -e '.Buckets | length > 0' > /dev/null; then
        echo "$result" | jq '.Buckets[].Name'
    else
        echo "No S3 buckets found."
    fi
}

list_ec2_volumes() {
    echo "List of EBS Volumes"
    result=$(aws ec2 describe-volumes --query "Volumes[*].VolumeId" --output text)
    if [ -n "$result" ]; then
        echo "$result"
    else
        echo "No EBS volumes found."
    fi
}

list_efs() {
    echo "List of Elastic File Systems"
    result=$(aws efs describe-file-systems --output json)
    if echo "$result" | jq -e '.FileSystems | length > 0' > /dev/null; then
        echo "$result" | jq '.FileSystems[] | {FileSystemId, CreationTime}'
    else
        echo "No Elastic File Systems found."
    fi
}

##########################################################################

# AWS Networking Resources

list_vpcs() {
    echo "List of VPCs"
    result=$(aws ec2 describe-vpcs --query "Vpcs[].VpcId" --output text)
    if [ -n "$result" ]; then
        echo "$result"
    else
        echo "No VPCs found."
    fi
}

list_route53() {
    echo "List of Route 53 Hosted Zones and Health Checks"
    hosted_zones=$(aws route53 list-hosted-zones --output json)
    health_checks=$(aws route53 list-health-checks --output json)

    if echo "$hosted_zones" | jq -e '.HostedZones | length > 0' > /dev/null; then
        echo "Hosted Zones:"
        echo "$hosted_zones" | jq '.HostedZones[].Name'
    else
        echo "No Route 53 hosted zones found."
    fi

    if echo "$health_checks" | jq -e '.HealthChecks | length > 0' > /dev/null; then
        echo "Health Checks:"
        echo "$health_checks" | jq '.HealthChecks[].Id'
    else
        echo "No Route 53 health checks found."
    fi
}

##########################################################################

# AWS Database Resources

list_rds_instances() {
    echo "List of RDS Instances"
    result=$(aws rds describe-db-instances --output json)
    if echo "$result" | jq -e '.DBInstances | length > 0' > /dev/null; then
        echo "$result" | jq '.DBInstances[].DBInstanceIdentifier'
    else
        echo "No RDS instances found."
    fi
}

list_dynamo_tables() {
    echo "List of DynamoDB Tables"
    result=$(aws dynamodb list-tables --output json)
    if echo "$result" | jq -e '.TableNames | length > 0' > /dev/null; then
        echo "$result" | jq '.TableNames[]'
    else
        echo "No DynamoDB tables found."
    fi
}

##########################################################################

# AWS Monitoring & Logging

list_cloudwatch_alarms() {
    echo "List of CloudWatch Alarms"
    result=$(aws cloudwatch describe-alarms --output json)
    if echo "$result" | jq -e '.MetricAlarms | length > 0' > /dev/null; then
        echo "$result" | jq '.MetricAlarms[].AlarmName'
    else
        echo "No CloudWatch alarms found."
    fi
}

list_cloudtrail_trails() {
    echo "List of CloudTrails"
    result=$(aws cloudtrail describe-trails --output json)
    if echo "$result" | jq -e '.trailList | length > 0' > /dev/null; then
        echo "$result" | jq '.trailList[].Name'
    else
        echo "No CloudTrails found."
    fi
}

##########################################################################

# Other AWS Resources

list_iam_users() {
    echo "List of IAM Users"
    result=$(aws iam list-users --query "Users[].UserName" --output text)
    if [ -n "$result" ]; then
        echo "$result"
    else
        echo "No IAM users found."
    fi
}

list_cloudformation_stacks() {
    echo "List of CloudFormation Stacks"
    result=$(aws cloudformation describe-stacks --query "Stacks[].StackName" --output text)
    if [ -n "$result" ]; then
        echo "$result"
    else
        echo "No CloudFormation stacks found."
    fi
}

list_sqs_queues() {
    echo "List of SQS Queues"
    result=$(aws sqs list-queues --output json)
    if echo "$result" | jq -e '.QueueUrls | length > 0' > /dev/null; then
        echo "$result" | jq '.QueueUrls[]'
    else
        echo "No SQS queues found."
    fi
}

list_sns_topics() {
    echo "List of SNS Topics"
    result=$(aws sns list-topics --output json)
    if echo "$result" | jq -e '.Topics | length > 0' > /dev/null; then
        echo "$result" | jq '.Topics[].TopicArn'
    else
        echo "No SNS topics found."
    fi
}

##########################################################################

# Execute Functions
list_ec2_instances
list_lambda_functions
list_ecs_services
list_s3_buckets
list_ec2_volumes
list_efs
list_vpcs
list_route53
list_rds_instances
list_dynamo_tables
list_cloudwatch_alarms
list_cloudtrail_trails
list_iam_users
list_cloudformation_stacks
list_sqs_queues
list_sns_topics

set +x  # Disable debugging mode
