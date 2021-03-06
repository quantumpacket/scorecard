SHELL := /bin/bash
IMAGE_NAME = gitcache
.PHONY: help
help:  ## Display this help
	@awk \
		-v "col=${COLOR}" -v "nocol=${NOCOLOR}" \
		' \
			BEGIN { \
				FS = ":.*##" ; \
				printf "Available targets:\n"; \
			} \
			/^[a-zA-Z0-9_-]+:.*?##/ { \
				printf "  %s%-25s%s %s\n", col, $$1, nocol, $$2 \
			} \
			/^##@/ { \
				printf "\n%s%s%s\n", col, substr($$0, 5), nocol \
			} \
		' $(MAKEFILE_LIST)

all:  ## Runs build
.PHONY: all
all: build 

.PHONY: build
build: ## Runs go build and generates executable
	CGO_ENABLED=0 go build -a -ldflags '-w -extldflags "-static"'  -o gitblobcache

.phony: dockerbuild
dockerbuild: ## runs docker build
	$(call ndef, github_auth_token)
	DOCKER_BUILDKIT=1 docker build .  --tag $(IMAGE_NAME)


check-env:
ifndef BLOB_URL
	$(error BLOB_URL is undefined)
endif

.PHONY: ci-e2e
ci-e2e: build check-env ## runs ci-e2e tests
	$(call ndef, BLOB_URL)
	ginkgo -v -cover ./e2e/...
