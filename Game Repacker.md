## Structure:

``` Plaintext
ultra/
├── bin/
│   ├── 7z/
│   │   └── x64/
│   │       └── 7za.exe         # Standard 7-Zip standalone engine
│   ├── arc.exe                 # FreeArc 0.50 alpha engine
│   ├── oo2reck.exe             # Oodle stream processor
│   ├── precomp.exe             # Zlib stream pre-processor
│   └── srep.exe                # Smart REPeater deduplicator
└── compress.exe                # Your compiled automation script
```

## Scenario A: Standard Engine Pipeline (e.g., Hollow Knight, Tomb Raider)

**Trigger Conditions:** No proprietary Oodle layout binaries are detected. The game utilizes standard `.tiger`, `.assets`, or general compressed archive structures natively built on Zlib/Deflate streams.

**Step 1: Create Base Stream** Packs the target folder into an uncompressed, structured TAR stream.
``` PowerShell
.\bin\7z\x64\7za.exe a -ttar game.tar "..\Target Game Folder\"
```

**Step 2: Stream Inflation (Precomp) & Target Purge** Deep-scans the stream to find and temporarily decompress all hidden Zlib layers into `game.pcf`. The moment it finishes, the massive `game.tar` is immediately deleted.
``` PowerShell
.\bin\precomp.exe -intense -v game.tar
Remove-Item game.tar
```

**Step 3: Sliding Deduplication (SREP) & PCF Purge** Scans across the inflated `.pcf` file with a sliding dictionary window to replace identical duplicate memory sequences with tiny pointers. The moment `game.srep` is written, `game.pcf` is immediately deleted.
``` PowerShell
.\bin\srep.exe -m3f -l4g -tc64 game.pcf game.srep
Remove-Item game.pcf
```

**Step 4: The Final Squeeze (FreeArc) & SREP Purge** Applies an advanced ultra-compression profile over the deduplicated map. Once `Game_Repack.arc` is safely validated, the final intermediate scratch file `game.srep` is permanently deleted.
``` PowerShell
.\bin\arc.exe a -mx -ld=1g -w.\ "Game_Repack.arc" "game.srep"
Remove-Item game.srep
```


## Scenario B: Modern Oodle Engine Pipeline (e.g., The Last of Us, Sony Ports)

**Trigger Conditions:** Target game folder contains `oo2core_*.dll`. Assets are heavily wrapped using proprietary Kraken or Leviathan compression constraints.

**Step 1: Create Base Stream**
``` PowerShell
.\bin\7z\x64\7za.exe a -ttar game.tar "..\Target Sony Game Folder\"
```

**Step 2: Oodle Stream Decoding & TAR Purge** Intercepts and unpacks the aggressive Oodle asset layouts into raw binary byte blocks (`game.ood`). The input `game.tar` is immediately purged.
``` PowerShell
.\bin\oo2reck.exe c game.tar game.ood
Remove-Item game.tar
```

**Step 3: Residual Stream Inflation & OOD Purge** Catches any remaining traditional zlib/png assets left behind by the Oodle wrapper, emitting `game.ood.pcf`. The intermediate `game.ood` stream is dropped immediately.
``` PowerShell
.\bin\precomp.exe -intense -v game.ood
Remove-Item game.ood
```

**Step 4: Maximum Deduplication & PCF Purge** Leverages an expanded memory profile optimized for large-scale AAA open-world structural duplicate detection. The moment `game.srep` completes, `game.ood.pcf` is erased.
``` PowerShell
.\bin\srep.exe -m3f -l8g -tc64 game.ood.pcf game.srep
Remove-Item game.ood.pcf
```

**Step 5: Final LZMA2 Archival Layout & SREP Purge** Compresses the fully raw, deduplicated output using a maximized 256MB dictionary block allocation. Once 7z outputs "Everything is Ok", the final scratch file `game.srep` is safely wiped.
``` PowerShell
.\bin\7z\x64\7za.exe a -m0=lzma2 -mx9 -md=256m -ms=on Game_Oodle_Repack.7z game.srep
Remove-Item game.srep
```



## Reversal & Decompression Execution Docs
To reconstruct the original asset layout back to fully working game directories, the decompression mechanism mirrors this immediate-purge strategy to ensure safe user storage limits.

### To Unpack a Standard `.arc` Repack:
``` PowerShell
# 1. Extract FreeArc container
.\bin\arc.exe x Game_Repack.arc

# 2. Reconstruct duplicate arrays, then drop the srep index file
.\bin\srep.exe -d game.srep
Remove-Item game.srep

# 3. Re-compress streams back to original Zlib standard, then drop the pcf stream
.\bin\precomp.exe -d game.pcf game.tar
Remove-Item game.pcf

# 4. Unpack tar back to standard game directory structure, then drop the tar stream
.\bin\7z\x64\7za.exe x game.tar -o"..\Extracted Game"
Remove-Item game.tar
```

### To Unpack an Oodle `.7z` Repack:
``` PowerShell
# 1. Extract 7z container
.\bin\7z\x64\7za.exe x Game_Oodle_Repack.7z -o.

# 2. Reconstruct duplicate arrays, then drop the srep index file
.\bin\srep.exe -d game.srep
Remove-Item game.srep

# 3. Re-compress residual Zlib streams, then drop the pcf stream
.\bin\precomp.exe -d game.ood.pcf game.ood
Remove-Item game.ood.pcf

# 4. Re-pack raw bytes back into Oodle containers, then drop the ood stream
.\bin\oo2reck.exe d game.ood game.tar
Remove-Item game.ood

# 5. Extract original game folder structure, then drop the final tar container
.\bin\7z\x64\7za.exe x game.tar -o"..\Extracted Sony Game"
Remove-Item game.tar
```

