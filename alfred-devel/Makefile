#
# Copyright (C) 2013 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=alfred-devel

ifdef CONFIG_alfred-devel_SOURCE_GIT

PKG_BRANCH:=$(call qstrip,$(CONFIG_alfred-devel_BRANCH))
PKG_SOURCE_URL:=$(call qstrip,$(CONFIG_alfred-devel_REPOSITORY))
PKG_REV:=$(shell git ls-remote $(PKG_SOURCE_URL) $(PKG_BRANCH)|cut -c1-40)
PKG_VERSION:=0.$(PKG_REV)
PKG_SOURCE_PROTO:=git

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_VERSION:=$(PKG_REV)

endif

ifdef CONFIG_alfred-devel_SOURCE_DIRECTORY

PKG_REV:=0
PKG_VERSION:=0.$(PKG_REV)

endif

PKG_LICENSE:=GPL-2.0
PKG_REV?=0
PKG_VERSION?=0.$(PKG_REV)

include $(INCLUDE_DIR)/package.mk

init-y := alfred

define Package/alfred-devel
  URL:=https://www.open-mesh.org/
  SECTION:=net
  CATEGORY:=Network
  TITLE:=A.L.F.R.E.D. - Almighty Lightweight Fact Remote Exchange Daemon devel
  DEPENDS:= +libc @IPV6 +libnl-tiny +librt \
	  +ALFRED_DEVEL_NEEDS_lua:lua \
	  +ALFRED_DEVEL_NEEDS_libgps:libgps
endef

define Package/alfred-devel/description
alfred is a user space daemon for distributing arbitrary local information over
the mesh/network in a decentralized fashion. This data can be anything which
appears to be useful - originally designed to replace the batman-adv
visualization (vis), you may distribute hostnames, phone books, administration
information, DNS information, the local weather forecast ...

alfred runs as daemon in the background of the system. A user may insert
information by using the alfred binary on the command line, or use special
programs to communicate with alfred (done via unix sockets). alfred then takes
care of distributing the local information to other alfred servers on other
nodes. This is done via IPv6 link-local multicast, and does not require any
configuration. A user can request data from alfred, and will receive the
information available from all alfred servers in the network.
endef

define Package/alfred-devel/conffiles
/etc/config/alfred
endef

define Package/alfred-devel/config
	source "$(SOURCE)/Config.devel.in"
	source "$(SOURCE)/Config.in"
endef

MAKE_FLAGS += \
	CONFIG_ALFRED_VIS=$(if $(CONFIG_PACKAGE_ALFRED_DEVEL_VIS),y,n) \
	CONFIG_ALFRED_GPSD=$(if $(CONFIG_PACKAGE_ALFRED_DEVEL_GPSD),y,n) \
	CONFIG_ALFRED_CAPABILITIES=n \
        LIBNL_NAME="libnl-tiny" \
        LIBNL_GENL_NAME="libnl-tiny" \
        REVISION="openwrt-$(PKG_VERSION)"

TARGET_CFLAGS  += -ffunction-sections -fdata-sections -flto
TARGET_LDFLAGS += -Wl,--gc-sections -fuse-linker-plugin

ifdef CONFIG_alfred-devel_SOURCE_DIRECTORY

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
	$(CP) $(CONFIG_alfred-devel_DIRECTORY)/* $(PKG_BUILD_DIR)/
	$(call Build/Prepare/Default)
endef

endif

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
