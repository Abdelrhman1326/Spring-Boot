``` PowerShell
# =====================================================================
# STEP 1: CREATE THE STREAM (Completed)
# =====================================================================
.\7z\7za.exe a -ttar game.tar "..\Hollow Knight\"

# =====================================================================
# STEP 2: PRECOMP (Running...)
# =====================================================================
# This will output: game.pcf
.\precomp.exe -intense -v game.tar

# =====================================================================
# STEP 3: SREP DEDUPLICATION (Run this next)
# =====================================================================
.\srep.exe -m3f -l512m game.pcf game.srep

# =====================================================================
# STEP 4: THE ULTIMATE SQUEEZE
# =====================================================================
.\bin\arc.exe a -mx -ld=1g -w.\ "game.arc" "game.srep"

# =====================================================================
# STEP 5: CLEANUP
# =====================================================================
# Safely wipe the heavy intermediate scratch files
Remove-Item game.tar, game.pcf, game.srep
```

``` powershell
# 1. Extract the 7z archive back to the SREP file
.\7z\x64\7za.exe x HollowKnight_Repack.7z -o.

# 2. Undo the SREP duplication layout back into a PCF file
.\srep.exe -d game.srep game.pcf

# 3. Re-compress the streams back to original zlib standards
.\precomp.exe -d game.pcf game.tar

# 4. Extract the final game folder out of the tar file
.\7z\x64\7za.exe x game.tar -o"..\Hollow Knight Extracted"
```

``` powershell
# Move to your working directory
cd C:\Users\user\Desktop\ultra

# 1. Create the base uncompressed tar stream
.\7z\7za.exe a -ttar game.tar "..\The Last of Us Part I\"

# 2. Run oo2reck to un-pack the Oodle streams 
# This decodes the Kraken/Leviathan algorithms into raw bytes
.\oo2reck.exe c game.tar game.ood

# 3. Run Precomp on the resulting file
# This cleans up any standard zlib/png streams skipped by oo2reck
.\precomp.exe -intense -v game.ood

# 4. Run SREP (Max 8GB dictionary for your 32GB RAM)
# Now that Oodle and zlib are decoded, SREP will find massive repetition loops
.\srep.exe -m3f -d8g -tc64 game.ood.pcf game.srep

# 5. Final Ultimate LZMA2 7-Zip compression
.\7z\x64\7za.exe a -m0=lzma2 -mx9 -md=256m -ms=on HollowKnight_Repack_Final.7z game.srep
```
