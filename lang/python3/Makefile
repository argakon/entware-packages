#
# Copyright (C) 2006-2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

# The file included below defines PYTHON_VERSION
include ./files/python3-package.mk

PYTHON_VERSION:=$(PYTHON3_VERSION)
PYTHON_VERSION_MICRO:=$(PYTHON3_VERSION_MICRO)

PKG_NAME:=python3
PKG_RELEASE:=4
PKG_VERSION:=$(PYTHON_VERSION).$(PYTHON_VERSION_MICRO)

PKG_SOURCE:=Python-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://www.python.org/ftp/python/$(PKG_VERSION)
PKG_MD5SUM:=e9ea6f2623fffcdd871b7b19113fde80

PKG_LICENSE:=PSF
PKG_LICENSE_FILES:=LICENSE Modules/_ctypes/libffi_msvc/LICENSE Modules/_ctypes/darwin/LICENSE Modules/_ctypes/libffi/LICENSE Modules/_ctypes/libffi_osx/LICENSE Tools/pybench/LICENSE

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1
HOST_BUILD_PARALLEL:=1

PKG_BUILD_DIR:=$(BUILD_DIR)/Python-$(PKG_VERSION)
HOST_BUILD_DIR:=$(BUILD_DIR_HOST)/Python-$(PKG_VERSION)

PKG_BUILD_DEPENDS:=libbz2/host expat/host python3/host
HOST_BUILD_DEPENDS:=bzip2/host expat/host libffi/host

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk

define Package/python3/Default
  SUBMENU:=Python
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Python $(PYTHON_VERSION) programming language
  URL:=https://www.python.org/
  MAINTAINER:=Alexandru Ardelean <ardeleanalex@gmail.com>
endef

define Package/python3/Default/description
 Python is a dynamic object-oriented programming language that can be used
 for many kinds of software development. It offers strong support for
 integration with other languages and tools, comes with extensive standard
 libraries, and can be learned in a few days. Many Python programmers
 report substantial productivity gains and feel the language encourages
 the development of higher quality, more maintainable code.
endef

define Package/python3-base
$(call Package/python3/Default)
  TITLE:=Python $(PYTHON_VERSION) interpreter
  DEPENDS:=+libpthread +zlib
endef

define Package/python3-base/description
  This package contains only the interpreter and the bare minimum
  for the interpreter to start.
endef

define Package/python3-light
$(call Package/python3/Default)
  TITLE:=Python $(PYTHON_VERSION) light installation
  DEPENDS:=+python3-base +libffi +libbz2
endef

define Package/python3-light/description
  This package is essentially the python3-base package plus
  a few of the rarely used (and big) libraries stripped out
  into separate packages.
endef

PYTHON3_LIB_FILES_DEL:=
PYTHON3_PACKAGES:=
PYTHON3_SO_SUFFIX:=cpython-$(PYTHON3_VERSION_MAJOR)$(PYTHON3_VERSION_MINOR).so
define Py3BasePackage
  PYTHON3_PACKAGES+=$(1)
  PYTHON3_LIB_FILES_DEL+=$(2)
  define Py3Package/$(1)/filespec
    $(subst $(space),$(newline),$(foreach lib_file,$(2),+|$(lib_file)))
  endef
endef

include ./files/python3-package-*.mk

define Package/python3
$(call Package/python3/Default)
  DEPENDS:=+python3-light $(foreach package,$(filter-out python3-dev python3-lib2to3,$(PYTHON3_PACKAGES)),+$(package))
endef

define Package/python3/description
  This package contains the (almost) full Python install.
  It's python3-light + all other packages.
endef

MAKE_FLAGS+=\
	CROSS_COMPILE=yes \
	LD="$(TARGET_CC)" \
	FREEZE_IMPORTLIB=_freeze_importlib \
	PGEN=pgen3

ifeq ($(ARCH),i386)
MAKE_FLAGS+=PYTHON_DECIMAL_WITH_MACHINE=ansi32
endif

EXTRA_CFLAGS+= \
	-DNDEBUG -fno-inline
EXTRA_LDFLAGS+= \
	-L$(PKG_BUILD_DIR)

ENABLE_IPV6:=
ifeq ($(CONFIG_IPV6),y)
	ENABLE_IPV6 += --enable-ipv6
