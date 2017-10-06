### CONFIG ###
#

export PATH 	:=$(CURDIR)/bin:$(PATH)
SHELL 		:= env PATH=$(PATH) $(SHELL)

URL     	:= https://ci.gerhard.io
TEAM 		:= pipeline
FLY 		:= fly --target p

PIPELINE 	?= $(USER)
X 		:= openssl enc -aes-256-cbc -a
SECRETS 	?= secrets

default: help

### TARGETS ###
#

dec:
	@$(X) -d -in $(SECRETS).x -out $(SECRETS)

delete: ## Delete pipeline
	$(FLY) destroy-pipeline --pipeline $(PIPELINE)

enc:
	@$(X) -salt -in $(SECRETS) -out $(SECRETS).x

help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

login: ## Login to CI
	@$(FLY) login --concourse-url $(URL) --team-name $(TEAM)

pipeline: PIPELINE = pipeline
pipeline: CONF = pipeline.yml
pipeline: SECRETS = my_secrets
pipeline: set-pipeline

pipelines: ## List all pipelines
	@$(FLY) pipelines

preview:## Preview pipeline
	@open $(URL)/teams/$(TEAM)/pipelines/$(PIPELINE)

production: CONF = production.yml
production: set-pipeline ## Configure production web app pipeline

set: dec
	@-$(FLY) set-pipeline --config $(CONF) --pipeline $(PIPELINE) --load-vars-from $(SECRETS) --var=user=$(USER)
	@-$(FLY) expose-pipeline --pipeline $(PIPELINE)

set-pipeline: set
	@rm -f $(SECRETS)

simple: CONF = simple.yml
simple: set-pipeline ## Configure simple web app pipeline

team:
	@$(FLY) --target g set-team --team-name $(TEAM) --basic-auth-username $(U) --basic-auth-password $(P)

unpause: ## Unpause pipeline
	@-$(FLY) unpause-pipeline --pipeline $(PIPELINE)

.PHONY: dec delete enc help login pipeline pipelines preview production set set-pipeline simple team unpause
