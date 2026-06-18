``` PowerShell
# =====================================================================
# STEP 1: CREATE THE STREAM (Already completed)
# =====================================================================
# This packed the game files into an uncompressed archive stream.
.\7z\7za.exe a -ttar game.tar "..\Hollow Knight\"

# =====================================================================
# STEP 2: PRECOMP (Currently running)
# =====================================================================
# This unzips internal Unity streams (zlib/png/mp3) so they can be compressed better.
# WAIT for this to completely finish before moving to Step 3.
.\precomp.exe -intense -v game.tar

# =====================================================================
# STEP 3: SREP DEDUPLICATION (Run this next)
# =====================================================================
# This scans the data and strips out duplicate sequences across files.
# Uses a 4 GB dictionary profile maximized for your 32 GB RAM.
.\srep.exe -m3f -d4g -tc64 game.pcf game.srep

# =====================================================================
# STEP 4: THE ULTIMATE SQUEEZE (Run after SREP finishes)
# =====================================================================
# This applies heavy LZMA2 compression using a massive 256 MB dictionary.
.\7z\7za.exe a -mx9 -md=256m -ms=on HollowKnight_Repack.7z game.srep

# =====================================================================
# STEP 5: CLEANUP
# =====================================================================
# Once 7-Zip says "Everything is Ok", delete the massive temporary streams.
Remove-Item game.tar, game.pcf, game.srep
```

``` powershell
# 1. Extract the 7z archive back to the SREP file
.\7z\7za.exe x HollowKnight_Repack.7z

# 2. Undo the SREP duplication layout back into a PCF file
.\srep.exe -d game.srep game.pcf

# 3. Re-compress the streams back to original zlib standards
.\precomp.exe -d game.pcf game.tar

# 4. Extract the final game folder out of the tar file
.\7z\7za.exe x game.tar -o"..\Hollow Knight Extracted\"
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
.\7z\7za.exe a -mx9 -md=512m -fb=273 -ms=on TLOU_Repack.7z game.srep
```
