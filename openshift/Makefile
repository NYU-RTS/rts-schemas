# Based on:
# - https://github.com/garethr/openshift-json-schema
# - https://cloud.redhat.com/blog/validating-openshift-manifests-in-a-gitops-world

ifeq ($(VERSION),)
VERSION := $(shell oc version -o json | jq -r 'if .releaseClientVersion then "v" + .releaseClientVersion | split(".")[:2] | join(".") else "master" end')
endif
OPENAPI_SPEC_FILE = openapi/openshift-openapi-spec-$(VERSION).json
FILTERED_SPEC_FILE = openapi/openshift-openapi-spec-$(VERSION)-filtered.json
PREFIX = https://raw.githubusercontent.com/NYU-RTS/rts-schemas/refs/heads/main/openshift/v4.21-standalone-strict/_definitions.json

define generate_schema
$(VERSION)$(if $(1),-$(1))/.jsonschema.stamp: $(FILTERED_SPEC_FILE)
	uv run openapi2jsonschema -o $$(dir $$@) --expanded --kubernetes $(2) $$<
	uv run openapi2jsonschema -o $$(dir $$@) --kubernetes $(2) $$<
	touch $$@

$(VERSION)$(if $(1),-$(1)): $(VERSION)$(if $(1),-$(1))/.jsonschema.stamp

$(VERSION).0$(if $(1),-$(1))/.jsonschema.stamp: $(VERSION)$(if $(1),-$(1))
	cp -a $$< $$(dir $$@)
	touch $$@

$(VERSION).0$(if $(1),-$(1)): $(VERSION).0$(if $(1),-$(1))/.jsonschema.stamp

SCHEMA_DIRS += $(VERSION)$(if $(1),-$(1)) $(VERSION).0$(if $(1),-$(1))
endef

all: build

$(OPENAPI_SPEC_FILE):
	mkdir -p $(dir $(OPENAPI_SPEC_FILE))
	oc get --raw /openapi/v2 >$@

# openapi2jsonschema --kubernetes unconditionally does title.split('.')[-3], crashing on
# short-named definitions (e.g. KubeVirt's "v1.VirtualMachineInstance"). Filter them out here.
$(FILTERED_SPEC_FILE): $(OPENAPI_SPEC_FILE)
	jq '.definitions |= with_entries(select(.key | split(".") | length >= 3))' $< >$@

$(eval $(call generate_schema,,--prefix $(PREFIX)))

$(eval $(call generate_schema,standalone,--stand-alone))

$(eval $(call generate_schema,standalone-strict,--stand-alone --strict))

$(eval $(call generate_schema,local,))

build: $(SCHEMA_DIRS)

test: lint

lint:
	pre-commit run --all

pre-commit-update:
	pre-commit autoupdate

clean:
	$(RM) -r $(SCHEMA_DIRS)

mrproper: clean
	$(RM) $(OPENAPI_SPEC_FILE)

.PHONY: all build clean lint mrproper pre-commit-update test
