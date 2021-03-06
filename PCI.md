# Payment Card Industry Data Security Standard (PCI DSS)
reference from https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-pcidss-to-disable.html
## Step 1 参数设置 Set Parameter
### China Region
中国区特殊的情况没有root账号 No root account in China region
```
reason='no root user in China'
regions=($(aws ec2 describe-regions --query 'Regions[*].RegionName' --output text --region=cn-north-1))
ids=(
'PCI.IAM.1' 'PCI.CW.1' 'PCI.IAM.4' 'PCI.IAM.5'
)

```
### Self-Choice
条款本身可供客户选择的,禁用其中未选的,PCI let customer to choose one and disable other

[PCI.IAM.4](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html#pcidss-iam-1:~:text=resources%20as%20expected.-,%5BPCI.IAM.4%5D%20Hardware%20MFA%20should%20be%20enabled%20for%20the%20root%20user,-Severity%3A%20Critical) Hardware MFA should be enabled for the root user 

[PCI.IAM.5](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html#pcidss-iam-1:~:text=to%20your%20selection.-,%5BPCI.IAM.5%5D%20Virtual%20MFA%20should%20be%20enabled%20for%20the%20root%20user,-Severity%3A%20Critical)
Virtual MFA should be enabled for the root user 
```
reason='choose between different level control'
ids=(
'PCI.IAM.4' 或or 'PCI.IAM.5' 
)

```
如果选择开启Guardduty则无须使用PCI.CW.1,可以禁用
If Guardduty is enable, PCI.CW.1 can be disabled

```
reason='guardduty is enabled'
ids=(
'PCI.CW.1' 或 'PCI.GuardDuty.1'
)

```
### Global resource
将需要唯一保留的region名称如cn-north-1 或 eu-west-2赋给 keepregion
Assign a region name that needs to be uniquely reserved such as cn-north-1 or eu-west-2 to keepregion
```
reason='global resource only in one region'
ids=(
'PCI.IAM.1' 'PCI.IAM.2' 'PCI.IAM.3' 'PCI.IAM.4' 'PCI.IAM.5' 'PCI.IAM.6'
)
regions=($(aws ec2 describe-regions --query 'Regions[*].RegionName' --output text))
regions=(${regions[*]/$keepregion}) 
keepregion=cn-north-1
```


### 与FSBP和CIS同时都重复项
以下control同时存在于三个标准中,可选择只在一个standard中保留
The following controls exist in three standards at the same time, and you can choose to keep only one standard
```
regions=($(aws ec2 describe-regions --query 'Regions[*].RegionName' --output text))
ids=(
'PCI.CloudTrail.2' 'PCI.CloudTrail.1' 'PCI.CloudTrail.3' 'PCI.CloudTrail.4' 'PCI.Config.1' 'PCI.EC2.2' 'PCI.EC2.6' 'PCI.IAM.7' 'PCI.IAM.8' 
)
reason='duplicated with FSBP and CIS'
```

## Run CLI Commands

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

[三个标准重复项对照表3standardscompare table](https://github.com/jessicawyc/securityhub-standard-control-disable#%E4%B8%89%E4%B8%AA%E6%A0%87%E5%87%86%E7%9A%84%E7%9B%B8%E4%BC%BC%E6%88%96%E9%87%8D%E5%A4%8D%E6%9D%A1%E6%AC%BE%E5%AF%B9%E7%85%A7)

