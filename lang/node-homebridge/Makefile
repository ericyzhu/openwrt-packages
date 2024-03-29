# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NPM_NAME:=homebridge
PKG_NAME:=node-$(PKG_NPM_NAME)
PKG_VERSION:=0.4.50
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NPM_NAME)-$(PKG_VERSION).tgz
PKG_SOURCE_URL:=http://registry.npmjs.org/$(PKG_NPM_NAME)/-/
PKG_HASH:=85541254b44c0b70e38ed5e49a9c6a68a20467b99fe0ff3703ca9fc422ce879c

PKG_BUILD_DEPENDS:=node/host
PKG_USE_MIPS16:=0

PKG_MAINTAINER:=Eric Zhu <eric@zhu.email>
PKG_LICENSE:=ISC Apache-2.0
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk

define Package/node-homebridge
  SUBMENU:=Node.js
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Node.js HomeKit Server
  URL:=https://www.npmjs.org/package/homebridge
  DEPENDS:=+node +node-npm +node-hap-nodejs
  USERID:=homebridge:homebridge
endef

define Package/node-homebridge/description
 Homebridge is a lightweight Node.js server you can run on your home network that emulates the iOS HomeKit API
endef

define Package/node-homebridge/conffiles
/etc/homebridge/config.conf
endef

NODEJS_CPU:=$(subst powerpc,ppc,$(subst aarch64,arm64,$(subst x86_64,x64,$(subst i386,ia32,$(ARCH)))))
TMPNPM:=$(shell mktemp -u XXXXXXXXXX)

TARGET_CFLAGS+=$(FPIC)

define Build/Prepare
	$(INSTALL_DIR) $(PKG_BUILD_DIR)
endef

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
	npm install -g $(DL_DIR)/$(PKG_SOURCE)
	rm -rf $(TMP_DIR)/npm-tmp-$(TMPNPM)
	rm -rf $(TMP_DIR)/npm-cache-$(TMPNPM)
	rm -rf $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/node_modules/hap-nodejs
endef

define Package/node-homebridge/install
	$(INSTALL_DIR) $(1)/usr/lib/node_modules/$(PKG_NPM_NAME)
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{*.json,*.md} \
		$(1)/usr/lib/node_modules/$(PKG_NPM_NAME)/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{node_modules,lib} \
		$(1)/usr/lib/node_modules/$(PKG_NPM_NAME)/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/$(PKG_NPM_NAME)/{LICENSE,CNAME,bin} \
		$(1)/usr/lib/node_modules/$(PKG_NPM_NAME)/

	$(INSTALL_DIR) $(1)/usr/bin
	$(LN) ../lib/node_modules/homebridge/bin/homebridge $(1)/usr/bin/homebridge

	$(INSTALL_DIR) $(1)/etc/homebridge/
	$(CP) ./files/etc/homebridge/config.json $(1)/etc/homebridge/

	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/homebridge.init $(1)/etc/init.d/homebridge
endef

$(eval $(call BuildPackage,node-homebridge))
