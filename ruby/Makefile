include ../Makefile.inc
UPSTREAM=https://cache.ruby-lang.org/pub/ruby/2.2/ruby-2.2.3.tar.gz
#export CFLAGS="-static"
ifeq ($(shell uname),Darwin)
ISO_OPTIONS=-joliet
else
ISO_OPTIONS=
endif

all: bin/ruby images/usr.iso

bin/ruby: build/ruby
	mkdir -p bin
	rumprun-bake hw_generic bin/ruby.bin build/rubydist/bin/ruby

# --with-out-ext=fiddle,bigdecimal,continuation,coverage,date,dbm,-test*;
build/ruby: build/Makefile
	(cd build; \
	./configure --enable-static --disable-shared --host=$(RUMPRUN_TOOLCHAIN_TUPLE) --with-static-linked-ext --prefix=$(shell pwd)/build/rubydist --disable-install-doc --with-out-ext=-test* LDFLAGS="-Bstatic -static"; \
	$(MAKE) --debug; make install)

build/Makefile:
	mkdir -p build
	../scripts/fetch.sh ${UPSTREAM} build/ruby.tar.gz
	(cd build && tar -xz --strip-components 1 -f ruby.tar.gz)
	(cd build && ../../scripts/apply-patches.sh ./ ../patches/*)

images/usr.iso: 
	mkdir -p images
	$(RUMPRUN_GENISOIMAGE) ${ISO_OPTIONS} -o images/usr.iso build/rubydist/

images/examples.iso:
	cd examples && bundle install --path=gems
	$(RUMPRUN_GENISOIMAGE) ${ISO_OPTIONS} -o images/examples.iso examples/
	
.PHONY: clean
clean:
	-$(MAKE) -C build clean
	rm -f bin/*
	rm -f images/*
