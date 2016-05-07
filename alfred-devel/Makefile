#
# Copyright (C) 2013 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=alfred-devel

PKG_BRANCH:=$(call qstrip,$(CONFIG_ALFRED_DEVEL_ALFRED_BRANCH))
PKG_VERSION:=$(shell git ls-remote https://git@git.open-mesh.org/alfred.git $(PKG_BRANCH) | cut -c1-7)
PKG_ALFRED_FNAME:=alfred-devel-$(PKG_VERSION).tar.gz
PKG_ALFRED_DIR:=alfred-$(PKG_VERSION)

PKG_SOURCE_URL:=https://git.open-mesh.org/snapshot/
PKG_SOURCE:=$(PKG_ALFRED_FNAME)

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)/$(PKG_ALFRED_DIR)

include $(INCLUDE_DIR)/package.mk

init-y := alfred

define Package/alfred-devel
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Almighty Lightweight Fact Remote Exchange Daemon
  DEPENDS:= +libc +kmod-ipv6 +librt \
	  +ALFRED_DEVEL_NEEDS_lua:lua \
	  +ALFRED_DEVEL_NEEDS_libgps:libgps
endef

define Package/alfred-devel/conffiles
/etc/config/alfred
endef

define Package/alfred-devel/config
	source "$(SOURCE)/Config.in"
endef

MAKE_ALFRED_FLAGS=\
	CONFIG_ALFRED_VIS=$(if $(CONFIG_PACKAGE_ALFRED_DEVEL_VIS),y,n) \
	CONFIG_ALFRED_GPSD=$(if $(CONFIG_PACKAGE_ALFRED_DEVEL_GPSD),y,n) \
	CONFIG_ALFRED_CAPABILITIES=n

TARGET_CFLAGS  += -ffunction-sections -fdata-sections -flto
TARGET_LDFLAGS += -Wl,--gc-sections -fuse-linker-plugin

define Build/Compile
	CFLAGS="$(TARGET_CPPFLAGS) $(TARGET_CFLAGS)" \
	LDFLAGS="$(TARGET_LDFLAGS)" \
	$(MAKE) -C $(PKG_BUILD_DIR) $(MAKE_FLAGS) $(MAKE_ALFRED_FLAGS) all
endef

define Package/alfred-devel/install
	$(INSTALL_DIR) $(1)/usr/sbin
	cp -fpR  $(PKG_BUILD_DIR)/alfred $(1)/usr/sbin/
	[ "x$(CONFIG_PACKAGE_ALFRED_DEVEL_VIS)" == "xy" ] && cp -fpR  $(PKG_BUILD_DIR)/vis/batadv-vis $(1)/usr/sbin/ ; true
	[ "x$(CONFIG_PACKAGE_ALFRED_DEVEL_GPSD)" == "xy" ] && cp -fpR  $(PKG_BUILD_DIR)/gpsd/alfred-gpsd $(1)/usr/sbin/ ; true
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/alfred.init $(1)/etc/init.d/alfred
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DATA) ./files/alfred.config $(1)/etc/config/alfred
	$(INSTALL_DIR) $(1)/etc/alfred
	[ "x$(CONFIG_PACKAGE_ALFRED_DEVEL_BATHOSTS)" == "xy" ] && $(INSTALL_BIN) ./files/bat-hosts.lua $(1)/etc/alfred/bat-hosts.lua ; true
endef

$(eval $(call BuildPackage,alfred-devel))
