# AWS Foundational Best Practices
reference from https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-fsbp-to-disable.html
## 与CIS Benchmark重复的control
```
ids=(
'IAM.1' 'IAM.2'
'IAM.3' 'IAM.4' 'IAM.5' 'IAM.6' 'IAM.7' 'IAM.8' 'Config.1'
'CloudTrail.1' 
'CloudTrail.2'
'CloudTrail.4'
'CloudTrail.5'
'EC2.2'

)
reason='duplicated with CIS'
```
## global resource
将需要唯一保留的region名称如cn-north-1 或 eu-west-2赋给 keepregion
```
keepregion=
regions=(${regions[*]/$keepregion}) 
ids=(
'IAM.1' 'IAM.2' 
'IAM.3' 'IAM.4' 'IAM.5' 'IAM.6' 'IAM.7' 'IAM.8' 'Config.1'
)
reason='global resource in one region'
```
中国区没有IAM.6,此处会报错,可忽略


## CLI 命令
```
for region in $regions; do
sarn=$(aws securityhub get-enabled-standards --query 'StandardsSubscriptions[1].StandardsSubscriptionArn' --output text --region=$region)
echo $region
for cid in $ids; do
arn=$sarn'/'$cid
echo $arn
aws securityhub update-standards-control  --standards-control-arn=$arn --control-status=DISABLED --disabled-reason=$reason --region=$region
done
done
```

[三个标准重复项对照表](https://github.com/jessicawyc/securityhub-standard-control-disable#%E4%B8%89%E4%B8%AA%E6%A0%87%E5%87%86%E7%9A%84%E7%9B%B8%E4%BC%BC%E6%88%96%E9%87%8D%E5%A4%8D%E6%9D%A1%E6%AC%BE%E5%AF%B9%E7%85%A7)
