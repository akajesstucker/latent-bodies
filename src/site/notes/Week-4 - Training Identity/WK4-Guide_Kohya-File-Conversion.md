---
dg-publish: true
permalink: /notes/week-4/kohya-file-conversion/
---
# Converting Image Files for Kohya Training

This note covers converting raw or mixed-format images to JPG using the command line. If you would rather convert files manually, open them in Preview (Mac), Photoshop, or any image editor and export as JPEG or PNG — then come back to the main guide.

---

## Converting raw files (DNG, CR2, ARW) to JPG

This requires ImageMagick. If you do not have it, download it from imagemagick.org and install it before continuing.

Navigate into your image subfolder first:

**Windows (PowerShell):**
```powershell
cd "C:\lora-training\dataset\15_myname"
```

**Mac (Terminal):**
```bash
cd /path/to/dataset/15_myname
```

Then run the conversion. Replace `*.DNG` with your raw format if different (e.g. `*.CR2`, `*.ARW`).

**Windows (PowerShell):**
```powershell
Get-ChildItem -Filter *.DNG | ForEach-Object { magick $_.Name -auto-orient -auto-level ($_.BaseName + ".jpg") }
```

**Mac (Terminal):**
```bash
for f in *.DNG; do magick "$f" -auto-orient -auto-level "${f%.DNG}.jpg"; done
```

Run `dir` (Windows) or `ls` (Mac) to confirm the converted JPGs are there before deleting the originals:

**Windows:**
```powershell
Remove-Item *.DNG
```

**Mac:**
```bash
rm *.DNG
```

---

## Normalizing .JPEG extensions to .jpg

On Windows, `-Filter *.jpg` catches both `.jpg` and `.JPG` automatically. If your files end in `.JPEG`, rename them to `.jpg` first:

**Windows (PowerShell):**
```powershell
Get-ChildItem -Filter *.JPEG | Rename-Item -NewName { $_.Name -replace '\.JPEG$','.jpg' }
```

**Mac (Terminal):**
```bash
for f in *.JPEG; do mv "$f" "${f%.JPEG}.jpg"; done
```

Once your files are all `.jpg` (or all `.png`), return to the [[WK4-Guide_Kohya-Lora-Training|Kohya training guide]] and continue with the renaming step.
