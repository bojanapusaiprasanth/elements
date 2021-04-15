.DEFAULT_GOAL := build

motags ?=
prefix = saiprasanth981/elementconnect
myname := "elementconnect"
mydir := $(shell pwd)
image := saiprasanth981/elementconnect
tag := $(shell jq -r ".version" "$(mydir)/package.json")
sysname := $(shell uname -s | awk '{print tolower($$0)}')

# release.artifact: @ creates deployable release artifact
release.artifact:
	@printf "\033[36m## $(myname): release.artifact\033[0m\n"
	@which docker 1>/dev/null || { printf "docker binary not found\n" && exit 1 ;}
	docker build --no-cache -f "$(mydir)/deploy/Dockerfile" -t "$(image)" "$(mydir)"

	docker tag "$(image)" "$(image):$(tag)"
	docker tag "$(image)" "$(image):latest"
.PHONY: release.artifact

# release.publish: @ publishes release artifact(s)
release.publish:
	@printf "\033[36m## $(myname): release.publish\033[0m\n"
	docker push "$(image):$(tag)"
	docker push "$(image):latest"
.PHONY: release.publish

# release.security: @ performs security scans on artifact
release.security: release.artifact
	@printf "\033[36m## $(myname): release.security (full-report)\033[0m\n"
#	trivy --exit-code 0 "$(image):$(tag)"
	@printf "\n\033[36m## $(myname): release.security (ignore-unfixed)\033[0m\n"
	docker run -v /var/run/docker.sock:/var/run/docker.sock  aquasec/trivy --exit-code 0 --severity LOW,MEDIUM --ignore-unfixed "$(image):$(tag)"
#	trivy --clear-cache
	docker run --rm -v /var/run/docker.sock:/var/run/docker.sock  aquasec/trivy --exit-code 1 --severity HIGH,CRITICAL --ignore-unfixed "$(image):$(tag)"
.PHONY: release.security

# Define the commandline invocation of Maven if necessary:
ifeq ($(MVN),)
    MVN  := mvn
endif

all: build push

mvn-clean:
	@ mvn clean

mvn-compile: 
	@ mvn compile

offline-dependency-test:
	@ mvn dependency:go-offline

mvn-test:
	@ mvn clean test -U

mvn-install:
	@ mvn clean install

build:
	@docker build -t $(prefix):$(tag) .
	@for t in $(motags); do docker tag $(prefix):$(tag) $(prefix):$$t; done

clean:
	@for t in $(tag) $(motags); do docker rmi $(prefix):$$t -f; done

push:
	@for t in $(tag) $(motags); do docker push $(prefix):$$t; done

#clean:
#	@- rm -rf ./bin/*
#	@- rm -rf ./build/*
#	@- rm -rf ./docs/*	

.PHONY: mvn-compile mvn-clean offline-dependency-test mvn-test mvn-install clean build push
