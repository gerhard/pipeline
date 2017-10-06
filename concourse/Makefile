### CONFIG ###
#

export PATH 	:=$(CURDIR)/bin:/usr/local/opt/openssl/bin:$(PATH)
SHELL 		:= env PATH=$(PATH) $(SHELL)

URL     	:= https://ci.gerhard.io
TEAM 		:= pipeline
FLY 		:= fly --target p

PIPELINE 	?= $(USER)
X_CIPHER 	?= aes-256-cbc
X 		:= openssl enc -$(X_CIPHER) -a
SECRETS 	?= secrets


### TARGETS ###
#
default: help

builds: ## Show all builds
	@$(FLY) builds

containers: ## Show all containers
	@$(FLY) containers

dec:
	@$(X) -d -in $(SECRETS).x -out $(SECRETS)

destroy:## Destroy pipeline
	$(FLY) destroy-pipeline --pipeline $(PIPELINE)

enc:
	@$(X) -salt -in $(SECRETS) -out $(SECRETS).x

help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

login:  ## Login to CI
	@$(FLY) login --concourse-url $(URL) --team-name $(TEAM)

openssl:## Verify openssl version & required enc cipher type
	@openssl version
	@-openssl enc --fail 2>&1 | grep --only --color $(X_CIPHER)

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

unpause:## Unpause pipeline
	@-$(FLY) unpause-pipeline --pipeline $(PIPELINE)

volumes:## Show all volumes
	@$(FLY) volumes --details

workers: ## Show all workers
	@$(FLY) workers --details

.PHONY: builds containers dec destroy enc help login openssl pipeline pipelines preview production set set-pipeline simple team unpause volumes workers
