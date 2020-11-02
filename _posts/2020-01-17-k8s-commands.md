## cronjobs

kubectl v1.10.1+

手动启动cronjob
kubectl create job --from=cronjob/<cronjob-name> <job-name>

```
k create job --from=cronjob/mng-job-group-member-total-active-script mng-job-group-member-total-active-script-manual-fdsafda
```
