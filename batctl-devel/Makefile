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

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/$(PKG_NAME)-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk

define Package/batctl-devel
  URL:=https://www.open-mesh.org/
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+libnl-tiny +libc +librt
  PROVIDES:=batctl-devel
  MAINTAINER:=Simon Wunderlich <sw@simonwunderlich.de>
  TITLE:=B.A.T.M.A.N. Advanced user space configuration tool (Default)
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

MAKE_VARS += \
        LIBNL_NAME="libnl-tiny" \
        LIBNL_GENL_NAME="libnl-tiny"

MAKE_FLAGS += \
        REVISION="openwrt-$(PKG_VERSION)"

config-n := \
	aggregation \
	ap_isolation \
	backbonetable \
	bisect_iv \
	bonding \
	bridge_loop_avoidance \
	claimtable \
	dat_cache \
	distributed_arp_table \
	event \
	fragmentation \
	gateways \
	gw_mode \
	hop_penalty \
	interface \
	isolation_mark \
	loglevel \
	mcast_flags \
	multicast_fanout \
	multicast_forceflood \
	multicast_mode \
	nc_nodes \
	neighbors \
	network_coding \
	orig_interval \
	originators \
	ping \
	routing_algo \
	statistics \
	tcpdump \
	throughputmeter \
	traceroute \
	transglobal \
	translate \
	translocal \

config-settings := \
	aggregation \
	ap_isolation \
	bonding \
	bridge_loop_avoidance \
	distributed_arp_table \
	fragmentation \
	gw_mode \
	hop_penalty \
	interface \
	isolation_mark \
	loglevel \
	multicast_fanout \
	multicast_forceflood \
	multicast_mode \
	network_coding \
	orig_interval \
	routing_algo \

config-tables := \
	backbonetable \
	claimtable \
	dat_cache \
	gateways \
	loglevel \
	nc_nodes \
	neighbors \
	originators \
	statistics \
	transglobal \
	translocal \

config-tools := \
	event \
	ping \
	tcpdump \
	throughputmeter \
	traceroute \
	translate \

config-extratools := \
	bisect_iv \

ifdef CONFIG_batctl-devel_tiny

config-y := \
	$(config-settings) \

endif

ifdef CONFIG_batctl-devel_default

config-y := \
	$(config-settings) \
	$(config-tables) \
	$(config-tools) \

endif

ifdef CONFIG_batctl-devel_full

config-y := \
	$(config-settings) \
	$(config-tables) \
	$(config-tools) \
	$(config-extratools) \

endif

define ConfigVars
$(subst $(space),,$(foreach opt,$(config-$(1)),CONFIG_$(opt)=$(1)
))
endef

define batctl_config
$(call ConfigVars,n)$(call ConfigVars,y)
endef
$(eval $(call shexport,batctl_config))

ifdef CONFIG_batctl-devel_SOURCE_DIRECTORY

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
	$(CP) $(CONFIG_batctl-devel_DIRECTORY)/* $(PKG_BUILD_DIR)/
	$(call Build/Prepare/Default)
endef

endif

MAKE_FLAGS += $$$$$(call shvar,batctl_config)

define Package/batctl-devel/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/batctl $(1)/usr/sbin/
endef

$(eval $(call BuildPackage,batctl-devel))
