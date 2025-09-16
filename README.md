# Hardbrick Recovery Guide

This guide explains how to recover a **hardbricked Elo I-Series 2.0** running **Android 7.1**, and by using **Xiaomi Note 4, Android 8.1 Recovery ROM**.  
⚠️ **Warning:** This process will erase all data. Proceed at your own risk.

---

## Table of Contents
- [Prerequisites](#prerequisites)
- [Part 1: Flashing the Recovery ROM (Xiaomi Note 4, Android 8.1)](#part-1-flashing-the-recovery-rom-xiaomi-note-4-android-81)
- [Part 2: Flashing the Elo ROM (Android 7.1 Nougat, v3.66.43)](#part-2-flashing-the-elo-rom-elo-i-series-20-for-android-71-nougat-v36643)
- [Part 3: Accessing Fastboot & Flashing via Fastboot](#part-3-accessing-fastboot--flashing-via-fastboot)
- [Part 4: Bootloader Unlock (MSM8953 Only)](#part-4-bootloader-unlock-msm8953-only)
- [Unlock-Brickloader](#unlock-brickloader)
- [Completion](#completion)
- [Notes & Next Steps](#notes--next-steps)

---

## Prerequisites

Before starting, make sure you have the following:

- A Windows PC with **Qualcomm drivers** installed - [Download](https://github.com/mollyinduced/elo2.0-blackbox/releases/download/1.0/qualcomm.drivers.zip)  
- **QFIL (Qualcomm Flash Image Loader)** software - [Download](https://github.com/mollyinduced/elo2.0-blackbox/releases/download/1.0/qpst_2.7.496.zip)
- USB cable compatible with Elo I-Series 2.0
- The following files:
  - `flashboot-rom.zip` (Recovery ROM for Xiaomi Note 4, Android 8.1)  
  - `elo2.0-fix.zip` (Elo I-Series 2.0 fix package)  
- Basic knowledge of **ADB** and **Fastboot** commands
- Enough battery charge (recommended **50%+**)  
- Unlocked Brickloader (if locked go to #unlock-brickloader)

## Part 1: Flashing the Recovery ROM (Xiaomi Note 4, Android 8.1)

1. Unplug the device and plug it back in.
2. Ensure the device is in **EDL (Emergency Download) mode**.
3. Open **QFIL** and connect to the device via **ADB**.
4. Open **Partition Manager** and erase all data.
5. Extract `flashboot-rom.zip`.
6. In QFIL, load the following files:
   - Firehose Programmer  
   - `rawprogram0.xml`  
   - `patch0.xml`  
7. Press **Download** and wait for the partitions to flash back to their original state.
8. Once complete, continue to **Part 2**.

---

## Part 2: Flashing the Elo ROM (Elo I-Series 2.0 for Android 7.1 Nougat, v3.66.43)

1. Unplug the device and replug it in.
2. Ensure the device is in **EDL mode**.
3. Open **QFIL** and connect via **ADB**.
4. Extract `elo2.0-fix.zip`.
5. In QFIL, load the following files for `elo2.0-fix`:
   - Firehose Programmer  
   - `modified_rawprogram0.xml`  
   - `patch0.xml`  
6. Press **Download** and wait for flashing to finish.
7. Once done, proceed to **Part 3**.

---

## Part 3: Accessing Fastboot & Flashing via Fastboot

1. Unplug the device.  
   Before replugging it, **hold the Home button** on the back of the device, then plug it back in.
2. Keep holding the Home button until the power indicator flashes for 3 seconds and stops. Release the button when it stops.
3. Wait a few seconds – the device should boot into **Recovery Mode**.  
   > **Controls:** Home = Navigate | Power = Select
4. In Recovery, navigate to **Reboot to Bootloader** and press the power button.  
   The device should now boot into **Fastboot**.
5. From your PC, go back to the `elo2.0-fix` folder and run `flash_elo.bat`.
6. Wait for flashing to finish and reboot the device.

---

## Misc: Bootloader Unlock (MSM8953 Only)
---

### Tutorial:
1. **Reboot to EDL**  
   ```bash
   adb reboot edl
   ```
2. **Check QFIL connection**  
   - Open QFIL.  
   - You should see **Qualcomm HS-USB QD-Loader 9008 (COM****)**.  
   - Select **Flat Build**.  
   - Select your firehose (`emmc_firehose_8***.mbn`).  
3. **Access Partition Manager**  
   - Go to **Tools → Partition Manager**.  
   - Click **OK**.  
   - Focus on the **/devinfo** partition.  
4. **Dump /devinfo**  
   - Right-click **devinfo** → *Manage Partition Data*.  
   - Click **Read Data**.  
   - Check the log window for the save location (usually in `AppData\Roaming\Qualcomm`).  
   - The dump will look like:  
     `ReadData_emmc_Lun0_0x1c000_Len16384_DT_YYYY_MM_DD_HH_MM_SS.bin`  
   - Copy this file to a safe location (desktop + backup).  
5. **Hex edit /devinfo**  
   - Open the dumped file in **HxD**.  
   - Find this sequence:  
     ```
     41 4E 44 52 4F 49 44 2D 42 4F 4F 54 21 00 00 00
     00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
     ```  
   - Replace it with:  
     ```
     41 4E 44 52 4F 49 44 2D 42 4F 4F 54 21 00 00 00
     01 00 00 00 00 00 00 00 01 00 00 00 00 00 00 00
     ```  
   - **Go to offset `0x007FFE00`** and repeat the same replacement (ZTE placed a second marker there).  
6. **Save the file**  
   - Click **File → Save**.  
   - Boot your phone into **EDL** again (reopen QFIL if needed).  
7. **Write back modified /devinfo**  
   - In QFIL Partition Manager, right-click **devinfo** → *Manage Partition Data*.  
   - Choose **Load Image**.  
   - Select your modified `.bin` file.  
   - Wait for it to finish, then reboot the phone.  
---

### Results
- Your bootloader is now unlocked.  

---

## Completion

If all steps were followed successfully, your device should now boot into a **developer state**.  
From here, you can:
- Recover via **ADB Sideload**  
- Flash a **custom OS** using the same method  

---

## Notes & Next Steps

- This method does not currently include **backup or recovery images**. Work is ongoing to provide a safer recovery option.  
- Contributions, suggestions, or improvements are welcome — please share your insights to help others facing similar hardware issues.  
