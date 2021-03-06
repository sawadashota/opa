DOCKER := docker

WASM_BUILDER_REPOSITORY := openpolicyagent/opa-wasm-builder
WASM_BUILDER_VERSION := 1.0
WASM_BUILDER_IMAGE := $(WASM_BUILDER_REPOSITORY):$(WASM_BUILDER_VERSION)
WASM_OBJ_DIR := _obj

$(shell mkdir -p $(WASM_OBJ_DIR))

CFLAGS := \
	-O3 \
	-nostdinc \
	-nodefaultlibs \
	--target=wasm32-unknown-unknown-wasm \
	-I src/lib \
	-I src/libmpdec \
	-DCONFIG_32 -DANSI

.PHONY: all
all: build test

.PHONY: clean
clean:
	rm -fr $(WASM_OBJ_DIR)

.PHONY: builder
builder: Dockerfile
	@$(DOCKER) build -t $(WASM_BUILDER_IMAGE) -f Dockerfile .

.PHONY: build
build:
	@$(DOCKER) run -it --rm -v $(CURDIR):/src $(WASM_BUILDER_IMAGE) make $(WASM_OBJ_DIR)/opa.wasm

.PHONY: test
test:
	@$(DOCKER) run -it --rm -v $(CURDIR):/src $(WASM_BUILDER_IMAGE) make $(WASM_OBJ_DIR)/opa-test.wasm
	@$(DOCKER) run -it --rm -e VERBOSE=$(VERBOSE) -v $(CURDIR):/src -w /src node:8 node test.js $(WASM_OBJ_DIR)/opa-test.wasm

.PHONY: hack
hack:
	@$(DOCKER) run -it --rm -v $(CURDIR):/src $(WASM_BUILDER_IMAGE)

$(WASM_OBJ_DIR)/arithmetic.wasm: src/arithmetic.c
	@$(CC) $(CFLAGS) -c $^ -o $@

$(WASM_OBJ_DIR)/array.wasm: src/array.c
	@$(CC) $(CFLAGS) -c $^ -o $@

$(WASM_OBJ_DIR)/bits-builtins.wasm: src/bits-builtins.c
	@$(CC) $(CFLAGS) -c $^ -o $@

$(WASM_OBJ_DIR)/context.wasm: src/context.c
	@$(CC) $(CFLAGS) -c $^ -o $@

$(WASM_OBJ_DIR)/json.wasm: src/json.c
	@$(CC) $(CFLAGS) -c $^ -o $@

$(WASM_OBJ_DIR)/malloc.wasm: src/malloc.c
	@$(CC) $(CFLAGS) -c $^ -o $@

$(WASM_OBJ_DIR)/mpd.wasm: src/mpd.c
	@$(CC) $(CFLAGS) -c $^ -o $@

$(WASM_OBJ_DIR)/set.wasm: src/set.c
	@$(CC) $(CFLAGS) -c $^ -o $@

$(WASM_OBJ_DIR)/str.wasm: src/str.c
	@$(CC) $(CFLAGS) -c $^ -o $@

$(WASM_OBJ_DIR)/types.wasm: src/types.c
	@$(CC) $(CFLAGS) -c $^ -o $@

$(WASM_OBJ_DIR)/unicode.wasm: src/unicode.c
	@$(CC) $(CFLAGS) -c $^ -o $@

$(WASM_OBJ_DIR)/value.wasm: src/value.c
	@$(CC) $(CFLAGS) -c $^ -o $@

$(WASM_OBJ_DIR)/lib-%.wasm: src/lib/%.c
	@$(CC) $(CFLAGS) -c $^ -o $@

$(WASM_OBJ_DIR)/libmpdec-%.wasm: src/libmpdec/%.c
	@$(CC) $(CFLAGS) -c $^ -o $@

