KERNELDIR=$(KERNEL_DIR)
EXTRA_CFLAGS += -DWAN2LAN

INCDIR		:= inc
SRCDIR		:= src

ifneq ($(TCSUPPORT_CPU_EN7521),)
	EXTRA_CFLAGS += -I$(MODULES_FE_7512_DIR)
	EXTRA_CFLAGS += -I$(MODULES_QDMA_7512_DIR)
else
	EXTRA_CFLAGS += -I$(MODULES_QDMA_DIR)
	EXTRA_CFLAGS += -I$(MODULES_RAETH_DIR)
endif
EXTRA_CFLAGS += -I$(MODULES_XPON_WAN_DIR)/$(INCDIR) -I$(MODULES_XPON_WAN_DIR)/$(INCDIR)/epon -I$(GLOBAL_INC_DIR)/modules/
ifeq ($(strip $(TCSUPPORT_3_18_21_KERNEL)),)
EXTRA_CFLAGS += -O0 -save-temps
endif
EXTRA_CFLAGS += -D CONFIG_SEND_ACK_PLOAMU=3 \
				-D CONFIG_USE_FOR_TEST \
				-D XPON_MAC_CONFIG_DEBUG 
#				-D CONFIG_USE_RX_NAPI \

ifneq ($(TCSUPPORT_CPU_EN7521),)
	EXTRA_CFLAGS += -D CONFIG_USE_RX_NAPI
endif

ifeq ($(CONFIG_USE_MT7520_ASIC),)
	#EXTRA_CFLAGS += -DCONFIG_USE_A60901
	EXTRA_CFLAGS += -DCONFIG_USE_A60928
endif
				
EXTRA_CFLAGS += -DEPON_MAC_HW_TEST
EXTRA_CFLAGS += -DTCSUPPORT_EPON_RGST_SILENT

ifneq ($(TCSUPPORT_EPON_FPGA_WORKAROUND),)
EXTRA_CFLAGS += -DTCSUPPORT_EPON_FPGA_WORKAROUND
endif


ifneq ($(TCSUPPORT_WAN_GPON),)
	GPON_SRC	:= gpon_init.c gpon_act.c gpon_ploam.c gpon.c gpon_dev.c gpon_qos.c gpon_dvt.c gpon_recovery.c
	ifneq ($(TCSUPPORT_CPU_EN7521),)
		GPON_SRC	+= gpon_power_management.c 
	endif
	GPON_OBJ	:= $(GPON_SRC:%.c=$(SRCDIR)/gpon/%.o)

	XMCS_SRC 	+= xmcs_gpon.c 
	PWAN_SRC	+= gpon_wan.c
endif

ifneq ($(TCSUPPORT_WAN_EPON),)
	EPON_SRC	:= epon_main.c epon_mpcp.c epon_util.c epon_ioctl.c epon_timer.c epon_hwtest.c
	EPON_OBJ	:= $(EPON_SRC:%.c=$(SRCDIR)/epon/%.o)

	XMCS_SRC 	+= xmcs_epon.c 
	PWAN_SRC	+= epon_wan.c
endif

XMCS_SRC	+= xmcs_mci.c xmcs_if.c xmcs_fdet.c xmcs_phy.c
XMCS_OBJ	:= $(XMCS_SRC:%.c=$(SRCDIR)/xmcs/%.o)

PWAN_SRC	+= xpon_netif.c 
ifneq ($(TCSUPPORT_XPON_HAL_API_EXT),)
PWAN_SRC	+= tc_vlanfilter.c
PWAN_SRC    	+= tc_l2lu.c
endif
PWAN_OBJ	:= $(PWAN_SRC:%.c=$(SRCDIR)/pwan/%.o)

TOTAL_OBJ	:= $(SRCDIR)/xpondrv.o $(SRCDIR)/phy_if_wrapper.o $(SRCDIR)/xpon_led.o $(SRCDIR)/xpon_daemon.o $(SRCDIR)/omci_oam_monitor.o\
       		   $(GPON_OBJ) $(EPON_OBJ) $(PWAN_OBJ) $(XMCS_OBJ) 

xpon-objs 	:= $(TOTAL_OBJ)
obj-m		:= xpon.o

#EXTRA_CFLAGS+=-g -Wa,-ahls >>  $(MODULES_XPON_WAN_DIR)/xpon.asm

all:
	$(MAKE) -C $(KERNELDIR) M=`pwd` modules
	$(STRIP) --strip-unneeded xpon.ko
	
clean:
	cd $(FILESYSTEM_DIR)/lib/modules/ && rm -f xpon.ko
	$(MAKE) -C $(KERNELDIR) M=`pwd` clean


