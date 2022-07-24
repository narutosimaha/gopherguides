DOCKER_REGISTRY := qpass
DEPLOY := qpass-be
RELEASE_VERSION := $(shell git describe --tags | grep -o '[0-9]\+\.[0-9]\+\.[0-9]\+')

all: clean build start
prod: clean build release
dev: clean build debug

gen:
	go generate ./...
build: gen
	go build -mod=vendor -o ./bin/api ./example/api
start:
	export `cat .env | grep -v ^\# | xargs` && export DEBUG='0' &&./bin/api
debug:
	export `cat .env | grep -v ^\# | xargs` && export DEBUG='1' && ./bin/api
release:
	export `cat .env | grep -v ^\# | xargs` && export GIN_MODE=release && ./bin/api

version:
	echo $(RELEASE_VERSION)

docker-build:
	docker build -t $(DOCKER_REGISTRY)/$(DEPLOY):$(RELEASE_VERSION) -f ./deloy/Dockerfile .
docker-run:
	docker stop $(DEPLOY)
	docker container rm $(DEPLOY)
	docker run --name $(DEPLOY) $(DOCKER_REGISTRY)/$(DEPLOY):$(RELEASE_VERSION)
push:
	docker push $(DOCKER_REGISTRY)/$(DEPLOY):$(RELEASE_VERSION)
clean:
	rm -rf ./bin
	go mod tidy