## LAMBDA로 보안 그룹 규칙을 제거합니다
### 이것은 작성 예제임니다
import boto3

def lambda_handler(event, context):
    ec2 = boto3.client('ec2')
    response = ec2.describe_security_groups(GroupIds=['my_sg_id'])
    group = response['SecurityGroups'][0]
    
    ec2.revoke_security_group_ingress(GroupId=group['GroupId'], IpPermissions = group['IpPermissions'])
    ec2.revoke_security_group_egress(GroupId=group['GroupId'], IpPermissions = group['IpPermissionsEgress'])

    return True
## 정책도 필요합니다
### (다른 스크립트는 종강 후 작성합니다)
{
	"Version": "2012-10-17",
	"Statement": [
		{
			"Sid": "Statement1",
			"Effect": "Allow",
			"Action": [
				"ec2:RevokeSecurityGroup*",
				"ec2:DescribeSecurityGroups"
			],
			"Resource": "*"
		}
	]
}
## 연결하는 거도 필요함니다
aws lambda add-permission \
--function-name <my_function_name> \
--statement-id AlarmAction \
--action 'lambda:InvokeFunction' \
--principal lambda.alarms.cloudwatch.amazonaws.com \
--source-account <my_account(12자리)> \
--source-arn <my_alarm_arn>
