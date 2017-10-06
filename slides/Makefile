CF_ORG 		:= gerhard
CF_SPACE 	:= pipeline

default: slides

slides: ## Run locally
	@npm start

target:
	@cf target -o $(CF_ORG) -s $(CF_SPACE) || \
	  cf login -a $(CF_API) -u $(CF_USER) -p $(CF_PASS) -o $(CF_ORG) -s $(CF_SPACE)


public: target ## Deploy
	@cf push

help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

.PHONY: slides public help

