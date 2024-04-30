# Off-Topic / Suggestions

  - Tiny11 Images storage:
    Supprusingly, binary diff between Orignal and Tiny11 images is very small (~90Mb)
    You can store binary diff along only one image using rdiff (<https://github.com/librsync/librsync>)
    - To create the diff:
      ```
      rdiff signature orig.iso orig.iso.sig
      rdiff delta tiny11.iso orig.sig tiny11.iso.rdiff
      del orig.sig
      ```
    - To restore the image using rdiff:
      ```
      rdiff patch orig.iso tiny11.iso tiny11.iso.rdiff
      ```

  - More optimzations by using manual installation:
    With manual install you can lower initial image size to under 8GB (if you disable pagefile)
    Here is an example (CAUTION! Might delete data on your disk if used blindly)
      - When first windows Setup screen appears, press (Shift-F10) to open terminal
      - Partition your drive. Example of adding a new windows partition on a free space at the end of the drive
        with already installed EFI OS (Windows / Linux):
          ```
          diskpart
          list disk
          sel disk 0 [Choose you disk]
          sel par 1
          assign letter=e
          create par pri
          list par
          sel par 3 [Choose you partition]
          format quick compress
          assign letter=c
          ```
      - Install windows:
          ```
          dism /apply-image /imagefile:d:\sources\install.wim /index:1 /applydir:c:\ /compact
          bcdboot c:\windows /s e: /f UEFI
          ```
      - Close terminal, Quit Windows Setup
      - Let windows reboot a few times and ask you questions to initialize your new Windows install

  - Things to do after install
    - If you installed windows manually, you won't have Recovery Partition (WRE)
      If you are planning on enabling Bitlocker, Windows will create WRE partition automatically.
      You can disable WRE to avoid adding Recovery Partition during encryption:
      reagentc /disable
    - Even though Tiny11 installed in "Compact" mode, only windows basic windows components will be compressed.
      You might consider compressing the whole C: drive by using corespondng iption in driver properties window.
    - If you prefer using password instead TPM with Bitlocker, you can do the followng:
      - Disable TPM in Bios (CAUTON! Changing BIOS settings might cause irreversible damage to you hardware and data)
      - Enable non-TPM (password) encryptin:
        - gpedit.msc (Local Group Policy Editor)
        - Navigate to: Computer Configuration/Administrative Templates/Windows Components/BitLocker Drive Encryption/Operating System Drive/Require additional authentication at startup
        - Choose (*) Enabled
        - Choose [v] Allow BitLocker without a compatible TPM
        - Enable Bitlocker as usual
