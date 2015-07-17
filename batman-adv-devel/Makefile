#
# Copyright (C) 2010 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# $Id: Makefile 5624 2006-11-23 00:29:07Z nbd $

include $(TOPDIR)/rules.mk

PKG_NAME:=batman-adv-devel

PKG_VERSION:=$(CONFIG_KMOD_BATMAN_ADV_DEVEL_BATMAN_REV)
PKG_BATMAN_SHORTREV:=$(shell echo $(PKG_VERSION) | cut -c1-7)
PKG_BATMAN_FNAME:=batman-adv-devel-$(PKG_BATMAN_SHORTREV).tar.gz
PKG_BATMAN_DIR:=batman-adv-$(PKG_BATMAN_SHORTREV)

PKG_SOURCE_URL:=http://git.open-mesh.org/snapshot/
PKG_SOURCE:=$(PKG_BATMAN_FNAME)

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)/$(PKG_BATMAN_DIR)

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/kernel.mk

define KernelPackage/batman-adv-devel
  URL:=http://www.open-mesh.org/
  MAINTAINER:=Marek Lindner <mareklindner@neomailbox.ch>
  SUBMENU:=Network Support
  DEPENDS:=+KMOD_BATMAN_ADV_DEVEL_BLA:kmod-lib-crc16 +kmod-crypto-core +kmod-crypto-crc32c +kmod-lib-crc32c
  KCONFIG:=\
          CONFIG_AVERAGE=y
  TITLE:=B.A.T.M.A.N. Adv devel
  FILES:=$(PKG_BUILD_DIR)/net/batman-adv/batman-adv.$(LINUX_KMOD_SUFFIX)
  AUTOLOAD:=$(call AutoLoad,50,batman-adv)
endef

define KernelPackage/batman-adv-devel/description
B.A.T.M.A.N. advanced is a kernel module which allows to
build layer 2 mesh networks. This package builds the latest
development version.
endef

define KernelPackage/batman-adv-devel/config
	source "$(SOURCE)/Config.in"
endef

define Package/kmod-batman-adv-devel/conffiles
/etc/config/batman-adv
endef

MAKE_BATMAN_ADV_ARGS += \
	CROSS_COMPILE="$(TARGET_CROSS)" \
	KERNELPATH="$(LINUX_DIR)" \
	ARCH="$(LINUX_KARCH)" \
	PATH="$(TARGET_PATH)" \
	SUBDIRS="$(PKG_BUILD_DIR)" \
	PWD="$(PKG_BUILD_DIR)" \
	LINUX_VERSION="$(LINUX_VERSION)" \
	CONFIG_BATMAN_ADV_DEBUG=$(if $(CONFIG_KMOD_BATMAN_ADV_DEVEL_DEBUG_LOG),y,n) \
	CONFIG_BATMAN_ADV_BLA=$(if $(CONFIG_KMOD_BATMAN_ADV_DEVEL_BLA),y,n) \
	CONFIG_BATMAN_ADV_DAT=$(if $(CONFIG_KMOD_BATMAN_ADV_DEVEL_DAT),y,n) \
	CONFIG_BATMAN_ADV_MCAST=$(if $(CONFIG_KMOD_BATMAN_ADV_DEVEL_MCAST),y,n) \
	CONFIG_BATMAN_ADV_NC=$(if $(CONFIG_KMOD_BATMAN_ADV_DEVEL_NC),y,n) \
	CONFIG_BATMAN_ADV_BATMAN_V=$(if $(CONFIG_KMOD_BATMAN_ADV_DEVEL_BATMAN_V),y,n) \
	REVISION="$(PKG_BATMAN_SHORTREV)" all

define Build/Prepare
	$(call Build/Prepare/Default)
	$(SED) '/#define _NET_BATMAN_ADV_MAIN_H_/a\#undef CONFIG_MODULE_STRIPPED' \
	    $(PKG_BUILD_DIR)/net/batman-adv/main.h
endef

define Build/Compile
	$(MAKE) -C "$(PKG_BUILD_DIR)" $(MAKE_BATMAN_ADV_ARGS)
endef

define Build/Clean
        rm -rf $(BUILD_DIR)/$(PKG_NAME)/
endef

define KernelPackage/batman-adv-devel/install
	$(INSTALL_DIR) $(1)/etc/config $(1)/etc/hotplug.d/net $(1)/etc/hotplug.d/iface $(1)/lib/batman-adv $(1)/usr/sbin $(1)/lib/netifd/proto
	$(INSTALL_DATA) ./files/etc/config/batman-adv $(1)/etc/config
	$(INSTALL_DATA) ./files/lib/batman-adv/config.sh $(1)/lib/batman-adv
	$(INSTALL_BIN) ./files/etc/hotplug.d/net/99-batman-adv $(1)/etc/hotplug.d/net
	$(INSTALL_BIN) ./files/lib/netifd/proto/batadv.sh $(1)/lib/netifd/proto
	$(INSTALL_BIN) ./files/lib/netifd/proto/batadv_vlan.sh $(1)/lib/netifd/proto
endef

$(eval $(call KernelPackage,batman-adv-devel))
