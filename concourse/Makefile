export PATH 	:=$(CURDIR)/bin:$(PATH)
SHELL 	:= env PATH=$(PATH) $(SHELL)

URL     := https://ci.gerhard.io
TEAM 	:= pipeline
FLY 	:= fly --target p
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

set-pipeline: $(SECRETS)
	@-$(FLY) set-pipeline --config $(CONF).yml --pipeline $$USER --load-vars-from $(SECRETS)

pipeline: set-pipeline ## Configure pipeline
	@rm -f $(SECRETS)

pipelines: ## List all pipelines
	@$(FLY) pipelines

help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

.PHONY: help login pipeline
