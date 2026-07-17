# Copyright 2018 The Prometheus Authors
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

MIBDIR   := mibs
MIB_PATH := 'mibs'
SNMP_YML ?= 'snmp.yml'

CURL_OPTS ?= -L -sS --retry 3 --retry-delay 3 --fail
CURL_USER_AGENT ?= -H "user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/138.0.0.0 Safari/537.36 snmp_exporter/generator"

REPO_TAG ?= $(shell git rev-parse --abbrev-ref HEAD)

DOCKER_IMAGE_NAME ?= snmp-generator
DOCKER_IMAGE_TAG  ?= $(subst /,-,$(REPO_TAG))
DOCKER_REPO       ?= prom

SANITIZED_DOCKER_IMAGE_TAG := $(subst +,-,$(DOCKER_IMAGE_TAG))

SELINUX_ENABLED := $(shell cat /sys/fs/selinux/enforce 2> /dev/null || echo 0)

ifeq ($(SELINUX_ENABLED),1)
  DOCKER_VOL_OPTS ?= :z
endif

LYNX_URL := https://download.lynx-technik.com/public/SNMP

.DEFAULT: all

.PHONY: all
mibs: mibs

clean:
	rm -rvf \
		$(MIBDIR)/* \
		$(MIBDIR)/.lynx \

generate:
	MIBDIRS=$(MIB_PATH) ../generator --fail-on-parse-errors generate --output-path=$(SNMP_YML)

parse_errors:
	MIBDIRS=$(MIB_PATH) ../generator --fail-on-parse-errors parse_errors

.PHONY: docker
docker:
	docker build -f ../Dockerfile --build-arg REPO_TAG="$(REPO_TAG)" -t "$(DOCKER_REPO)/$(DOCKER_IMAGE_NAME):$(SANITIZED_DOCKER_IMAGE_TAG)" .

.PHONY: docker-generate
docker-generate:
	docker run --rm -v "${PWD}:/opt$(DOCKER_VOL_OPTS)" "$(DOCKER_REPO)/$(DOCKER_IMAGE_NAME):$(SANITIZED_DOCKER_IMAGE_TAG)" --no-fail-on-parse-errors generate


mibs: \
	$(MIBDIR)/.lynx

$(MIBDIR)/.lynx:
	@echo ">> Downloading LYNX mibs"
	@curl $(CURL_OPTS) -o $(MIBDIR)/SNMPv2-CONF.mib $(LYNX_URL)/SNMPv2-CONF.mib
	@curl $(CURL_OPTS) -o $(MIBDIR)/SNMPv2-SMI.mib $(LYNX_URL)/SNMPv2-SMI.mib
	@curl $(CURL_OPTS) -o $(MIBDIR)/SNMPv2-TC.mib $(LYNX_URL)/SNMPv2-TC.mib
	@curl $(CURL_OPTS) -o $(MIBDIR)/LYNX-SMI.mib $(LYNX_URL)/LYNX-SMI.mib
	@curl $(CURL_OPTS) -o $(MIBDIR)/RCT5023-G_c208_sw734.mib $(LYNX_URL)/RCT5023-G_c208_sw734.mib
	@curl $(CURL_OPTS) -o $(MIBDIR)/RCT5023-GS_c209_sw734.mib $(LYNX_URL)/RCT5023-GS_c209_sw734.mib
	@curl $(CURL_OPTS) -o $(MIBDIR)/DVD5820_08c7_sw704.mib $(LYNX_URL)/DVD5820_08c7_sw704.mib
	@curl $(CURL_OPTS) -o $(MIBDIR)/DVD5830_08d3_sw619.mib $(LYNX_URL)/DVD5830_08d3_sw619.mib
	@curl $(CURL_OPTS) -o $(MIBDIR)/DVO5820_08c9_sw704.mib $(LYNX_URL)/DVO5820_08c9_sw704.mib
	@curl $(CURL_OPTS) -o $(MIBDIR)/OTX5840_08db_sw619.mib $(LYNX_URL)/OTX5840_08db_sw619.mib
	@curl $(CURL_OPTS) -o $(MIBDIR)/ORX5800_08dc_sw619.mib $(LYNX_URL)/ORX5800_08dc_sw619.mib
	@curl $(CURL_OPTS) -o $(MIBDIR)/PVD5800-O_07f1_sw762.mib $(LYNX_URL)/PVD5800-O_07f1_sw762.mib
	@touch $(MIBDIR)/.lynx


