
BASEPATH:=../../../

REGRESS := 1

PG_CONFIG ?= pg_config

# export pg_config for child make commands (check etc)
export PG_CONFIG

PGXS := $(shell $(PG_CONFIG) --pgxs)
include $(PGXS)

EXTENSIONLOAD :=  --load-extension=tsm_system_rows --load-extension=pg_cron --load-extension=vector --load-extension=postgis --load-extension citus  --load-extension=documentdb_core --load-extension=rum --load-extension=documentdb --load-extension=documentdb_distributed

MAKEFILE_DIR := $(dir $(realpath $(firstword $(MAKEFILE_LIST))))
export PATH := $(MAKEFILE_DIR)bin:$(PATH)
export PG_REGRESS_DIFF_OPTS = -dU10

export PGISOLATIONTIMEOUT = 60

.PHONY: check-basic check-dist-minimal

define common_test
	$(top_builddir)/src/test/regress/pg_regress --dlpath=$(BASEPATH) $(EXTENSIONLOAD) --temp-instance ./tmp --temp-config ./postgresql.conf --host localhost --port 58070 $(1) $(2) || (cat regression.diffs && false)
endef

define isolation_test
	$(top_builddir)/src/test/isolation/pg_isolation_regress --dlpath=$(BASEPATH) $(EXTENSIONLOAD) --temp-instance ./tmp --temp-config ./isolation_postgresql.conf --host localhost --port 58071 $(1) $(2) || (cat regression.diffs && false)
endef

check-dist-minimal:
	$(call common_test,--schedule=./minimal_schedule, $(EXTRA_TESTS))

check-basic:
	$(call common_test,--schedule=./basic_schedule --schedule=./basic_schedule_core)

check-test-output:
	./validate_test_output.sh

all: check-basic check-test-output
