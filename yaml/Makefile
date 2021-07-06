
SHELL=/bin/bash

# to see all colors, run
# bash -c 'for c in {0..255}; do tput setaf $c; tput setaf $c | cat -v; echo =$c; done'
# the first 15 entries are the 8-bit colors

# define standard colors
ifneq (,$(findstring xterm,${TERM}))
	BLACK        := $(shell tput -Txterm setaf 0)
	RED          := $(shell tput -Txterm setaf 1)
	GREEN        := $(shell tput -Txterm setaf 2)
	YELLOW       := $(shell tput -Txterm setaf 3)
	LIGHTPURPLE  := $(shell tput -Txterm setaf 4)
	PURPLE       := $(shell tput -Txterm setaf 5)
	BLUE         := $(shell tput -Txterm setaf 6)
	WHITE        := $(shell tput -Txterm setaf 7)
	RESET := $(shell tput -Txterm sgr0)
else
	BLACK        := ""
	RED          := ""
	GREEN        := ""
	YELLOW       := ""
	LIGHTPURPLE  := ""
	PURPLE       := ""
	BLUE         := ""
	WHITE        := ""
	RESET        := ""
endif

# set target color
TARGET_COLOR := $(BLUE)

POUND = \#

.PHONY: no_targets__ info help build deploy doc
	no_targets__:

.DEFAULT_GOAL := help

colors: ## show all the colors
	@echo "${BLACK}BLACK${RESET}"
	@echo "${RED}RED${RESET}"
	@echo "${GREEN}GREEN${RESET}"
	@echo "${YELLOW}YELLOW${RESET}"
	@echo "${LIGHTPURPLE}LIGHTPURPLE${RESET}"
	@echo "${PURPLE}PURPLE${RESET}"
	@echo "${BLUE}BLUE${RESET}"
	@echo "${WHITE}WHITE${RESET}"


help:
	@echo "${WHITE}Commands available:${RESET}"
	@echo "${BLUE}deploy${YELLOW}		Creates Kubernetes dashboard resources.${RESET}"
	@echo "${BLUE}clean${YELLOW}		Deletes Kubernetes dashboard resources.${RESET}"
	@echo "${BLUE}copytoken${YELLOW}	Copies the admin-user token to the clipboard.${RESET}"
	@echo "${BLUE}showtoken${YELLOW}	Displays the admin-user token so you can use it for accessing the dashboard.${RESET}"
	@echo "${BLUE}get${YELLOW}		Displays Kubernetes dashboard resource info.${RESET}"

deploy:
	@echo "${WHITE}Deploying...${PURPLE}"
	kubectl create -f ./kubernetes-dashboard.yml
	kubectl create -f dashboard.admin-user.yml -f dashboard.admin-user-role.yml
	kubectl -n kubernetes-dashboard describe secret admin-user | grep "^token:" | sed 's/.* //' | pbcopy
	@echo "${BLUE}http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/${RESET}"

showtoken:
	kubectl -n kubernetes-dashboard describe secret admin-user | grep "^token:" | sed 's/.* //'

copytoken:
	kubectl -n kubernetes-dashboard describe secret admin-user | grep "^token:" | sed 's/.* //' | pbcopy

clean:
	@echo "${WHITE}Deleting resources for kubernetes dashboard...${PURPLE}"
	kubectl delete clusterrolebinding admin-user
	kubectl delete clusterrolebinding kubernetes-dashboard
	kubectl delete clusterrole kubernetes-dashboard
	kubectl delete ns kubernetes-dashboard
	@echo "${WHITE}Done cleaning up resources.${RESET}"

get:
	@echo -e "\n${WHITE}Get clusterroles:${PURPLE}"
	kubectl get clusterroles kubernetes-dashboard -o wide
	@echo -e  "\n${WHITE}Get clusterrolebinding for kubernetes-dashboard:${PURPLE}"
	kubectl get clusterrolebinding kubernetes-dashboard -o wide
	@echo -e  "\n${WHITE}Get clusterrolebinding for admin-user:${PURPLE}"
	kubectl get clusterrolebinding admin-user -o wide
	@echo -e "\n${WHITE}Get kubernetes-dashboard deployment:${PURPLE}"
	kubectl -n kubernetes-dashboard get deployment kubernetes-dashboard
	@echo -e "\n${WHITE}Get kubernetes-dashboard service:${PURPLE}"
	kubectl -n kubernetes-dashboard get service kubernetes-dashboard
	@echo -e "\n${WHITE}Get dashboard-metrics-scraper deployment:${PURPLE}"
	kubectl -n kubernetes-dashboard get deployment dashboard-metrics-scraper
	@echo -e "\n${WHITE}Get dashboard-metrics-scraper service:${PURPLE}"
	kubectl -n kubernetes-dashboard get service dashboard-metrics-scraper
	@echo "${RESET}"
