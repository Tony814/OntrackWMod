## Binary patches for the Windows 3.1 ONTRACKW.386 driver

This repo houses the binary patches for the ONTRACKW.386 driver that allow it to pass validation for drives that comply with ATA-2.  Drives that do not update registers 1F2h, 1F3h, 1F4h, or 1F5h after a successful command will fail validation on the original versions of nearly all Windows 3.1 FastDisk drivers.  The patches listed herein modify the validation tests to not fail if those aforementioned registers are not updated (and only those registers).


### Steps to take

1.  Check the version of the ONTRACKW.386 driver that you have.  You can edit it and scroll down to the bottom, or you can `set ONTRACKDEBUG=Y` before you start Windows.  I have provided the patch for V1.07 (Ontrack Disk Manager v9.57, courtesy of PhilsComputerLab.com).
2.  Verify the SHA1 of your driver matches the one provided for ONTRACKW.386.
3.  Download a copy of BSDiff for your OS.
4.  Use BSPatch to patch your driver.
5.  Verify the SHA1 of your patched driver matches the ONTRACKX.386.sha1 file provided in the respective folder.
6.  Profit!


#### Technical Details

This driver splits Phases 5-A/B into separate sections for CHS and LBA drives.  For all offending phase checks, we patch out a `jz PutValidationInCxAndPrint` with QTY 2 2-byte NOP (66 90).
- CHS drives get patched only in Phases 6,7,8,9 (0D,0E,0F,10 and 14,15,16,17 as well).
- LBA drives add another phase in there because the Cylinder High (1F4) and Low (1F5) registers cannot be combined into a single check; each would have been checked independently.  Phases 6,7,8,9,A (et al.) all have the same jump instruction patched out.
