## cronjobs

kubectl v1.10.1+

手动启动cronjob
kubectl create job --from=cronjob/<cronjob-name> <job-name>

```
k create job --from=cronjob/mng-job-group-member-total-active-script mng-job-group-member-total-active-script-manual-fdsafda
```

## 回退
cotilla@mng-30:/data1/logs/containers$ k rollout history  deployment/mng-api2-webcv
deployment.apps/mng-api2-webcv
REVISION  CHANGE-CAUSE
5896      kubectl set image deploy mng-api2-webcv webcv-py=registry.colorv.cn:5000/api2webcv:20210531115856 --record=true
5897      kubectl set image deploy mng-api2-webcv webcv-py=registry.colorv.cn:5000/api2webcv:20210531130930 --record=true
5898      kubectl set image deploy mng-api2-webcv webcv-py=registry.colorv.cn:5000/api2webcv:20210531130930 --record=true
5899      kubectl set image deploy mng-api2-webcv webcv-py=registry.colorv.cn:5000/api2webcv:20210531152742 --record=true
5900      kubectl set image deploy mng-api2-webcv webcv-py=registry.colorv.cn:5000/api2webcv:20210531153339 --record=true
5901      kubectl set image deploy mng-api2-webcv webcv-py=registry.colorv.cn:5000/api2webcv:20210531155047 --record=true
5902      kubectl set image deploy mng-api2-webcv webcv-py=registry.colorv.cn:5000/api2webcv:20210531155221 --record=true
5903      kubectl set image deploy mng-api2-webcv webcv-py=registry.colorv.cn:5000/api2webcv:20210531160437 --record=true
5904      kubectl set image deploy mng-api2-webcv webcv-py=registry.colorv.cn:5000/api2webcv:20210531162326 --record=true
5905      kubectl set image deploy mng-api2-webcv webcv-py=registry.colorv.cn:5000/api2webcv:20210531162801 --record=true
5906      kubectl set image deploy mng-api2-webcv webcv-py=registry.colorv.cn:5000/api2webcv:20210531170440 --record=true

cotilla@mng-30:/data1/logs/containers$ k rollout undo deployment/mng-api2-webcv  --to-revision=5899
deployment.apps/mng-api2-webcv rolled back
