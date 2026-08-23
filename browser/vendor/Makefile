ALL := \
	lib/@aws-crypto/sha256-js/build/index.js \
	lib/@noble/hashes/sha2.js \
	lib/@noble/hashes/utils.js \
	lib/crypto-js/index.js \
	lib/fast-sha256/sha256.js \
	lib/hash-wasm/sha256.js \
	lib/hash.js/lib/hash/sha/256.js \
	lib/js-sha256/build/sha256.js \
	lib/jssha/dist/sha256.js \
	lib/node-forge/lib/sha256.js \
	lib/sha.js/sha256.js \

####

# Derive rollup's --name from the target path: drop "@", turn "/", "-" and "."
# into "_". $* only has a value while a recipe runs, so this must be = not :=.
NAME = $(subst /,_,$(subst -,_,$(subst .,_,$(subst @,,$*))))

all: $(ALL) sizes

lib/%.js: ../../node_modules/%.js
	@mkdir -p $(dir $@)
	../../node_modules/.bin/rollup --name $(NAME) \
	-p 'node-resolve={browser:true,preferBuiltins:false}' \
	-p 'commonjs' \
	--format iife $< -o $@

# Its per-algorithm UMD build is a standalone file that already carries
# the WebAssembly payload inline and is already minified, so re-wrapping
# it through rollup would gain nothing; copy it as it ships.
lib/hash-wasm/sha256.js: ../../node_modules/hash-wasm/dist/sha256.umd.min.js
	@mkdir -p $(dir $@)
	cp $< $@

lib/%.min.js: lib/%.js
	../../node_modules/.bin/terser $< -o $@ -c -m
	@ls -l $< $@

# The figures the README compatibility table quotes, in KB.
sizes: $(ALL:.js=.min.js)
	@for f in $^; do \
		printf '  %3dKB  %s\n' "$$(( ($$(wc -c < $$f) + 512) / 1024 ))" "$$f"; \
	done

####

clean:
	/bin/rm -fr lib

.PHONY: all clean sizes
