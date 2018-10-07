#
# Copyright (C) 2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=batctl-devel

PKG_BRANCH:=$(call qstrip,$(CONFIG_BATCTL_DEVEL_BATCTL_BRANCH))
PKG_VERSION:=$(shell git ls-remote https://git@git.open-mesh.org/batctl.git $(PKG_BRANCH) | cut -c1-7)
PKG_BATCTL_FNAME:=batctl-devel-$(PKG_VERSION).tar.gz
PKG_BATCTL_DIR:=batctl-$(PKG_VERSION)

PKG_SOURCE_URL:=https://git.open-mesh.org/snapshot/
PKG_SOURCE:=$(PKG_BATCTL_FNAME)

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)/$(PKG_BATCTL_DIR)

include $(INCLUDE_DIR)/package.mk

define Package/batctl-devel
  URL:=https://www.open-mesh.org/
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+kmod-batman-adv-devel +libnl-tiny +libc +librt
  TITLE:=B.A.T.M.A.N. Advanced user space configuration tool batctl
  MAINTAINER:=Simon Wunderlich <sw@simonwunderlich.de>
endef

define Package/batctl-devel/description
batctl is a more intuitive managment utility for B.A.T.M.A.N.-Advanced.
It is an easier method for configuring batman-adv and provides some
additional tools for debugging as well. This package builds the latest
development version ($(PKG_VERSION)).
endef

define Package/batctl-devel/config
	source "$(SOURCE)/Config.in"
endef

# The linker can identify unused sections of a binary when each symbol is stored
# in a separate section. This mostly removes unused linker sections and reduces
# the size by ~3% on mipsel.

TARGET_CFLAGS  += -ffunction-sections -fdata-sections
TARGET_LDFLAGS += -Wl,--gc-sections

# Link-time optimization allows to move parts of the optimization from the single
# source file to the global source view. This is done by emitting the GIMPLE
# representation in each object file and analyzing it again during the link step.

TARGET_CFLAGS  += -flto
TARGET_LDFLAGS += -fuse-linker-plugin

MAKE_BATCTL_ENV += \
        CPPFLAGS="$(TARGET_CPPFLAGS)" \
        CFLAGS="$(TARGET_CFLAGS)" \
        LDFLAGS="$(TARGET_LDFLAGS)" \
        LIBNL_NAME="libnl-tiny" \
        LIBNL_GENL_NAME="libnl-tiny"

MAKE_BATCTL_ARGS += \
        CC="$(TARGET_CC)" \
        DESTDIR="$(PKG_INSTALL_DIR)" \
        batctl install \
        REVISION="openwrt-$(PKG_VERSION)"


define Build/Compile
	$(MAKE_BATCTL_ENV) $(MAKE) -C "$(PKG_BUILD_DIR)" $(MAKE_BATCTL_ARGS)
endef

define Package/batctl-devel/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/batctl $(1)/usr/sbin/
endef

$(eval $(call BuildPackage,batctl-devel))
