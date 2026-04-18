TARGET := iphone:clang:latest:14.0
ARCHS = arm64 arm64e

include $(THEOS)/makefiles/common.mk

TWEAK_NAME = WeChatTapAnimation

WeChatTapAnimation_FILES = Tweak.xm
WeChatTapAnimation_CFLAGS = -fobjc-arc
WeChatTapAnimation_FRAMEWORKS = UIKit QuartzCore

include $(THEOS_MAKE_PATH)/tweak.mk

after-install::
	install.exec "killall -9 WeChat"