endif

CONFIGURE_ARGS+= \
	--sysconfdir=/opt/etc \
	--enable-shared \
	--without-cxx-main \
	--with-threads \
	--with-system-ffi="$(STAGING_DIR)/opt" \
	--with-system-expat="$(STAGING_DIR)/opt" \
	--without-pymalloc \
	--without-ensurepip \
	$(ENABLE_IPV6) \
	CONFIG_SITE="$(PKG_BUILD_DIR)/config.site" \
	OPT="$(TARGET_CFLAGS)"

define Build/Prepare
	$(call Build/Prepare/Default)
	$(CP) ./files/config.site $(PKG_BUILD_DIR)/config.site
endef

define Build/Compile
	$(call Build/Compile/Default)
	$(SED) "s|#!/usr/bin/env python3|#!/opt/bin/python3|g" $(PKG_BUILD_DIR)/Lib/pydoc.py
	$(SED) "s|#!/usr/bin/env python3|#!/opt/bin/python3|g" $(PKG_BUILD_DIR)/Lib/trace.py
	$(SED) "s|#!/usr/bin/env python3|#!/opt/bin/python3|g" $(PKG_BUILD_DIR)/Lib/tarfile.py
	$(SED) "s|#!/usr/bin/env python3|#!/opt/bin/python3|g" $(PKG_BUILD_DIR)/Lib/platform.py
	$(SED) "s|#!/usr/bin/env python3|#!/opt/bin/python3|g" $(PKG_BUILD_DIR)/Lib/lib2to3/tests/pytree_idempotency.py
	$(SED) "s|#!/usr/bin/env python|#!/opt/bin/python3|g" $(PKG_BUILD_DIR)/Lib/encodings/rot_13.py
	$(SED) "s|#!/usr/bin/env python|#!/opt/bin/python3|g" $(PKG_BUILD_DIR)/Lib/lib2to3/tests/data/false_encoding.py
	$(SED) "s|#!/usr/bin/env python|#!/opt/bin/python3|g" $(PKG_BUILD_DIR)/Lib/lib2to3/tests/data/different_encoding.py
endef


define Build/InstallDev
	$(INSTALL_DIR) $(STAGING_DIR)/mk/
	$(INSTALL_DIR) $(1)/opt/include/ $(1)/usr/lib/
	$(INSTALL_DIR) $(1)/opt/lib/python$(PYTHON_VERSION)/
	$(INSTALL_DATA) ./files/python3-package.mk $(STAGING_DIR)/mk/
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/include/python$(PYTHON_VERSION) \
		$(1)/opt/include/
	$(CP) \
		$(STAGING_DIR)/host/lib/python$(PYTHON_VERSION) \
		$(PKG_INSTALL_DIR)/opt/lib/libpython$(PYTHON_VERSION).so* \
		$(1)/opt/lib/
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/python$(PYTHON_VERSION)/config-$(PYTHON_VERSION) \
		$(1)/opt/lib/python$(PYTHON_VERSION)/
endef

PYTHON3_BASE_LIB_FILES:= \
	/opt/lib/python$(PYTHON_VERSION)/encodings \
	/opt/lib/python$(PYTHON_VERSION)/_collections_abc.py \
	/opt/lib/python$(PYTHON_VERSION)/_sitebuiltins.py \
	/opt/lib/python$(PYTHON_VERSION)/_sysconfigdata.py \
	/opt/lib/python$(PYTHON_VERSION)/_weakrefset.py \
	/opt/lib/python$(PYTHON_VERSION)/abc.py \
	/opt/lib/python$(PYTHON_VERSION)/codecs.py \
	/opt/lib/python$(PYTHON_VERSION)/genericpath.py \
	/opt/lib/python$(PYTHON_VERSION)/io.py \
	/opt/lib/python$(PYTHON_VERSION)/os.py \
	/opt/lib/python$(PYTHON_VERSION)/posixpath.py \
	/opt/lib/python$(PYTHON_VERSION)/site.py \
	/opt/lib/python$(PYTHON_VERSION)/sysconfig.py \
	/opt/lib/python$(PYTHON_VERSION)/stat.py

PYTHON3_LIB_FILES_DEL+=$(PYTHON3_BASE_LIB_FILES)

