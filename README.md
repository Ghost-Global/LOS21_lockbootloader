
### Lock Bootloader on LineageOS 21.0

#### Step 1: Apply Updates
Follow the steps below to update the necessary files:

- **Makefile:** `build/core/Makefile`
- **BoardConfig-common.mk:** `device/google/zuma/BoardConfig-common.mk`
- **BoardConfigLineage.mk:** `device/google/zuma/BoardConfigLineage.mk`
- **BoardConfigLineage_bluejay.mk:** `device/google/akita/BoardConfigLineage.mk`
- **testkey_rsa4096.pem:** `external/avb/test/data/testkey_rsa4096.pem`

#### Step 2: Rebuild the OS
Execute the following commands to rebuild the OS:

```bash
. build/envsetup.sh
croot
make clean
```

Then, build the OS with:

```bash
brunch akita
```

#### Step 3: Generate the `pkmd.bin` File
Run the following command to extract the public key and create the `pkmd.bin` file:

```bash
external/avb/avbtool.py extract_public_key --key external/avb/test/data/testkey_rsa4096.pem --output pkmd.bin
```

#### Step 4: Flash the Software
Use the script provided to flash the software after building.

#### Step 5: Lock the Bootloader
Follow these steps to lock the bootloader:

1. Boot the device into **fastboot mode** (software installation mode).
2. Execute the following commands:

```bash
fastboot $* flash vbmeta_system `dirname $0`/images/vbmeta_system.img
if [ $? -ne 0 ] ; then echo "Flash vbmeta_system error"; exit 1; fi
fastboot $* flash vendor_kernel_boot `dirname $0`/images/vendor_kernel_boot.img
if [ $? -ne 0 ] ; then echo "Flash vendor_kernel_boot error"; exit 1; fi
fastboot $* flash init_boot `dirname $0`/images/init_boot.img
if [ $? -ne 0 ] ; then echo "Flash init_boot error"; exit 1; fi
fastboot $* flash vbmeta_vendor `dirname $0`/images/vbmeta_vendor.img
if [ $? -ne 0 ] ; then echo "Flash vbmeta_vendor error"; exit 1; fi
fastboot $* flash vbmeta `dirname $0`/images/vbmeta.img
if [ $? -ne 0 ] ; then echo "Flash vbmeta error"; exit 1; fi
fastboot $* flash dtbo `dirname $0`/images/dtbo.img
if [ $? -ne 0 ] ; then echo "Flash dtbo error"; exit 1; fi
fastboot $* flash vendor_boot `dirname $0`/images/vendor_boot.img
if [ $? -ne 0 ] ; then echo "Flash vendor_boot error"; exit 1; fi
fastboot $* flash boot `dirname $0`/images/boot.img
if [ $? -ne 0 ] ; then echo "Flash boot error"; exit 1; fi
fastboot $* flash pvmfw `dirname $0`/images/pvmfw.img
if [ $? -ne 0 ] ; then echo "Flash pvmfw error"; exit 1; fi
fastboot $* flash super  `dirname $0`/images/super_empty.img
if [ $? -ne 0 ] ; then echo "Flash boot error"; exit 1; fi
fastboot $* -w
if [ $? -ne 0 ] ; then echo "Flash -w error"; exit 1; fi
fastboot $* reboot fastboot
if [ $? -ne 0 ] ; then echo "Flash Reboot fastboot error"; exit 1; fi
fastboot $* flash system `dirname $0`/images/system.img
if [ $? -ne 0 ] ; then echo "Flash system error"; exit 1; fi
fastboot $* flash product `dirname $0`/images/product.img
if [ $? -ne 0 ] ; then echo "Flash product error"; exit 1; fi
fastboot $* flash system_ext `dirname $0`/images/system_ext.img
if [ $? -ne 0 ] ; then echo "Flash system_ext error"; exit 1; fi
fastboot $* flash vendor `dirname $0`/images/vendor.img
if [ $? -ne 0 ] ; then echo "Flash vendor error"; exit 1; fi
fastboot $* flash vendor_dlkm `dirname $0`/images/vendor_dlkm.img
if [ $? -ne 0 ] ; then echo "Flash vendor_dlkm error"; exit 1; fi
fastboot $* flash system_dlkm `dirname $0`/images/system_dlkm.img
if [ $? -ne 0 ] ; then echo "Flash system_dlkm error"; exit 1; fi

fastboot $* reboot bootloader
if [ $? -ne 0 ] ; then echo "Reboot bootloader error"; exit 1; fi

fastboot $* erase avb_custom_key
if [ $? -ne 0 ] ; then echo "erase avb_custom_key error"; exit 1; fi

fastboot $* reboot bootloader
if [ $? -ne 0 ] ; then echo "Reboot bootloader error"; exit 1; fi

fastboot $* flash avb_custom_key `dirname $0`/pkmd.bin
if [ $? -ne 0 ] ; then echo "Flash avb_custom_key error"; exit 1; fi

fastboot $* reboot bootloader
if [ $? -ne 0 ] ; then echo "Reboot bootloader error"; exit 1; fi

fastboot $* flashing lock
if [ $? -ne 0 ] ; then echo "flashing lock error"; exit 1; fi


```

#### Step 6: Disable OEM Unlocking
Go to the **Developer Options** and disable the **OEM Unlock** option.
