# Helps with git hooks for README.md maintenance, testing, multi-architecture
# builds, and pushing images to docker hub.

SHELL=/bin/bash
GITLINKS= ../.git/hooks/pre-commit ../.git/hooks/pre-push

# Thanks to https://gist.github.com/mpneuried/0594963ad38e68917ef189b4e6a269db
# Requires gmake (GNU make)

ifndef APP_NAME
APP_NAME=$(shell sed 's,.*/docker-\(.*\)/d,\1,' <<< $(PWD))
endif
ifndef APP_NAME
$(error APP_NAME undefined; expecting to be in directory named "docker-foo" or to be called with variable setting, as in "APP_NAME=foo make ...")
endif
#APP_NAME=noid

DOCKER_REPO=jakkbl
VERSION=1.0
DIR=/app/home/$(APP_NAME)

.DEFAULT_GOAL := help

# DOCKER TASKS

rerun: stop build run ## Stop, rebuild, and rerun container

shell: ## Set up an interactive shell to container
	docker exec -it $(APP_NAME) /bin/bash

build: ## Build container
	docker compose build --no-cache $(APP_NAME)

run: ## Run container, detached (and removed after stopping)
	docker compose run $(APP_NAME)

test: build ## Run application tests in the container
	docker compose run -T $(APP_NAME) $(APP_NAME) test

dockerhub: mybuilder mabuild ## Push multi-architecture image to hub.docker.com

mybuilder: ## Create and use mybuilder
	docker buildx create --name mybuilder --use --bootstrap

mabuild: repo-login ## Build multi-architecture image and push to dockerhub
	docker buildx build --platform linux/amd64,linux/arm64,linux/arm/v7 -t $(DOCKER_REPO)/$(APP_NAME) --push .

# use /bin/echo below else -n isn't recognized
stop: ## Stop running container
	@docker ps --filter name=$(APP_NAME) | grep -wq $(APP_NAME) && { \
		/bin/echo -n 'Stopping container "$(APP_NAME)" ... ' ; \
		docker stop $(APP_NAME) > /dev/null ; \
		/bin/echo stopped. ; \
	} || true

# Docker tagging
tag: tag-latest tag-version ## Generate container tags for the `{version}` and `latest` tags

tag-latest: ## Generate container `{version}` tag
	@echo 'create tag latest'
	docker tag $(APP_NAME) $(DOCKER_REPO)/$(APP_NAME):latest

tag-version: ## Generate container `latest` tag
	@echo 'create tag $(VERSION)'
	docker tag $(APP_NAME) $(DOCKER_REPO)/$(APP_NAME):$(VERSION)

manpage: ../README.md ## Generate plain text manual page and README.md

../README.md: $(APP_NAME)
	./$(APP_NAME) help | \
	sed -e '/^SYNOPSIS/s/S .*/S/' -e '/^[A-Z]/s/^/# /' -e 's/^    //' \
		> ../README.md

.PHONY: help
help: $(GITLINKS) ## This help information
	@echo Makefile targets in this directory are for docker hub repo: $(DOCKER_REPO)/$(APP_NAME)
	@echo Use 'make mybuilder mabuild' to create and push image to hub.docker.com.
	@awk 'BEGIN {FS = ":.*?## "} /^[a-zA-Z_-]+:.*?## / {printf "\033[36m%-17s\033[0m %s\n", $$1, $$2}' $(MAKEFILE_LIST)

../.git/hooks/pre-commit:
	(cd ../.git/hooks; ln -s ../../d/.githooks/pre-commit .)

../.git/hooks/pre-push:
	(cd ../.git/hooks; ln -s ../../d/.githooks/pre-push .)

# Docker publish
publish: repo-login publish-latest publish-version ## Publish the `{version}` and `latest` tagged containers

publish-latest: tag-latest ## Publish the `latest` taged container
	@echo 'publish latest to $(DOCKER_REPO)'
	docker push $(DOCKER_REPO)/$(APP_NAME):latest

publish-version: tag-version ## Publish the `{version}` taged container
	@echo 'publish $(VERSION) to $(DOCKER_REPO)'
	docker push $(DOCKER_REPO)/$(APP_NAME):$(VERSION)

release: build-nc publish ## Build and publish the `{version}` and `latest` containers

# HELPERS

repo-login: ## Login to hub.docker.com
	@docker login

version: ## Print current image version
	@echo $(VERSION) of $(APP_NAME)
