CONTAINER_TOOL ?= podman

MAKEFLAGS += -j2

help:
	@echo "Build a instructlab container image for specified vendor "
	@echo
	@echo "  - make amd"
	@echo "  - make intel"
	@echo "  - make nvidia"
	@echo "  - make nvidia-quay"
	@echo "  - make amd-quay"
	@echo "  - make intel-quay"

default: help

.PHONY: all
all: nvidia intel amd

INSTRUCTLAB_GIT_REPO ?= https://github.com/instructlab/instructlab.git
INSTRUCTLAB_GIT_BRANCH ?= main

.PHONY: instructlab
instructlab:
	@mkdir -p ../build
	git clone $(INSTRUCTLAB_GIT_REPO) instructlab 2> /dev/null || true
	(cd instructlab; git pull origin $(INSTRUCTLAB_GIT_BRANCH))

.PHONY: nvidia
nvidia: instructlab
	rm -rf ../build/instructlab-$@
	"${CONTAINER_TOOL}" build --squash-all -t oci:../build/instructlab-$@ instructlab/containers/cuda

.PHONY: amd
amd: instructlab
	rm -rf ../build/instructlab-$@
	"${CONTAINER_TOOL}" build --squash-all -t oci:../build/instructlab-$@ -f instructlab/containers/rocm/Containerfile instructlab

.PHONY: intel
intel: instructlab
	rm -rf ../build/instructlab-$@
	"${CONTAINER_TOOL}" build --squash-all -t oci:../build/instructlab-$@  -f instructlab/containers/hpu/Containerfile instructlab

.PHONY: nvidia-quay
nvidia: instructlab
	rm -rf ../build/instructlab-$@
	"${CONTAINER_TOOL}" build --squash-all -t quay.io/ai-lab/instructlab-nvidia:latest instructlab/containers/cuda
	"${CONTAINER_TOOL}" push quay.io/ai-lab/instructlab-nvidia:latest

.PHONY: amd-quay
amd: instructlab
	rm -rf ../build/instructlab-$@
	"${CONTAINER_TOOL}" build --squash-all -t quay.io/ai-lab/instructlab-amd:latest -f instructlab/containers/rocm/Containerfile instructlab
	"${CONTAINER_TOOL}" push quay.io/ai-lab/instructlab-amd:latest

.PHONY: intel-quay
intel: instructlab
	rm -rf ../build/instructlab-$@
	"${CONTAINER_TOOL}" build --squash-all -t quay.io/ai-lab/instructlab-intel:latest -f instructlab/containers/hpu/Containerfile instructlab
	"${CONTAINER_TOOL}" push quay.io/ai-lab/instructlab-intel:latest