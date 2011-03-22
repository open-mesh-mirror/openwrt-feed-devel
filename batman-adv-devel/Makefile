#
# Copyright (C) 2010 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# $Id: Makefile 5624 2006-11-23 00:29:07Z nbd $

include $(TOPDIR)/rules.mk

PKG_NAME:=batman-adv-devel

PKG_BATMAN_REV:=3cc9ba15c95147eeb70529ca3892853b3c81a862
PKG_BATCTL_REV:=6f77463027311b49d52bd3aef4d82c8aae27014f

PKG_BATMAN_SHORTREV:=$(shell echo $(PKG_BATMAN_REV) | cut -c1-7)
PKG_BATMAN_FNAME:=batman-adv-devel-$(PKG_BATMAN_SHORTREV).tar.gz
PKG_BATMAN_DIR:=batman-adv-$(PKG_BATMAN_SHORTREV)
PKG_BATCTL_SHORTREV:=$(shell echo $(PKG_BATCTL_REV) | cut -c1-7)
PKG_BATCTL_FNAME:=batctl-devel-$(PKG_BATCTL_SHORTREV).tar.gz
PKG_BATCTL_DIR:=batctl-$(PKG_BATCTL_SHORTREV)

PKG_SOURCE_URL:=http://git.open-mesh.org/snapshot/$(PKG_BATMAN_VERSION)
PKG_SOURCE:=$(PKG_BATMAN_FNAME)

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)/$(PKG_BATMAN_DIR)
PKG_BATCTL_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)/$(PKG_BATCTL_DIR)

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/kernel.mk

define KernelPackage/batman-adv-devel
  URL:=http://www.open-mesh.org/
  MAINTAINER:=Marek Lindner <lindner_marek@yahoo.de>
  SUBMENU:=Network Support
  DEPENDS:=@!LINUX_2_4
  TITLE:=B.A.T.M.A.N. Adv devel
  FILES:=$(PKG_BUILD_DIR)/batman-adv.$(LINUX_KMOD_SUFFIX)
  AUTOLOAD:=$(call AutoLoad,50,batman-adv)
endef

define KernelPackage/batman-adv-devel/description
B.A.T.M.A.N. advanced is a kernel module which allows to
build layer 2 mesh networks. This package builds the latest
development version ($(PKG_BATMAN_SHORTREV)) of the kernel module plus 
its user space configuration & managerment tool batctl.
endef

define KernelPackage/batman-adv-devel/config
	source "$(SOURCE)/Config.in"
endef

MAKE_BATMAN_ADV_ARGS += \
	CROSS_COMPILE="$(TARGET_CROSS)" \
	ARCH="$(LINUX_KARCH)" \
	PATH="$(TARGET_PATH)" \
	$(if $(CONFIG_KMOD_BATMAN_ADV_DEVEL_DEBUG_LOG),EXTRA_CFLAGS="-DCONFIG_BATMAN_ADV_DEBUG -DREVISION_VERSION=\\\"$(PKG_BATMAN_SHORTREV)\\\"") \
	SUBDIRS="$(PKG_BUILD_DIR)" \
	LINUX_VERSION="$(LINUX_VERSION)" \
	REVISION="$(PKG_BATMAN_SHORTREV)" modules

MAKE_BATCTL_ARGS += \
	CFLAGS="$(TARGET_CFLAGS)" \
	CCFLAGS="$(TARGET_CFLAGS)" \
	OFLAGS="$(TARGET_CFLAGS)" \
	REVISION="$(PKG_BATCTL_SHORTREV)" \
	CC="$(TARGET_CC)" \
	NODEBUG=1 \
	UNAME="Linux" \
	INSTALL_PREFIX="$(PKG_INSTALL_DIR)" \
	STRIP="/bin/true" \
	batctl install

ifneq ($(DEVELOPER)$(CONFIG_KMOD_BATMAN_ADV_DEVEL_BATCTL),)
define Download/batctl
  FILE:=$(PKG_BATCTL_FNAME)
  URL:=$(PKG_SOURCE_URL)
endef
$(eval $(call Download,batctl))

EXTRACT_BATCTL = tar xzf "$(DL_DIR)/$(PKG_BATCTL_FNAME)" -C "$(BUILD_DIR)/$(PKG_NAME)"
BUILD_BATCTL = $(MAKE) -C $(PKG_BATCTL_BUILD_DIR) $(MAKE_BATCTL_ARGS)
endif

define Build/Compile
	cp $(PKG_BUILD_DIR)/Makefile.kbuild $(PKG_BUILD_DIR)/Makefile
	$(MAKE) -C "$(LINUX_DIR)" $(MAKE_BATMAN_ADV_ARGS)
	$(EXTRACT_BATCTL)
	$(BUILD_BATCTL)
endef

define Build/Clean
        rm -rf $(BUILD_DIR)/$(PKG_NAME)/
endef

ifneq ($(DEVELOPER)$(CONFIG_KMOD_BATMAN_ADV_DEVEL_BATCTL),)
define KernelPackage/batman-adv-devel/install
	$(INSTALL_DIR) $(1)/etc/config $(1)/etc/init.d
	$(INSTALL_BIN) ./files/etc/init.d/batman-adv $(1)/etc/init.d
	$(INSTALL_DATA) ./files/etc/config/batman-adv $(1)/etc/config
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/batctl $(1)/usr/sbin/
endef
else
define KernelPackage/batman-adv-devel/install
	$(INSTALL_DIR) $(1)/etc/config $(1)/etc/init.d
	$(INSTALL_BIN) ./files/etc/init.d/batman-adv $(1)/etc/init.d
	$(INSTALL_DATA) ./files/etc/config/batman-adv $(1)/etc/config
endef
endif

$(eval $(call KernelPackage,batman-adv-devel))
