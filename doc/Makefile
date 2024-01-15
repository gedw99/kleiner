SC=$(PWD)/_make

include $(SC)/os.mk

include $(SC)/caddy.mk
include $(SC)/gio.mk
include $(SC)/go.mk
include $(SC)/nats.mk

# MUST be last
include .env-git
include .env

# all binaries we make go here.
#BIN_ROOT=$(PWD)/.bin
#export $(PATH):=$(PATH):$(BIN_ROOT)

ci-all: print all

ci-test:
	@echo "hi ci "

print: os-print os-git-print env-print
	
all: dep-all dep-print build-all