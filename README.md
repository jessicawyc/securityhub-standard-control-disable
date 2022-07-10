# Securityhub-standard-control-disable
multiple region disable some controls for securityhub standards.There are total 3 standards, you must enable standard before to disable some controls.
you may change the StandardsSubscriptions[0] to[1],[2] to get other standard(depends on how many standards you enabled)

针对不同情况,可以选择disable的controls详细分析可和操作请参见各个标准文档:
For different situations, you can choose to disable the controls for detailed analysis and operation, please refer to each standard document:

[CIS.md](https://github.com/jessicawyc/securityhub-standard-disable/blob/main/CIS.md)

[FSBP.md](https://github.com/jessicawyc/securityhub-standard-disable/blob/main/FSBP.md)

[PCI.md](/PCI.md)

## Step 1 参数设置parameter settings

regions:需要执行的所有region, a list contains all the regions in use

ids:需要disable的所有control代码,a list contains all the controls ids to disable

reason:disable control必须写原因,the reason is mandatory,can not be blank

```
regions=($(aws ec2 describe-regions --query 'Regions[*].RegionName' --output text))
```
Sample code
```
ids=(
'DynamoDB.1'
'DynamoDB.2'
)
reason='没有使用此服务,可以按需求写,write the reason here'
```

## Step2 CLI commands,不同标准需要修改StandardsSubscriptions[0] to[1],[2] for detail check each standard page
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
## CLI命令,重新打开被disable的control, to re-enable some disabled control, use below commands
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
## 三个标准的相似或重复条款对照,duplicated or similar controls iin 3 standards
![table](/compare.1.png)
![table](/compare.2.png)
