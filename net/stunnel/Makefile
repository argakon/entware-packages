#
# Copyright (C) 2006-2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=stunnel
PKG_VERSION:=5.32
PKG_RELEASE:=1

PKG_LICENSE:=GPL-2.0+
PKG_MAINTAINER:=Michael Haas <haas@computerlinguist.org>
PKG_LICENSE_FILES:=COPYING COPYRIGHT.GPL

PKG_SOURCE_URL:=https://www.stunnel.org/downloads/archive/5.x
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_MD5SUM:=60462f97b62e745288541089e8c0877c

PKG_FIXUP:=autoreconf
PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk

define Package/stunnel
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+libopenssl +libpthread
  TITLE:=SSL TCP Wrapper
  URL:=http://www.stunnel.org/
endef

define Package/stunnel/description
	Stunnel is a program that allows you to encrypt arbitrary TCP
	connections inside SSL (Secure Sockets Layer) available on both Unix
	and Windows. Stunnel can allow you to secure non-SSL aware daemons and
	protocols (like POP, IMAP, LDAP, etc) by having Stunnel provide the
	encryption, requiring no changes to the daemon's code.
endef

define Package/stunnel/conffiles
/opt/etc/stunnel/stunnel.conf
endef


CONFIGURE_ARGS+= \
	--with-random=/dev/urandom \
	--with-threads=pthread \
	--with-ssl=$(STAGING_DIR)/opt \
	--disable-libwrap \
	--disable-systemd

ifeq ($(CONFIG_IPV6),n)
CONFIGURE_ARGS+= \
	--disable-ipv6
endif

define Build/Compile
	mkdir -p $(PKG_INSTALL_DIR)/opt/etc/stunnel
	echo '#dummy' > $(PKG_INSTALL_DIR)/opt/etc/stunnel/stunnel.pem
	$(call Build/Compile/Default)
endef

define Package/stunnel/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/stunnel $(1)/opt/bin/
	$(INSTALL_DIR) $(1)/opt/lib/stunnel
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/lib/stunnel/libstunnel.so $(1)/opt/lib/stunnel/
	$(INSTALL_DIR) $(1)/opt/etc/stunnel
	$(INSTALL_CONF) ./files/stunnel.conf $(1)/opt/etc/stunnel/stunnel.conf
endef

$(eval $(call BuildPackage,stunnel))
