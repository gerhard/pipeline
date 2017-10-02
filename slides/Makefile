default: local

local: ## Run locally
	@npm start

public:## Deploy
	@cf target -o gerhard -s lrun17
	@cf push

help:
	@grep -E '^[a-zA-Z_-]+:.*?## .*$$' $(MAKEFILE_LIST) | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

.PHONY: local public help

