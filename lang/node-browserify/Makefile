# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NPM_NAME:=browserify
PKG_NAME:=node-$(PKG_NPM_NAME)
PKG_VERSION:=16.3.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NPM_NAME)-$(PKG_VERSION).tgz
PKG_SOURCE_URL:=https://registry.npmjs.org/$(PKG_NPM_NAME)/-/
PKG_HASH:=ee33f5028cb73b38246732c21f964bf23379e7a5db2b1dcfbe2c4b62e9cb3fbf

PKG_MAINTAINER:=Hirokazu MORIKAWA <morikw2@gmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE

HOST_BUILD_DEPENDS:=node/host
HOST_BUILD_PARALLEL:=1

PKG_BUILD_DEPENDS:=node/host
PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk

define Package/node-browserify
  SUBMENU:=Node.js
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=browser-side require() the node way
  URL:=https://www.npmjs.org/package/browserify
  DEPENDS:=+node
endef

define Package/node-browserify/description
 browserify will recursively analyze all the require() calls in your app in order to build a bundle you can serve up to the browser in a single <script> tag
endef

TAR_OPTIONS+= --strip-components 1
TAR_CMD=$(HOST_TAR) -C $(1) $(TAR_OPTIONS)

NODEJS_CPU:=$(subst powerpc,ppc,$(subst aarch64,arm64,$(subst x86_64,x64,$(subst i386,ia32,$(ARCH)))))
TMPNPM:=$(shell mktemp -u XXXXXXXXXX)
HOSTTMPNPM:=$(shell mktemp -u XXXXXXXXXX)

TARGET_CFLAGS+=$(FPIC)
TARGET_CPPFLAGS+=$(FPIC)

define Build/Compile
	$(MAKE_VARS) \
	$(MAKE_FLAGS) \
	npm_config_arch=$(NODEJS_CPU) \
	npm_config_target_arch=$(NODEJS_CPU) \
	npm_config_build_from_source=true \
	npm_config_nodedir=$(STAGING_DIR)/usr/ \
	npm_config_prefix=$(PKG_INSTALL_DIR)/usr/ \
	npm_config_cache=$(TMP_DIR)/npm-cache-$(TMPNPM) \
	npm_config_tmp=$(TMP_DIR)/npm-tmp-$(TMPNPM) \
	npm install -g $(PKG_BUILD_DIR)
	rm -rf $(TMP_DIR)/npm-tmp-$(TMPNPM)
	rm -rf $(TMP_DIR)/npm-cache-$(TMPNPM)
endef

define Package/node-browserify/install
	$(INSTALL_DIR) $(1)/usr/lib/node/$(PKG_NPM_NAME)
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{package.json,LICENSE} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{*.yml,*.markdown,*.md,*.js} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{node_modules,lib,bin} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{assets,example,test} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(INSTALL_DIR) $(1)/usr/bin
	$(LN) ../lib/node/browserify/bin/cmd.js $(1)/usr/bin/browserify
endef

define Host/Compile
	$(HOST_MAKE_VARS) \
	npm_config_nodedir=$(STAGING_DIR)/usr/ \
	npm_config_prefix=$(HOST_INSTALL_DIR)/usr/ \
	npm_config_cache=$(TMP_DIR)/npm-cache-$(HOSTTMPNPM) \
	npm_config_tmp=$(TMP_DIR)/npm-tmp-$(HOSTTMPNPM) \
	npm install -g $(HOST_BUILD_DIR)
	rm -rf $(TMP_DIR)/npm-tmp-$(HOSTTMPNPM)
	rm -rf $(TMP_DIR)/npm-cache-$(HOSTTMPNPM)
endef

define Host/Install
	$(INSTALL_DIR) $(1)/lib/node_modules/$(PKG_NPM_NAME)
	$(CP) $(HOST_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{package.json,LICENSE} \
		$(1)/lib/node_modules/$(PKG_NPM_NAME)/
	$(CP) $(HOST_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{*.yml,*.markdown,*.md,*.js} \
		$(1)/lib/node_modules/$(PKG_NPM_NAME)/
	$(CP) $(HOST_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{node_modules,lib,bin} \
		$(1)/lib/node_modules/$(PKG_NPM_NAME)/
	$(CP) $(HOST_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{assets,example,test} \
		$(1)/lib/node_modules/$(PKG_NPM_NAME)/
	$(INSTALL_DIR) $(1)/bin
	$(LN) ../lib/node_modules/browserify/bin/cmd.js $(1)/bin/browserify
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,node-browserify))
