
### Lock Bootloader on LineageOS 21.0

#### Step 1: Apply Updates
Follow the steps below to update the necessary files:

- **Makefile:** `build/core/Makefile`
- **BoardConfig-common.mk:** `device/google/zuma/BoardConfig-common.mk`
- **BoardConfigLineage.mk:** `device/google/zuma/BoardConfigLineage.mk`
- **BoardConfigLineage_bluejay.mk:** `device/google/akita/BoardConfigLineage.mk`
- **avb_rsa4096.pem:** `/home/huytp/.android-certs/releasekey.key`

#### Step 2: Rebuild the OS
Execute the following commands to rebuild the OS:

```bash
. build/envsetup.sh
croot
make clean
```

Then, build the OS with:

```bash
brunch bluejay
```

#### Step 3: Generate the `pkmd.bin` File
Run the following command to extract the public key and create the `pkmd.bin` file:

```bash
external/avb/avbtool.py extract_public_key --key /home/huytp/.android-certs/releasekey.key --output pkmd.bin
```

#### Step 4: Flash the Software
Use the script provided to flash the software after building.

#### Step 5: Lock the Bootloader
Follow these steps to lock the bootloader:

1. Boot the device into **fastboot mode** (software installation mode).
2. Execute the following commands:

```bash
fastboot erase avb_custom_key
fastboot reboot bootloader
fastboot flash avb_custom_key pkmd.bin
fastboot reboot bootloader
fastboot flashing lock
```

#### Step 6: Disable OEM Unlocking
Go to the **Developer Options** and disable the **OEM Unlock** option.
