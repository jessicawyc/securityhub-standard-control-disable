# securityhub-standard-disable
multiple region disable some controls for securityhub standards.There are total 3 standards, you must enable standard before to disable some controls.
you may change the StandardsSubscriptions[0] to[1],[2] to get other standard(depends on how many standards you enabled)
## Step 1 参数设置

```
regions=( 
'cn-north-1'
'cn-northwest-1')
```
### CIS的标准只需要数字号,
```
ids=(
'1.1'
'1.2'
'1.3'
'1.4'
)
reason='没有root账号,可以按需求写'

```
### aws-foundational-security-best-practices
```
ids=(
'DynamoDB.1'
'DynamoDB.2'
)
reason='没有root账号,可以按需求写'
```
## Step2 CLI命令
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
