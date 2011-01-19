#
# Copyright (C) 2010 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# $Id: Makefile 5624 2006-11-23 00:29:07Z nbd $

include $(TOPDIR)/rules.mk

PKG_NAME:=batman-adv-devel

PKG_REV:=1902

PKG_VERSION:=r$(PKG_REV)
PKG_SOURCE_PROTO:=svn
PKG_SOURCE_VERSION:=$(PKG_REV)
PKG_SOURCE_URL:=http://downloads.open-mesh.org/svn/batman/trunk
PKG_SOURCE_SUBDIR:=$(PKG_NAME)
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/kernel.mk

define KernelPackage/batman-adv-devel
  URL:=http://www.open-mesh.org/
  MAINTAINER:=Marek Lindner <lindner_marek@yahoo.de>
  SUBMENU:=Network Support
  DEPENDS:=@!LINUX_2_4
  TITLE:=B.A.T.M.A.N. Adv devel
  FILES:=$(PKG_BUILD_DIR)/batman-adv/batman-adv.$(LINUX_KMOD_SUFFIX)
  AUTOLOAD:=$(call AutoLoad,50,batman-adv)
endef

define KernelPackage/batman-adv-devel/description
B.A.T.M.A.N. advanced is a kernel module which allows to
build layer 2 mesh networks. This package builds the latest
development version ($(PKG_VERSION)) of the kernel module plus
its user space configuration & managerment tool batctl.
endef

define KernelPackage/batman-adv-devel/config
	source "$(SOURCE)/Config.in"
endef

MAKE_BATMAN_ADV_ARGS += \
	CROSS_COMPILE="$(TARGET_CROSS)" \
	ARCH="$(LINUX_KARCH)" \
	PATH="$(TARGET_PATH)" \
	$(if $(CONFIG_KMOD_BATMAN_ADV_DEVEL_DEBUG_LOG),EXTRA_CFLAGS="-DCONFIG_BATMAN_ADV_DEBUG -DREVISION_VERSION=\\\"$(PKG_REV)\\\"") \
	SUBDIRS="$(PKG_BUILD_DIR)/batman-adv" \
	LINUX_VERSION="$(LINUX_VERSION)" \
	REVISION="$(PKG_REV)" modules

MAKE_BATCTL_ARGS += \
	CFLAGS="$(TARGET_CFLAGS)" \
	CCFLAGS="$(TARGET_CFLAGS)" \
	OFLAGS="$(TARGET_CFLAGS)" \
	REVISION="$(PKG_REV)" \
	CC="$(TARGET_CC)" \
	NODEBUG=1 \
	UNAME="Linux" \
	INSTALL_PREFIX="$(PKG_INSTALL_DIR)" \
	STRIP="/bin/true" \
	batctl install

ifneq ($(DEVELOPER)$(CONFIG_KMOD_BATMAN_ADV_DEVEL_BATCTL),)
BUILD_BATCTL = $(MAKE) -C $(PKG_BUILD_DIR)/batctl $(MAKE_BATCTL_ARGS)
endif

define Build/Compile
	cp $(PKG_BUILD_DIR)/batman-adv/Makefile.kbuild $(PKG_BUILD_DIR)/batman-adv/Makefile
	$(MAKE) -C "$(LINUX_DIR)" $(MAKE_BATMAN_ADV_ARGS)
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
