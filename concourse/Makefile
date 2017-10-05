export PATH 	:=$(CURDIR)/bin:$(PATH)
SHELL 		:= env PATH=$(PATH) $(SHELL)

URL     	:= https://ci.gerhard.io
TEAM 		:= pipeline
FLY 		:= fly --target p

CONF 		:= $(PIPELINE)
ifeq ($(CONF),)
CONF 		:= simple
endif
PIPELINE 	?= $$USER

X 		:= openssl enc -aes-256-cbc -a
SECRETS 	?= secrets

default: help

login: ## Login to CI
	@$(FLY) login --concourse-url $(URL) --team-name $(TEAM)

team:
	@$(FLY) --target g set-team --team-name $(TEAM) --basic-auth-username $(U) --basic-auth-password $(P)

enc:
	@$(X) -salt -in $(SECRETS) -out $(SECRETS).x

dec:
	@$(X) -d -in $(SECRETS).x -out $(SECRETS)

set: dec
	@-$(FLY) set-pipeline --config $(CONF).yml --pipeline $(PIPELINE) --load-vars-from $(SECRETS) --var=user=$$USER && \
	  $(FLY) expose-pipeline --pipeline $(PIPELINE) && \
	  $(FLY) unpause-pipeline --pipeline $(PIPELINE)

delete: ## Delete pipeline
	$(FLY) destroy-pipeline --pipeline $(PIPELINE)

pipeline: set ## Configure pipeline
	@rm -f $(SECRETS)

preview:## Preview pipeline
	@open $(URL)/teams/$(TEAM)/pipelines/$(PIPELINE)

pipelines: ## List all pipelines
	@$(FLY) pipelines

help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

.PHONY: help login pipeline pipelines preview
