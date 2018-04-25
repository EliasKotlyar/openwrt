#
# Copyright (C) 2009-2010 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# $(call MkImageGzip,$(KDIR)/vmlinux,$(KDIR)/uimage)
# mkimage -A MIPS -O linux -T firmware -C none -a 0 -e 0 -n jz_fw -d $(1).gz $(2)

JFFS2_BLOCKSIZE=256k 512k

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/image.mk

ifneq ($(CONFIG_TARGET_xburst_qi_lb60),)
UBI_OPTS = -m 4096 -p 512KiB
UBIFS_OPTS = -m 4096 -e 516096 -c 4095
else
UBI_OPTS = -m 2048 -p 128KiB -s 512
UBIFS_OPTS = -m 2048 -e 126KiB -c 4096
endif

UIMAGE:=$(BIN_DIR)/$(IMG_PREFIX)-uImage.bin
ROOTIMAGE:=$(BIN_DIR)/$(IMG_PREFIX)-rootfs.bin

define Image/Prepare
	cat $(KDIR)/vmlinux | $(STAGING_DIR_HOST)/bin/lzma e -si -so -eos -lc1 -lp2 -pb2 > $(KDIR)/vmlinux.lzma
	mkimage -A mips -O linux -T kernel -a 0x80010000 -C lzma -n 'MIPS OpenWrt Linux-$(LINUX_VERSION)' -d $(KDIR)/vmlinux.lzma $(KDIR)/uimage
endef

define Image/BuildKernel
	cp $(KDIR)/uimage $(UIMAGE)
endef

define Image/Build/squashfs
	$(call prepare_generic_squashfs,$(KDIR)/root.squashfs)
endef

define Image/Build
	# Copy root
	#dd if=$(KDIR)/root.$(1) of=$(BIN_DIR)/$(IMG_PREFIX)-root.$(1) bs=128k conv=sync
	# Pad Kernel:
	#dd if=/dev/null of=$(UIMAGE) bs=1 count=0 seek=0x200000
	cp $(KDIR)/root.squashfs $(ROOTIMAGE)
	# Pad Rootfs:
	#dd if=/dev/null of=$(ROOTIMAGE) bs=1 count=0 seek=0x350000
endef

$(eval $(call BuildImage))
