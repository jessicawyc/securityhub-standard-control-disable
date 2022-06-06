# cis-aws-foundations-benchmark
reference from https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-cis-to-disable.html
## cloudtrail
```
keepregion=
regions=(${regions[*]/$keepregion}) 
ids=('2.7')
reason='CloudTrail trail logs is centrally stored in one region one account'
```
## global resource
将需要唯一保留的region名称如cn-north-1 或 eu-west-2赋给 keepregion
```
keepregion=
regions=(${regions[*]/$keepregion}) 
ids=(
'1.2' '1.3' '1.4' '1.5' '1.6' '1.7' '1.8' '1.9' '1.10' '1.11' '1.12' '1.13' '1.14' '1.16' '1.20' '1.22' '2.5'
)
reason='global resource only in one region'
```
## guardduty

```
ids=(
'1.1' '3.1' '3.2' '3.3' '3.4' '3.5' '3.6' '3.7' '3.8' '3.9' '3.10' '3.11' '3.12' '3.13' '3.14'
)
reason='guardduty is enabled'
```
## China Region
中国区特殊的情况没有root账号
```
ids=(
'1.1' '3.3' '1.12' 
)
reason='no root account in China'
```
## CLI 命令
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
