# Copyright (C) 2009-2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=nfs-kernel-server
PKG_VERSION:=1.3.3
PKG_RELEASE:=3
PKG_MD5SUM:=9b87d890669eaaec8e97a2b0a35b2665

PKG_SOURCE_URL:=@SF/nfs
PKG_SOURCE:=nfs-utils-$(PKG_VERSION).tar.bz2

HOST_BUILD_DIR:=$(BUILD_DIR_HOST)/nfs-utils-$(PKG_VERSION)
PKG_BUILD_DIR:=$(BUILD_DIR)/nfs-utils-$(PKG_VERSION)
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1
PKG_BUILD_DEPENDS:=$(PKG_NAME)/host

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk

define Package/nfs-kernel-server/Default
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Filesystem
  DEPENDS:=+libwrap +libblkid +libuuid
  URL:=http://nfs.sourceforge.net/
  MAINTAINER:=Peter Wagner <tripolar@gmx.at>
endef

define Package/nfs-kernel-server
  $(call Package/nfs-kernel-server/Default)
  TITLE:=Kernel NFS server support
  DEPENDS+= +portmap
endef

define Package/nfs-kernel-server/description
  Kernel NFS server support
endef

define Package/nfs-kernel-server-utils
  $(call Package/nfs-kernel-server/Default)
  TITLE:=NFS server utils
  DEPENDS:=nfs-kernel-server
endef

define Package/nfs-kernel-server-utils/description
  NFS server utils
endef

define Package/nfs-kernel-server/conffiles
/opt/etc/exports
endef

define Package/nfs-utils
  $(call Package/nfs-kernel-server/Default)
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=updated mount utility (includes nfs4)
endef

define Package/nfs-utils/description
  Updated mount.nfs command - allows mounting nfs4 volumes
endef

TARGET_CFLAGS += -I$(STAGING_DIR)/opt/include/ -Drpc_uint=uint
TARGET_LDFLAGS += -Wl,-rpath-link=$(STAGING_DIR)/opt/lib

CONFIGURE_ARGS += \
	--disable-gss \
	--disable-nfsv4 \
	--disable-nfsv41 \
	--disable-ipv6 \
	--enable-static \
	--enable-shared \
	--disable-caps \
	--disable-tirpc \
	--disable-nfsdcld \
	--with-statedir=/opt/var/lib/nfs \
	--with-statdpath=/opt/var/lib/nfs

CONFIGURE_VARS += \
	libblkid_cv_is_recent=yes \
	CONFIG_SQLITE3_TRUE="\#" \
	CONFIG_NFSDCLD_TRUE="\#"

MAKE_FLAGS += \
	OPT="$(TARGET_CFLAGS)" \
	INSTALLSUID="install -m 4755" \
	DESTDIR="$(PKG_INSTALL_DIR)" \
	RPCGEN_PATH=$(STAGING_DIR)/host/bin/rpcgen \
	RPCGEN=$(STAGING_DIR)/host/bin/rpcgen

HOST_CFLAGS += -Dlinux

HOST_CONFIGURE_ARGS += \
	--disable-gss \
	--disable-nfsv4 \
	--disable-nfsv41 \
	--disable-ipv6 \
	--disable-tirpc \
	--without-tcp-wrappers

HOST_CONFIGURE_VARS += \
	ac_cv_lib_event_event_dispatch=yes \
	ac_cv_lib_nfsidmap_nfs4_init_name_mapping=yes \
	ac_cv_lib_blkid_blkid_get_library_version=yes \
	ac_cv_header_event_h=yes \
	ac_cv_header_nfsidmap_h=yes \
	ac_cv_header_blkid_blkid_h=yes \
	GSSGLUE_CFLAGS=" " \
	GSSGLUE_LIBS=" " \
	RPCSECGSS_CFLAGS=" " \
	RPCSECGSS_LIBS=" " \
	CONFIG_SQLITE3_TRUE="\#" \
	CONFIG_NFSDCLD_TRUE="\#"

define Host/Compile
	$(MAKE) -C $(HOST_BUILD_DIR)/tools/rpcgen all
endef

define Host/Install
	$(INSTALL_DIR) $(STAGING_DIR)/host/bin
	$(INSTALL_BIN) $(HOST_BUILD_DIR)/tools/rpcgen/rpcgen $(STAGING_DIR)/host/bin/rpcgen
endef

define Package/nfs-kernel-server/install
	$(INSTALL_DIR) $(1)/opt/etc/init.d $(1)/opt/sbin
	$(INSTALL_DATA) ./files/nfsd.exports $(1)/opt/etc/exports
	$(INSTALL_BIN) ./files/S57nfs-kernel-server $(1)/opt/etc/init.d/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utils/statd/sm-notify $(1)/opt/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utils/statd/statd $(1)/opt/sbin/rpc.statd
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utils/nfsd/nfsd $(1)/opt/sbin/rpc.nfsd
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utils/mountd/mountd $(1)/opt/sbin/rpc.mountd
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utils/exportfs/exportfs $(1)/opt/sbin/
endef

define Package/nfs-kernel-server-utils/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utils/showmount/showmount $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utils/nfsstat/nfsstat $(1)/opt/sbin
endef

define Package/nfs-utils/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/sbin/mount.nfs $(1)/opt/sbin/
	(cd $(1)/opt/sbin; ln -sf mount.nfs mount.nfs4; ln -sf mount.nfs umount.nfs; ln -sf mount.nfs umount.nfs4)
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,nfs-kernel-server))
$(eval $(call BuildPackage,nfs-kernel-server-utils))
$(eval $(call BuildPackage,nfs-utils))
