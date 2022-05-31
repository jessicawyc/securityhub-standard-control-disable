# securityhub-standard-disable
multiple region disable some controls for securityhub standards.There are total 3 standards, you must enable standard before to disable some controls.
you may change the [0] to[1],[2] to get other standard.
``
ids=(
'1.1'
'1.2'
'1.3'
'1.4'
)
reason='没有root账号'
regions=( 
'cn-north-1'
'cn-northwest-1')
for region in $regions; do
sarn=$(aws securityhub get-enabled-standards --query 'StandardsSubscriptions[0].StandardsSubscriptionArn' --output text --region=$region)
echo $region
for cid in $ids; do
arn=$sarn'/'$cid
echo $arn
aws securityhub update-standards-control  --standards-control-arn=$arn --control-status=DISABLED --disabled-reason=$reason --region=$region
done
done
``
