# cis-aws-foundations-benchmark
reference from https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-cis-to-disable.html
## cloudtrail
```
ids=('2.7')
reason='CloudTrail trail logs is centrally stored'
```
## global resource

```
regions=(${regions[*]/eu-west-3}) 
ids=(
'1.1''3.1''3.2''3.3''3.4''3.5''3.6''3.7''3.8''3.9''3.10''3.11''3.12''3.13''3.14'
)
reason='global resource in one region'
```
## guardduty
需要将保留此control的region排除,将eu-west-3替换
```
ids=(
'1.1' '3.1' '3.2' '3.3' '3.4' '3.5' '3.6' '3.7' '3.8' '3.9' '3.10' '3.11' '3.12' '3.13' '3.14'
)
reason='guardduty is enabled'
```
