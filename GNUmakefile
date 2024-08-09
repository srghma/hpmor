# Make a PDF and e-book release

PROJECT=hpmor

TAG := $(shell git describe --tags)
VERSION := $(shell echo $(TAG) | sed -e 's/^v//')
EBOOKS = $(PROJECT).epub $(PROJECT).mobi $(PROJECT).fb2
ZIPFILE = $(PROJECT)-$(VERSION).zip

all: ebooks pdf

pdf:
	latexmk

ebooks: pdf
	sh scripts/make_ebooks.sh

zip: pdf ebooks
	rm -f $(ZIPFILE) && \
	zip $(ZIPFILE) *.pdf $(EBOOKS)

# To make a release: git tag vx.y && make release
# Needs woger from https://github.com/rrthomas/woger/
release: zip
	git diff --exit-code && \
	git push --tags && \
	woger github package=$(PROJECT) version=$(VERSION) dist_type=zip && \
	for file in $(PROJECT).pdf $(EBOOKS); do \
		suffix=$${file##*.}; \
		gh release upload $(TAG) $$file#$(PROJECT)-$(VERSION).$$suffix; \
	done

docker_build:
	docker build -t hpmor .

docker_run:
	docker run -it -v ./:/app hpmor bash

docker_run__latexmk:
	docker run -it -v ./:/app hpmor latexmk hpmor
