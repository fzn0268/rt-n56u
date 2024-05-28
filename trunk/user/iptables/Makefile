SRC_NAME=iptables-1.8.10
SRC_URL=https://www.netfilter.org/projects/iptables/files/$(SRC_NAME).tar.xz

CFLAGS  += -ffunction-sections -fdata-sections
LDFLAGS += -Wl,--gc-sections

THISDIR = $(shell pwd)

all: download_test extract_test config_test
	$(MAKE) -C $(SRC_NAME)

download_test:
	( if [ ! -f $(SRC_NAME).tar.xz ]; then \
		wget -t5 --timeout=20 --no-check-certificate -O $(SRC_NAME).tar.xz $(SRC_URL); \
	fi )

extract_test:
	( if [ ! -d $(SRC_NAME) ]; then \
		tar -xJf $(SRC_NAME).tar.xz; \
	fi )

config_test:
	( if [ -f ./config_done ]; then \
		echo "the same configuration"; \
	else \
		make configure && touch config_done; \
	fi )

configure:
	( cd $(SRC_NAME) ; \
	./autogen.sh ; \
	./configure \
		--prefix= \
		--enable-static \
		--disable-shared \
		--disable-nftables \
		--with-xtlibdir=/usr/lib/xtables \
		--with-xt-lock-name=/var/run/xtables.lock \
		--with-kernel=$(ROOTDIR)/$(LINUXDIR) \
		$(if $(CONFIG_IPV6),--enable-ipv6,--disable-ipv6) \
		--host=$(HOST_TARGET) \
		--build=$(HOST_BUILD) ; \
	)

clean:
	rm -rf $(SRC_NAME)
	rm -f config_done

romfs:
	mkdir -p $(ROMFSDIR)/usr/lib/xtables
	$(ROMFSINST) $(THISDIR)/$(SRC_NAME)/iptables/xtables-multi /bin/xtables-multi
	$(ROMFSINST) -s xtables-multi /bin/iptables
	$(ROMFSINST) -s xtables-multi /bin/iptables-restore
	$(ROMFSINST) -s xtables-multi /bin/iptables-save
ifdef CONFIG_IPV6
	$(ROMFSINST) -s xtables-multi /bin/ip6tables
	$(ROMFSINST) -s xtables-multi /bin/ip6tables-restore
	$(ROMFSINST) -s xtables-multi /bin/ip6tables-save
endif
