# securityhub-standard-disable
multiple region disable some controls for securityhub standards.There are total 3 standards, you must enable standard before to disable some controls.
you may change the StandardsSubscriptions[0] to[1],[2] to get other standard(depends on how many standards you enabled)
## Step 1 参数设置
三个参数:
regions:需要执行的所有region
ids:需要disable的所有control代码
reason:disable control必须写原因
中国的
```
regions=($(aws ec2 describe-regions --query 'Regions[*].RegionName' --region=cn-north-1 --output text))
```
海外的
```
regions=($(aws ec2 describe-regions --query 'Regions[*].RegionName' --region=us-east-1 --output text))
```
cis-aws-foundations-benchmark https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-cis-to-disable.html
```
ids=('2.7'
'1.2''1.3''1.4''1.5''1.6''1.7''1.8''1.9''1.10''1.11''1.12''1.3''1.14''1.16''1.20''1.22''2.5'
'1.1''3.1''3.2''3.3''3.4''3.5''3.6''3.7''3.8''3.9''3.10''3.11''3.12''3.13''3.14'
)
reason='没有root账号,可以按需求写'

```
aws-foundational-security-best-practices sample
```
ids=(
'DynamoDB.1'
'DynamoDB.2'
)
reason='没有使用此服务,可以按需求写'
```
## Step2 CLI命令,不同标准需要修改StandardsSubscriptions[0] to[1],[2]
```
for region in $regions; do
sarn=$(aws securityhub get-enabled-standards --query 'StandardsSubscriptions[0].StandardsSubscriptionArn' --output text --region=$region)
echo $region
for cid in $ids; do
arn=$sarn'/'$cid
echo $arn
aws securityhub update-standards-control  --standards-control-arn=$arn --control-status=DISABLED --disabled-reason=$reason --region=$region
done
done
```
## Step3 CLI命令,重新打开disable的control
```
for region in $regions; do
sarn=$(aws securityhub get-enabled-standards --query 'StandardsSubscriptions[0].StandardsSubscriptionArn' --output text --region=$region)
echo $region
for cid in $ids; do
arn=$sarn'/'$cid
echo $arn
aws securityhub update-standards-control  --standards-control-arn=$arn --control-status=ENABLED --region=$region --region=$region
done
done
```
