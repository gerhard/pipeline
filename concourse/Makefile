URL     := https://ci.gerhard.io
TEAM 	:= pipeline
PATH    := $(shell pwd)/bin:$(PATH)
FLY 	:= PATH=$(PATH) fly --target p
CONF 	?= simple

X 	:= openssl enc -aes-256-cbc -a
SECRETS := secrets.yml

default: help

login: ## Login to Concourse
	@$(FLY) login --concourse-url $(URL) --team-name $(TEAM)

secrets:
	@$(X) -salt -in $(SECRETS) -out $(SECRETS).enc

$(SECRETS):
	@$(X) -d -in $(SECRETS).enc -out $(SECRETS)

safe:
	@rm -f $(SECRETS)

set-pipeline:
	@-$(FLY) set-pipeline --config $(CONF).yml --pipeline $$USER --load-vars-from $(SECRETS)

pipeline: | $(SECRETS) set-pipeline safe ## Configure pipeline

pipelines: ## List all pipelines
	@$(FLY) pipelines

help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

.PHONY: help login pipeline
