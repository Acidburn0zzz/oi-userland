#
# This file and its contents are supplied under the terms of the
# Common Development and Distribution License ("CDDL)". You may
# only use this file in accordance with the terms of the CDDL.
#
# A full copy of the text of the CDDL should have accompanied this
# source. A copy of the CDDL is also available via the Internet at
# http://www.illumos.org/license/CDDL.
#

#
# Copyright 2017 Alexander Pyhalov
#

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		pycairo
COMPONENT_VERSION=	1.10.0
COMPONENT_REVISION=	1
COMPONENT_PROJECT_URL=	http://www.cairographics.org
COMPONENT_SUMMARY=	Python bindings for Cairo
COMPONENT_SRC=		$(COMPONENT_NAME)-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.bz2
COMPONENT_ARCHIVE_HASH=	\
    sha256:9aa4078e7eb5be583aeabbe8d87172797717f95e8c4338f0d4a17b683a7253be
COMPONENT_ARCHIVE_URL=	http://cairographics.org/releases/$(COMPONENT_ARCHIVE)

PYTHON_VERSION =	3.4
PYTHON_VERSIONS =	3.4

VARIANT_PYTHON34 = $(BUILD_DIR)/python-34

VARIANTS = $(VARIANT_PYTHON34)

BUILD_32 = $(VARIANTS:%=%/$(MACH32)/.built)
BUILD_64 = $(VARIANTS:%=%/$(MACH64)/.built)

INSTALL_32 = $(VARIANTS:%=%/$(MACH32)/.installed)
INSTALL_64 = $(VARIANTS:%=%/$(MACH64)/.installed)

$(VARIANT_PYTHON34)/$(MACH64)/.configured: BITS=64
$(VARIANT_PYTHON34)/$(MACH64)/.configured: PYTHON=$(PYTHON.3.4.64)

$(VARIANT_PYTHON32)/$(MACH32)/.configured: BITS=32
$(VARIANT_PYTHON32)/$(MACH32)/.configured: PYTHON=$(PYTHON.3.4.32)


include $(WS_TOP)/make-rules/prep.mk
include $(WS_TOP)/make-rules/waf.mk
include $(WS_TOP)/make-rules/ips.mk

PATH=/usr/gnu/bin:$(USRBINDIR.$(BITS))

# Trick bundled waf into unpacking itself so we can patch it
COMPONENT_POST_UNPACK_ACTION += \
        (cd $(@D) ; $(PYTHON.3.4.32) waf --help >> /dev/null );

CONFIGURE_OPTIONS+=	--nopyo
CONFIGURE_OPTIONS+=	--nopyc
CONFIGURE_ENV+=		PYTHON=$(PYTHON)

# waf picks up the PYTHONDIR & PYTHONARCHDIR settings at configure time,
# but still insists on installing to site-packages instead of vendor-packages
COMPONENT_POST_INSTALL_ACTION += \
	if [ -d  $(PROTO_DIR)/usr/lib/$(MACH64)/python3.4  ]; then \
            $(MV) $(PROTO_DIR)/usr/lib/python3.4/site-packages $(PROTO_DIR)/usr/lib/python3.4/vendor-packages ; \
            $(MKDIR) -p  $(PROTO_DIR)/usr/lib/python3.4/vendor-packages/cairo/64      ; \
            $(MV) $(PROTO_DIR)/usr/lib/$(MACH64)/python3.4/site-packages/cairo/*.so $(PROTO_DIR)/usr/lib/python3.4/vendor-packages/cairo/64 ; \
            $(RM) -fr  $(PROTO_DIR)/usr/lib/$(MACH64)/python3.4 ; \
	fi ;

COMPONENT_POST_INSTALL_ACTION += \
        $(PYTHON.3.4.32) -m compileall $(PROTO_DIR)$(PYTHON_LIB)/cairo

# common targets
build:		$(BUILD_32_and_64)

install:	$(INSTALL_32_and_64)

test:		$(NO_TESTS)

REQUIRED_PACKAGES += library/desktop/cairo
REQUIRED_PACKAGES += runtime/python-34
REQUIRED_PACKAGES += system/library
