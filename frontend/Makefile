DOCKER := docker
DATA_VOLUME := $(shell pwd)
IMAGE := liberoadmin/prc-frontend
IMAGE_TAG := local

.PHONY: build dev install lint* prod test

dev: export TARGET = dev
dev: install build
	$(DOCKER) run -v $(DATA_VOLUME)/src:/app/src:ro -v $(DATA_VOLUME)/static:/app/static:ro -p 8080:80 $(IMAGE):$(IMAGE_TAG)-dev

prod: export TARGET = prod
prod: build
	$(DOCKER) run -p 8080:80 $(IMAGE):$(IMAGE_TAG)

lint: export TARGET = dev
lint: build
	$(DOCKER) run --rm $(IMAGE):$(IMAGE_TAG)-dev npm run lint

lint\:fix: export TARGET = dev
lint\:fix: build
	$(DOCKER) run --rm -v $(DATA_VOLUME)/src:/app/src -v $(DATA_VOLUME)/test:/app/test $(IMAGE):$(IMAGE_TAG)-dev npm run lint:fix

test: export TARGET = dev
test: build
	$(DOCKER) run $(IMAGE):$(IMAGE_TAG)-dev node_modules/.bin/jest

build:
	@if [ "$(TARGET)" != prod ]; then \
		image_tag_suffix=-dev; \
	fi; \
	$(DOCKER) build -t $(IMAGE):$(IMAGE_TAG)$${image_tag_suffix} . --target $(TARGET)

install: node_modules

node_modules: package.json package-lock.json
	npm install
	touch node_modules
