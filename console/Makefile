
DOCKER ?= docker
DOCKER_IMAGE_NAME ?= apache/polaris-console
DOCKER_IMAGE_TAG ?= latest

.PHONY: build-docker
build-docker:
	@$(DOCKER) build -f docker/Dockerfile -t $(DOCKER_IMAGE_NAME):$(DOCKER_IMAGE_TAG) .


.PHONY: install
install:
	npm ci

.PHONY: lint
lint: format-check
	npm run lint

.PHONY: format-fix
format-fix:
	npm run format

.PHONE: lint-fix
lint-fix: format-fix
	npm run lint -- --fix

.PHONY: format-check
format-check:
	npm run format:check

.PHONY: build
build: lint
	npm run build

.PHONY: dev
dev:
	npm run dev

# Reproducible build verification using devbox
.PHONY: verify-reproducible
verify-reproducible:
	@command -v devbox >/dev/null 2>&1 || { echo "Please install devbox: curl -fsSL https://get.jetify.com/devbox | bash"; exit 1; }
	@echo "Building twice and comparing checksums..."
	@devbox run build
	@tar --sort=name --mtime="1980-01-01 00:00:00 UTC" --owner=0 --group=0 --numeric-owner -cf /tmp/a.tar dist/
	@rm -rf dist
	@devbox run build
	@tar --sort=name --mtime="1980-01-01 00:00:00 UTC" --owner=0 --group=0 --numeric-owner -cf /tmp/b.tar dist/
	@echo "Build A:" && sha256sum /tmp/a.tar
	@echo "Build B:" && sha256sum /tmp/b.tar
	@diff <(sha256sum /tmp/a.tar | cut -d' ' -f1) <(sha256sum /tmp/b.tar | cut -d' ' -f1) && echo " Builds are reproducible!" || { echo "Builds are NOT reproducible"; exit 1; }
	@rm -f /tmp/a.tar /tmp/b.tar