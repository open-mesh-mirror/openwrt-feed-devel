#
# Copyright (C) 2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=batctl-devel

ifdef CONFIG_batctl-devel_SOURCE_GIT

PKG_BRANCH:=$(call qstrip,$(CONFIG_batctl-devel_BRANCH))
PKG_SOURCE_URL:=$(call qstrip,$(CONFIG_batctl-devel_REPOSITORY))
PKG_REV:=$(shell git ls-remote $(PKG_SOURCE_URL) $(PKG_BRANCH)|cut -c1-40)
PKG_VERSION:=0.$(PKG_REV)
PKG_SOURCE_PROTO:=git

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_VERSION:=$(PKG_REV)

endif

ifdef CONFIG_batctl-devel_SOURCE_DIRECTORY

PKG_REV:=0
PKG_VERSION:=0.$(PKG_REV)

endif

PKG_LICENSE:=GPL-2.0
PKG_REV?=0
PKG_VERSION?=0.$(PKG_REV)

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
	source "$(SOURCE)/Config.devel.in"
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


ifdef CONFIG_batctl-devel_SOURCE_DIRECTORY

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
	$(CP) $(CONFIG_batctl-devel_DIRECTORY)/* $(PKG_BUILD_DIR)/
	$(call Build/Prepare/Default)
endef

endif

define Build/Compile
	$(MAKE_BATCTL_ENV) $(MAKE) -C "$(PKG_BUILD_DIR)" $(MAKE_BATCTL_ARGS)
endef

define Package/batctl-devel/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/batctl $(1)/usr/sbin/
endef

$(eval $(call BuildPackage,batctl-devel))
