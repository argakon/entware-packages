#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=mutt
PKG_VERSION:=1.5.24
PKG_RELEASE:=1

PKG_SOURCE_URL:=ftp://ftp.mutt.org/pub/mutt/
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_MD5SUM:=7f25d27f3c7c82285ac07aac35f5f0f2

PKG_LICENSE:=GPL-2.0+
PKG_LICENSE_FILES:=GPL
PKG_MAINTAINER:=Phil Eichinger <phil@zankapfel.net>


PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk

define Package/mutt
  SECTION:=mail
  CATEGORY:=Mail
  DEPENDS:=+libopenssl +libncursesw +libidn +zlib
  TITLE:=Console mail client
  URL:=http://www.mutt.org/
endef

define Package/mutt/description
	Mutt is a small but very powerful text-based mail client for Unix
	operating systems.
endef

CONFIGURE_ARGS += \
	--includedir=$(PKG_BUILD_DIR)/. \
	--oldincludedir=$(PKG_BUILD_DIR)/. \
	--enable-pop \
	--enable-imap \
	--with-ssl \
	--with-idn

define Package/mutt/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/mutt $(1)/opt/bin/
endef

$(eval $(call BuildPackage,mutt))
