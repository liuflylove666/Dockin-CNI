BINARY="dockin-ipam"
VERSION=0.1.0
BUILD=`date +%FT%T%z`

PACKAGES=`go list ./... | grep -v /vendor/`
VETPACKAGES=`go list ./... | grep -v /vendor/ | grep -v /examples/`
GOFILES=`find . -name "*.go" -type f -not -path "./vendor/*"`

OS=linux
ARCH=amd64

default:
	go mod tidy
	go mod vendor
	mkdir -p build
	cd build && GOOS=${OS} GOARCH=${ARCH} go build -o ${BINARY} ../cmd


list:
	echo ${PACKAGES}
	echo ${VETPACKAGES}
	echo ${GOFILES}

fmt:
	gofmt -s -w ${GOFILES}

fmt-check:
	diff=?(gofmt -s -d $(GOFILES)); \
	if [ -n "$$diff" ]; then \
		echo "Please run 'make fmt' and commit the result:"; \
		echo "$${diff}"; \
		exit 1; \
	fi;

vet:
	go vet $(VETPACKAGES)

clean:
	if [ -f ${BINARY} ] ; then rm ${BINARY} ; fi


.PHONY: default fmt fmt-check install vet clean