define Py3Package/python3-base/filespec
+|/opt/bin/python$(PYTHON_VERSION)
$(subst $(space),$(newline),$(foreach lib_file,$(PYTHON3_BASE_LIB_FILES),+|$(lib_file)))
endef

define Py3Package/python3-light/filespec
+|/opt/lib/python$(PYTHON_VERSION)
-|/opt/lib/python$(PYTHON_VERSION)/distutils/cygwinccompiler.py
-|/opt/lib/python$(PYTHON_VERSION)/distutils/command/wininst*
-|/opt/lib/python$(PYTHON_VERSION)/ensurepip
-|/opt/lib/python$(PYTHON_VERSION)/idlelib
-|/opt/lib/python$(PYTHON_VERSION)/tkinter
-|/opt/lib/python$(PYTHON_VERSION)/turtledemo
-|/opt/lib/python$(PYTHON_VERSION)/lib-dynload/_test*.so
-|/opt/lib/python$(PYTHON_VERSION)/lib-dynload/readline*.so
-|/opt/lib/python$(PYTHON_VERSION)/pdb.doc
-|/opt/lib/python$(PYTHON_VERSION)/test
-|/opt/lib/python$(PYTHON_VERSION)/webbrowser.py
-|/opt/lib/python$(PYTHON_VERSION)/*/test
-|/opt/lib/python$(PYTHON_VERSION)/*/tests
-|/opt/lib/python$(PYTHON_VERSION)/_osx_support.py
$(subst $(space),$(newline),$(foreach lib_file,$(PYTHON3_LIB_FILES_DEL),-|$(lib_file)))
endef

define Py3Package/python3-base/install
	# Adding the lib-dynload folder (even just empty) suppresses 2 warnings when starting Python
	$(INSTALL_DIR) $(1)/opt/lib/python$(PYTHON_VERSION)/lib-dynload/
	$(INSTALL_DIR) $(1)/opt/bin
	$(LN) python$(PYTHON_VERSION) $(1)/opt/bin/python3
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libpython$(PYTHON_VERSION).so* $(1)/opt/lib/
endef

define Py3Package/python3/filespec
-|$(PYTHON3_PKG_DIR)
endef

HOST_CFLAGS+= \
	-I/usr/include/ncursesw \
	-I/usr/include/ncurses

HOST_CONFIGURE_ARGS+= \
	--without-cxx-main \
	--without-pymalloc \
	--with-threads \
	--prefix=$(STAGING_DIR)/host \
	--exec-prefix=$(STAGING_DIR)/host \
	--sysconfdir=$(STAGING_DIR_HOST)/host/etc \
	--localstatedir=$(STAGING_DIR)/host/var \
	--sbindir=$(STAGING_DIR)/host/bin \
	--with-system-expat=$(STAGING_DIR)/host \
	--with-system-ffi=$(STAGING_DIR)/host \
	--with-ensurepip=upgrade \
	CONFIG_SITE= \
	CFLAGS="$(HOST_CFLAGS)"

define Host/Compile
	+$(MAKE) $(HOST_JOBS) -C $(HOST_BUILD_DIR) python Parser/pgen
	+$(MAKE) $(HOST_JOBS) -C $(HOST_BUILD_DIR) sharedmods
endef

define Host/Install
	$(INSTALL_DIR) $(STAGING_DIR)/host/bin/
	$(MAKE) -C $(HOST_BUILD_DIR) install
	$(INSTALL_BIN) $(HOST_BUILD_DIR)/Parser/pgen $(STAGING_DIR)/host/bin/pgen3
	$(INSTALL_BIN) $(HOST_BUILD_DIR)/Programs/_freeze_importlib $(STAGING_DIR)/host/bin/_freeze_importlib
endef

$(eval $(call HostBuild))

$(foreach package, $(PYTHON3_PACKAGES),  \
	$(eval $(call Py3Package,$(package))) \
	$(eval $(call BuildPackage,$(package))) \
)

$(eval $(call Py3Package,python3-base))
$(eval $(call Py3Package,python3-light))
$(eval $(call Py3Package,python3))

$(eval $(call BuildPackage,python3-base))
$(eval $(call BuildPackage,python3-light))
$(eval $(call BuildPackage,python3))
