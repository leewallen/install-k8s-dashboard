deploy:
	@echo "Deploying..."
	kubectl create -f ./kubernetes-dashboard.yml
	kubectl create -f dashboard.admin-user.yml -f dashboard.admin-user-role.yml
	kubectl -n kubernetes-dashboard describe secret admin-user | grep "^token:" | sed 's/.* //' | pbcopy
	@echo "http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/"

copytoken:
	kubectl -n kubernetes-dashboard describe secret admin-user | grep "^token:" | sed 's/.* //' | pbcopy

clean:
	@echo "Deleting resources for kubernetes dashboard..."
	kubectl delete clusterrolebinding admin-user
	kubectl delete clusterrolebinding kubernetes-dashboard
	kubectl delete clusterrole kubernetes-dashboard
	kubectl delete ns kubernetes-dashboard
	@echo "Done cleaning up resources."

get:
	kubectl get clusterroles
	kubectl get clusterrolebinding