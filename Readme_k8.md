Kubernetes command



kubectl delete -f k8s/dashboard-all.yaml


--- apply manifest file ------
kubectl apply -f k8s/dashboard-all.yaml

Check all resources are created 

kubectl get all -n release-dashboard

Individual resources
# Pods
kubectl get pods -n release-dashboard

# Services
kubectl get svc -n release-dashboard

# PVC
kubectl get pvc -n release-dashboard

# Ingress
kubectl get ingress -n release-dashboard



Check logs to verify the services

# MySQL logs
kubectl logs -n release-dashboard mysql-0

# Backend logs
kubectl logs -n release-dashboard -l app=backend

# Frontend logs
kubectl logs -n release-dashboard -l app=frontend



Verify mysql is ready 

kubectl exec -it mysql-0 -n release-dashboard -- mysql -uroot -proot -e "SELECT 1;"


Test backend connectivity (from inside cluster)

kubectl run -it --rm debug --image=curlimages/curl --restart=Never -n release-dashboard -- \
  curl http://backend-service:3000/health


Test frontend

kubectl port-forward -n release-dashboard svc/frontend-service 8080:80 &
# Then open: http://localhost:8080

Test through ingress if dns is configured

curl https://sa-uat.bajajfinserv.in/release-dashboard/uat/


Quick troubleshooting if pods don’t start
 See detailed pod info
kubectl describe pod <pod-name> -n release-dashboard

# Check events
kubectl get events -n release-dashboard --sort-by='.lastTimestamp'


Port forward for frontend

kubectl port-forward -n release-dashboard svc/frontend-service 8080:80

Port forward for backend

kubectl port-forward -n release-dashboard svc/backend-service 3000:3000

Port forward for mysql

kubectl port-forward -n release-dashboard svc/mysql-service 3306:3306

#To connect with the mysql client  mysql -h127.0.0.1 -uroot -proot release_dashboard  Run all three in background(one-liner)

kubectl port-forward -n release-dashboard svc/frontend-service 8080:80 &
kubectl port-forward -n release-dashboard svc/backend-service 3000:3000 &
kubectl port-forward -n release-dashboard svc/mysql-service 3306:3306 &


To stop all 

pkill -f "kubectl port-forward"

To kill all the port forward 

pkill -f "kubectl port-forward"



kubectl get all -n release-dashboard---
* kubectl get all -n release-dashboard — a command to list all resources in that namespace
* --- — YAML document separator
* Together: invalid and will cause YAML parsing errors


kubectl port-forward -n release-dashboard svc/frontend-service 8080:80 & kubectl port-forward -n release-dashboard svc/backend-service 3000:3000 &
kubectl port-forward -n release-dashboard svc/mysql-service 3306:3306 &



To delete the kubectl namespace
kubectl delete namespace test-frontend
kubectl port-forward -n release-dashboard-local svc/frontend-service 8080:80 & kubectl port-forward -n release-dashboard-local svc/backend-service 3000:3000 &
kubectl port-forward -n release-dashboard-local  svc/mysql-service 3306:3306 &




 mysql -h 127.0.0.1 -u root -proot release_dashboard
mysql: [Warning] Using a password on the command line interface can be insecure.
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)




Stop services without deleting them 
kubectl scale deployment --all --replicas=0 -n release-dashboard-local
