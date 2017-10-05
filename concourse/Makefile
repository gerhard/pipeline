export PATH 	:=$(CURDIR)/bin:$(PATH)
SHELL 	:= env PATH=$(PATH) $(SHELL)

URL     := https://ci.gerhard.io
TEAM 	:= pipeline
FLY 	:= fly --target p
CONF 	?= simple

X 	:= openssl enc -aes-256-cbc -a
SECRETS := secrets.yml

default: help

login: ## Login to CI
	@$(FLY) login --concourse-url $(URL) --team-name $(TEAM)

secrets:
	@$(X) -salt -in $(SECRETS) -out $(SECRETS).enc

$(SECRETS):
	@$(X) -d -in $(SECRETS).enc -out $(SECRETS)

set: $(SECRETS)
	@-$(FLY) set-pipeline --config $(CONF).yml --pipeline $$USER --load-vars-from $(SECRETS) --var=user=$$USER
	@-$(FLY) expose-pipeline --pipeline $$USER
	@-$(FLY) unpause-pipeline --pipeline $$USER

delete: ## Delete pipeline
	$(FLY) delete-pipeline --pipeline $$USER

pipeline: set ## Configure pipeline
	@rm -f $(SECRETS)

preview:## Preview pipeline
	@open $(URL)/teams/$(TEAM)/pipelines/$$USER

pipelines: ## List all pipelines
	@$(FLY) pipelines

help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

.PHONY: help secrets login pipeline pipelines preview