$(WASM_OBJ_DIR)/opa.wasm: \
		$(WASM_OBJ_DIR)/arithmetic.wasm \
		$(WASM_OBJ_DIR)/array.wasm \
		$(WASM_OBJ_DIR)/bits-builtins.wasm \
		$(WASM_OBJ_DIR)/context.wasm \
		$(WASM_OBJ_DIR)/json.wasm \
		$(WASM_OBJ_DIR)/lib-ctype.wasm \
		$(WASM_OBJ_DIR)/lib-errno.wasm \
		$(WASM_OBJ_DIR)/lib-locale.wasm \
		$(WASM_OBJ_DIR)/lib-math.wasm \
		$(WASM_OBJ_DIR)/lib-printf.wasm \
		$(WASM_OBJ_DIR)/lib-stdio.wasm \
		$(WASM_OBJ_DIR)/lib-stdlib.wasm \
		$(WASM_OBJ_DIR)/lib-string.wasm \
		$(WASM_OBJ_DIR)/libmpdec-basearith.wasm \
		$(WASM_OBJ_DIR)/libmpdec-constants.wasm \
		$(WASM_OBJ_DIR)/libmpdec-context.wasm \
		$(WASM_OBJ_DIR)/libmpdec-convolute.wasm \
		$(WASM_OBJ_DIR)/libmpdec-crt.wasm \
		$(WASM_OBJ_DIR)/libmpdec-difradix2.wasm \
		$(WASM_OBJ_DIR)/libmpdec-fnt.wasm \
		$(WASM_OBJ_DIR)/libmpdec-fourstep.wasm \
		$(WASM_OBJ_DIR)/libmpdec-io.wasm \
		$(WASM_OBJ_DIR)/libmpdec-memory.wasm \
		$(WASM_OBJ_DIR)/libmpdec-mpdecimal.wasm \
		$(WASM_OBJ_DIR)/libmpdec-numbertheory.wasm \
		$(WASM_OBJ_DIR)/libmpdec-sixstep.wasm \
		$(WASM_OBJ_DIR)/libmpdec-transpose.wasm \
		$(WASM_OBJ_DIR)/malloc.wasm \
		$(WASM_OBJ_DIR)/mpd.wasm \
		$(WASM_OBJ_DIR)/str.wasm \
		$(WASM_OBJ_DIR)/set.wasm \
		$(WASM_OBJ_DIR)/types.wasm \
		$(WASM_OBJ_DIR)/unicode.wasm \
		$(WASM_OBJ_DIR)/value.wasm
	@wasm-ld-8 \
			--allow-undefined-file=src/undefined.symbols \
			--import-memory \
			--no-entry \
			--export-all \
			$^ \
			-o $@
	@wasm2wat $(WASM_OBJ_DIR)/opa.wasm > $(WASM_OBJ_DIR)/opa.wast

$(WASM_OBJ_DIR)/test.wasm: tests/test.c
	@$(CC) $(CFLAGS) -I src -c $^ -o $@

$(WASM_OBJ_DIR)/opa-test.wasm: \
		$(WASM_OBJ_DIR)/arithmetic.wasm \
		$(WASM_OBJ_DIR)/array.wasm \
		$(WASM_OBJ_DIR)/bits-builtins.wasm \
		$(WASM_OBJ_DIR)/context.wasm \
		$(WASM_OBJ_DIR)/json.wasm \
		$(WASM_OBJ_DIR)/lib-ctype.wasm \
		$(WASM_OBJ_DIR)/lib-errno.wasm \
		$(WASM_OBJ_DIR)/lib-locale.wasm \
		$(WASM_OBJ_DIR)/lib-math.wasm \
		$(WASM_OBJ_DIR)/lib-printf.wasm \
		$(WASM_OBJ_DIR)/lib-stdio.wasm \
		$(WASM_OBJ_DIR)/lib-stdlib.wasm \
		$(WASM_OBJ_DIR)/lib-string.wasm \
		$(WASM_OBJ_DIR)/libmpdec-basearith.wasm \
		$(WASM_OBJ_DIR)/libmpdec-constants.wasm \
		$(WASM_OBJ_DIR)/libmpdec-context.wasm \
		$(WASM_OBJ_DIR)/libmpdec-convolute.wasm \
		$(WASM_OBJ_DIR)/libmpdec-crt.wasm \
		$(WASM_OBJ_DIR)/libmpdec-difradix2.wasm \
		$(WASM_OBJ_DIR)/libmpdec-fnt.wasm \
		$(WASM_OBJ_DIR)/libmpdec-fourstep.wasm \
		$(WASM_OBJ_DIR)/libmpdec-io.wasm \
		$(WASM_OBJ_DIR)/libmpdec-memory.wasm \
		$(WASM_OBJ_DIR)/libmpdec-mpdecimal.wasm \
		$(WASM_OBJ_DIR)/libmpdec-numbertheory.wasm \
		$(WASM_OBJ_DIR)/libmpdec-sixstep.wasm \
		$(WASM_OBJ_DIR)/libmpdec-transpose.wasm \
		$(WASM_OBJ_DIR)/malloc.wasm \
		$(WASM_OBJ_DIR)/mpd.wasm \
		$(WASM_OBJ_DIR)/str.wasm \
		$(WASM_OBJ_DIR)/set.wasm \
		$(WASM_OBJ_DIR)/test.wasm \
		$(WASM_OBJ_DIR)/types.wasm \
		$(WASM_OBJ_DIR)/unicode.wasm \
		$(WASM_OBJ_DIR)/value.wasm
	@cat src/undefined.symbols tests/undefined.symbols > _obj/undefined.symbols
	@wasm-ld-8 \
			--allow-undefined-file=_obj/undefined.symbols \
			--import-memory \
			--no-entry \
			--export-all \
			$^ \
			-o $@
