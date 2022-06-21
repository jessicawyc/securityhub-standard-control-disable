# Payment Card Industry Data Security Standard (PCI DSS)
reference from https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-pcidss-to-disable.html

## Global resource
将需要唯一保留的region名称如cn-north-1 或 eu-west-2赋给 keepregion
```
regions=($(aws ec2 describe-regions --query 'Regions[*].RegionName' --output text))
keepregion=
regions=(${regions[*]/$keepregion}) 
ids=(
'PCI.IAM.1' 'PCI.IAM.2' 'PCI.IAM.3' 'PCI.IAM.4' 'PCI.IAM.5' 'PCI.IAM.6'
)
reason='global resource only in one region'
```
## Enable Guardduty

```
regions=($(aws ec2 describe-regions --query 'Regions[*].RegionName' --output text))
ids=(
'PCI.CW.1' 
)
reason='guardduty is enabled'
```
## China Region
中国区特殊的情况没有root账号
```
regions=($(aws ec2 describe-regions --query 'Regions[*].RegionName' --output text))
ids=(
'1.1' '3.3' '1.12' 
)
reason='no root account in China'
```
## 与FSBP和CIS同时都重复项
以下control同时存在于AWS Foundational Security Best Practices standard,可选择只在一个standard中保留
```
regions=($(aws ec2 describe-regions --query 'Regions[*].RegionName' --output text))
ids=(
'PCI.CloudTrail.2' 'PCI.CloudTrail.1' 'PCI.CloudTrail.3' 'PCI.CloudTrail.4' 'PCI.Config.1' 'PCI.EC2.2' 'PCI.EC2.6' 'PCI.IAM.7' 'PCI.IAM.8' 
)
reason='duplicated with FSBP and CIS'
```
## CLI 命令
```
for region in $regions; do
sarn=$(aws securityhub get-enabled-standards --query 'StandardsSubscriptions[2].StandardsSubscriptionArn' --output text --region=$region)
echo $region
for cid in $ids; do
arn=$sarn'/'$cid
echo $arn
aws securityhub update-standards-control  --standards-control-arn=$arn --control-status=DISABLED --disabled-reason=$reason --region=$region
done
done
```

![三个标准重复项对照表](/)
