# Tesseract OCR Quick Reference

## Overview

**Tesseract** is an open-source Optical Character Recognition (OCR) engine developed by Google. It supports 100+ languages and can extract text from images, PDFs, and scanned documents with high accuracy.

**Key Features:**
- Multi-language support (Latin, Cyrillic, Arabic, Asian scripts)
- Multiple output formats (txt, pdf, hocr, tsv, alto)
- Page segmentation modes for different layouts
- Training support for custom fonts/languages
- Command-line and library API (C++, Python, Java)

**Version:** This guide covers Tesseract 4.x and 5.x (LSTM-based)

---

## Installation

### Linux (Fedora/RHEL)
```bash
# Install Tesseract engine
sudo dnf install tesseract

# Install language packs
sudo dnf install tesseract-langpack-eng # English
sudo dnf install tesseract-langpack-hun # Hungarian
sudo dnf install tesseract-langpack-deu # German
sudo dnf install tesseract-langpack-spa # Spanish

# List all available language packs
dnf search tesseract-langpack
```

### Ubuntu/Debian
```bash
sudo apt update
sudo apt install tesseract-ocr
sudo apt install tesseract-ocr-eng tesseract-ocr-hun
```

### macOS
```bash
brew install tesseract
brew install tesseract-lang # All languages
```

### Verify Installation
```bash
tesseract --version
tesseract --list-langs # Show installed languages
```

---

## Basic Usage

### Simple Text Extraction

```bash
# Extract text from image to stdout
tesseract image.png stdout

# Save to text file
tesseract image.png output    # Creates output.txt
tesseract scan.jpg result -l eng # Specify English language

# Multi-page processing
tesseract page1.png output1
tesseract page2.png output2
```

### Language Selection

```bash
# Single language
tesseract document.png output -l hun # Hungarian

# Multiple languages (combined)
tesseract mixed.png output -l eng+hun # English + Hungarian
tesseract arabic.png output -l ara+eng

# Script-based detection
tesseract chinese.png output -l chi_sim # Simplified Chinese
tesseract chinese.png output -l chi_tra # Traditional Chinese
```

---

## Output Formats

### Text Output (Default)
```bash
tesseract image.png output     # Creates output.txt
tesseract image.png stdout     # Print to console
tesseract image.png - > file.txt  # Redirect to file
```

### PDF Output (Searchable)
```bash
# Create searchable PDF
tesseract scan.jpg output pdf          # Creates output.pdf
tesseract scan.jpg output -l eng pdf      # With language
tesseract image.png output pdf txt       # PDF + TXT both

# Batch convert images to single PDF
tesseract page*.png output pdf # Warning: May not combine pages properly
```

### HOCR (HTML-based OCR format)
```bash
# Preserves layout, coordinates, confidence scores
tesseract document.png output hocr       # Creates output.hocr
tesseract scan.jpg output -l eng hocr
```

### TSV (Tab-Separated Values)
```bash
# Detailed output with bounding boxes and confidence
tesseract image.png output tsv         # Creates output.tsv

# Columns: level, page_num, block_num, par_num, line_num, word_num, 
#     left, top, width, height, conf, text
```

### ALTO XML (Library Standard)
```bash
tesseract document.png output alto       # Creates output.xml
```

### Combined Outputs
```bash
# Generate multiple formats at once
tesseract scan.jpg output txt pdf hocr
tesseract image.png output txt tsv alto
```

---

## Page Segmentation Modes (PSM)

PSM controls how Tesseract detects text layout. Critical for accuracy.

```bash
# Syntax
tesseract image.png output --psm <mode>
```

### PSM Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| 0 | Orientation and script detection only | Detect document rotation |
| 1 | Automatic page segmentation with OSD | General documents |
| 2 | Automatic page segmentation (no OSD) | Skewed documents |
| 3 | Fully automatic (default) | Most cases |
| 4 | Single column of text | Book pages, articles |
| 5 | Single vertical block | Asian text |
| 6 | Single uniform block | Clean text blocks |
| 7 | Single text line | Cropped lines, captions |
| 8 | Single word | License plates, labels |
| 9 | Single word in circle | Logos, stamps |
| 10 | Single character | Character recognition |
| 11 | Sparse text (find as much as possible) | Noisy images |
| 12 | Sparse text with OSD | Rotated noisy images |
| 13 | Raw line (bypass segmentation) | Pre-processed lines |

### PSM Examples

```bash
# Single line of text (e.g., invoice number)
tesseract line.png output --psm 7

# License plate (single word)
tesseract plate.jpg output --psm 8 -l eng

# Handwritten note (sparse text)
tesseract note.jpg output --psm 11

# Book page (single column)
tesseract page.png output --psm 4 -l eng

# Detect rotation angle
tesseract rotated.png output --psm 0
```

---

## OCR Engine Modes (OEM)

OEM controls the OCR engine used.

```bash
# Syntax
tesseract image.png output --oem <mode>
```

### OEM Modes

| Mode | Engine | Description |
|------|--------|-------------|
| 0 | Legacy | Tesseract 3.x (older, faster, less accurate) |
| 1 | LSTM | Neural network-based (default, most accurate) |
| 2 | Legacy + LSTM | Combined engines |
| 3 | Default | Automatically choose best |

```bash
# Use LSTM engine (default in v4+)
tesseract image.png output --oem 1

# Legacy engine (faster, lower accuracy)
tesseract image.png output --oem 0

# Combined engines
tesseract image.png output --oem 2
```

---

## Practical Use Cases

### 1. Scan Receipt/Invoice

```bash
# Preprocess + OCR + TSV for structured data
convert receipt.jpg -resize 200% -sharpen 0x1 receipt_clean.png
tesseract receipt_clean.png output --psm 6 tsv

# Extract specific fields (e.g., total amount)
tesseract receipt.jpg stdout --psm 6 | grep -i "total"
```

### 2. Extract Text from PDF

```bash
# Convert PDF to images first
pdftoppm document.pdf page -png

# OCR each page
for img in page-*.png; do
tesseract "$img" "${img%.png}" -l eng pdf
done

# Combine PDFs (using pdfunite or similar)
pdfunite page-*.pdf combined_ocr.pdf
```

### 3. Batch Process Scanned Books

```bash
#!/bin/bash
# batch_ocr.sh

INPUT_DIR="scans"
OUTPUT_DIR="ocr_output"
LANG="eng"

mkdir -p "$OUTPUT_DIR"

for img in "$INPUT_DIR"/*.jpg; do
base=$(basename "$img" .jpg)
echo "Processing $base..."
tesseract "$img" "$OUTPUT_DIR/$base" -l "$LANG" --psm 4 pdf txt
done

echo "OCR complete. Output in $OUTPUT_DIR/"
```

### 4. Extract Table Data

```bash
# Use TSV output for tables
tesseract table.png output tsv

# Parse TSV with awk/Python to extract rows/columns
awk -F'\t' '$1==5 {print $12}' output.tsv # Extract word-level text
```

### 5. Process Screenshots with Code

```bash
# Code screenshots often need specific PSM
tesseract code_snippet.png output --psm 6 -l eng

# Preserve whitespace (important for Python/YAML)
tesseract code.png stdout --psm 6 | cat -A # Show whitespace
```

### 6. Handwritten Notes (Low Accuracy)

```bash
# Handwriting requires sparse text mode
tesseract handwritten.jpg output --psm 11 -l eng

# Better: Train custom model or use Google Vision API
# Tesseract is NOT optimized for handwriting
```

### 7. Multi-Language Documents

```bash
# English + Hungarian document
tesseract bilingual.png output -l eng+hun

# Arabic + English
tesseract arabic_doc.png output -l ara+eng --psm 1
```

### 8. Extract Text from Video Frames

```bash
# Extract frames from video
ffmpeg -i video.mp4 -vf fps=1 frame_%04d.png

# OCR all frames
for frame in frame_*.png; do
tesseract "$frame" "${frame%.png}" -l eng txt
done
```

### 9. Verify Text in Images (Testing)

```bash
# CI/CD verification: Check if image contains expected text
EXPECTED="Error 404"
RESULT=$(tesseract screenshot.png stdout)

if echo "$RESULT" | grep -q "$EXPECTED"; then
echo "Test passed: Text found"
else
echo "Test failed: Expected text not found"
exit 1
fi
```

### 10. Create Searchable PDF Archive

```bash
#!/bin/bash
# convert_archive.sh

for doc in archive/*.jpg; do
base=$(basename "$doc" .jpg)
tesseract "$doc" "searchable_pdfs/$base" -l eng pdf
done

# Merge all PDFs
cd searchable_pdfs
pdfunite *.pdf ../complete_archive.pdf
```

---

## Image Preprocessing

OCR accuracy depends heavily on image quality. Preprocessing is critical.

### Using ImageMagick

```bash
# Install ImageMagick
sudo dnf install ImageMagick

# Resize (larger = better OCR, up to a point)
convert input.jpg -resize 200% output.png

# Sharpen
convert input.jpg -sharpen 0x1 output.png

# Increase contrast
convert input.jpg -contrast -contrast output.png

# Convert to grayscale
convert input.jpg -colorspace Gray output.png

# Denoise
convert input.jpg -despeckle output.png

# Threshold (binarize)
convert input.jpg -threshold 50% output.png

# Full preprocessing pipeline
convert input.jpg \
-resize 200% \
-colorspace Gray \
-sharpen 0x1 \
-contrast \
-threshold 50% \
preprocessed.png

tesseract preprocessed.png output -l eng
```

### Deskew (Fix Rotation)

```bash
# Auto-deskew with ImageMagick
convert input.jpg -deskew 40% deskewed.jpg

# Or use tesseract's built-in OSD
tesseract input.jpg output --psm 0 # Detect rotation
# Then manually rotate:
convert input.jpg -rotate 90 rotated.jpg
```

### Remove Borders/Noise

```bash
# Trim white borders
convert input.jpg -trim output.jpg

# Remove salt-and-pepper noise
convert input.jpg -median 2 output.jpg
```

---

## Configuration Files

### Custom Configuration

```bash
# Use config file
tesseract image.png output -c tessedit_char_whitelist=0123456789

# Create custom config file (e.g., digits_only)
echo "tessedit_char_whitelist 0123456789" > digits.conf
tesseract image.png output digits
```

### Common Configurations

```bash
# Digits only (useful for phone numbers, IDs)
tesseract image.png output -c tessedit_char_whitelist=0123456789

# Alphanumeric only
tesseract image.png output -c tessedit_char_whitelist=ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789

# Preserve whitespace
tesseract image.png output -c preserve_interword_spaces=1

# Min confidence threshold (reject low-confidence chars)
tesseract image.png output -c tessedit_char_min_confidence=80
```

---

## Advanced Examples

### Extract Metadata from TSV

```bash
# Get word-level confidence scores
tesseract image.png output tsv

# Parse TSV to find low-confidence words
awk -F'\t' '$11 < 80 && $12 != "" {print $12, $11}' output.tsv

# Average confidence score
awk -F'\t' '$11 > 0 {sum+=$11; count++} END {print sum/count}' output.tsv
```

### Parallel Processing

```bash
# Process 100 images in parallel (GNU parallel)
find images/ -name "*.jpg" | \
parallel -j 8 tesseract {} {.} -l eng pdf

# Using xargs
ls images/*.png | \
xargs -n 1 -P 4 -I {} tesseract {} {}.txt -l eng
```

### Integration with Python

```python
#!/usr/bin/env python3
import pytesseract
from PIL import Image

# Simple OCR
text = pytesseract.image_to_string(Image.open('image.png'), lang='eng')
print(text)

# Get detailed data
data = pytesseract.image_to_data(Image.open('image.png'), output_type='dict')
print(f"Confidence: {data['conf']}")

# Custom config
custom_config = r'--psm 6 --oem 1'
text = pytesseract.image_to_string(Image.open('image.png'), config=custom_config)
```

### OCR + Spellcheck

```bash
# Extract text and fix typos with aspell
tesseract image.png stdout -l eng | aspell -a

# Or hunspell
tesseract image.png stdout -l eng | hunspell -l
```

---

## Troubleshooting

### Problem: Low Accuracy

**Solutions:**
1. Preprocess image (resize, sharpen, denoise)
2. Try different PSM modes (`--psm 4`, `--psm 6`, `--psm 11`)
3. Verify correct language (`-l eng`, `-l hun`)
4. Check image DPI (300+ DPI recommended)
5. Binarize image (convert to black/white)

```bash
# Debug: Save preprocessed image and test
convert input.jpg -resize 300% -sharpen 0x1 -threshold 50% debug.png
tesseract debug.png output --psm 6 -l eng
```

### Problem: Missing Text

**Causes:**
- Wrong PSM mode (use `--psm 11` for sparse text)
- Language not installed (`tesseract --list-langs`)
- Text too small (resize image to 200-300%)

```bash
# Check installed languages
tesseract --list-langs

# Install missing language
sudo dnf install tesseract-langpack-<lang>
```

### Problem: Garbled Output

**Causes:**
- Wrong character encoding
- Mixed languages (use `-l eng+hun`)
- Special characters not in language model

```bash
# Try combined languages
tesseract image.png output -l eng+hun+deu

# Check encoding
file output.txt
iconv -f ISO-8859-1 -t UTF-8 output.txt -o fixed.txt
```

### Problem: Slow Processing

**Solutions:**
1. Use legacy engine (`--oem 0`)
2. Reduce image resolution
3. Use faster PSM mode (`--psm 6` vs `--psm 3`)
4. Parallel processing (GNU parallel)

```bash
# Faster processing (lower accuracy)
tesseract image.png output --oem 0 --psm 6
```

### Problem: PDF Input Not Working

**Solution:** Convert PDF to images first

```bash
# Install poppler-utils
sudo dnf install poppler-utils

# Convert PDF to images
pdftoppm input.pdf page -png -r 300 # 300 DPI

# OCR each page
for img in page-*.png; do
tesseract "$img" "${img%.png}" -l eng pdf
done
```

---

## Performance Tips

### Optimal Image Settings
- **DPI:** 300 DPI (minimum 150, maximum 600)
- **Format:** PNG or TIFF (lossless)
- **Color:** Grayscale (color not needed for OCR)
- **Size:** Resize to 2000-3000px width for normal text

### Speed vs Accuracy Trade-offs

```bash
# Fastest (lowest accuracy)
tesseract img.png out --oem 0 --psm 6

# Balanced (good for production)
tesseract img.png out --oem 1 --psm 3 # Default

# Most accurate (slowest)
tesseract img.png out --oem 2 --psm 1 -c tessedit_char_min_confidence=90
```

### Batch Processing Best Practices

```bash
# Use tmpfs for temp files (faster I/O)
mkdir -p /tmp/ocr_temp
for img in *.jpg; do
cp "$img" /tmp/ocr_temp/
tesseract /tmp/ocr_temp/"$img" output/"${img%.jpg}" -l eng
rm /tmp/ocr_temp/"$img"
done
```

---

## Integration Examples

### Shell Script: Automated Invoice Processing

```bash
#!/bin/bash
# process_invoices.sh

INVOICE_DIR="invoices"
OUTPUT_DIR="extracted_data"
LANG="eng"

mkdir -p "$OUTPUT_DIR"

for invoice in "$INVOICE_DIR"/*.pdf; do
base=$(basename "$invoice" .pdf)

# Convert PDF to image
pdftoppm -png -r 300 "$invoice" "$OUTPUT_DIR/$base"

# Preprocess
convert "$OUTPUT_DIR/${base}-1.png" \
-resize 200% \
-colorspace Gray \
-sharpen 0x1 \
"$OUTPUT_DIR/${base}_clean.png"

# OCR with structured output
tesseract "$OUTPUT_DIR/${base}_clean.png" \
"$OUTPUT_DIR/$base" \
-l "$LANG" \
--psm 6 \
tsv txt

# Extract specific fields (example: invoice number)
grep -i "invoice" "$OUTPUT_DIR/$base.txt" | head -1

# Cleanup temp files
rm "$OUTPUT_DIR/${base}-1.png" "$OUTPUT_DIR/${base}_clean.png"
done

echo "Processing complete."
```

### Python: Extract Text with Confidence Scores

```python
#!/usr/bin/env python3
import pytesseract
from PIL import Image
import pandas as pd

def ocr_with_confidence(image_path):
"""Extract text with confidence scores"""
img = Image.open(image_path)

# Get detailed data
data = pytesseract.image_to_data(img, output_type='dict')

# Filter low-confidence words
df = pd.DataFrame(data)
df = df[df['conf'] > 60] # Keep only >60% confidence

# Group by line
lines = df.groupby(['block_num', 'par_num', 'line_num'])['text'].apply(' '.join)

return '\n'.join(lines)

# Usage
text = ocr_with_confidence('document.png')
print(text)
```

### Bash: Monitor Folder for New Scans

```bash
#!/bin/bash
# watch_scans.sh - Auto-OCR new files with inotifywait

WATCH_DIR="$HOME/scans"
OUTPUT_DIR="$HOME/ocr_results"

mkdir -p "$OUTPUT_DIR"

inotifywait -m -e close_write --format '%f' "$WATCH_DIR" | \
while read filename; do
if [[ "$filename" =~ \.(jpg|png|tif)$ ]]; then
echo "Processing $filename..."
tesseract "$WATCH_DIR/$filename" \
"$OUTPUT_DIR/${filename%.*}" \
-l eng --psm 3 pdf txt
echo "Done: ${filename%.*}.pdf"
fi
done
```

---

## Training Custom Models

For specialized fonts, historical documents, or custom languages.

### Quick Training Overview

```bash
# 1. Generate training images with text
# 2. Create box files (bounding boxes)
# 3. Train Tesseract model

# Generate box file
tesseract training_image.png training_image lstm.train

# (Full training process is complex - see official docs)
```

**Note:** Custom training is advanced. Use pre-trained models when possible.

---

## Alternative Tools

| Tool | Use Case | Comparison |
|------|----------|------------|
| **Google Cloud Vision API** | High accuracy, handwriting | Paid, cloud-based, more accurate |
| **Amazon Textract** | Forms, tables, AWS integration | Paid, excellent for structured docs |
| **EasyOCR** | 80+ languages, Python-native | Easier API, GPU support |
| **PaddleOCR** | Asian languages, lightweight | Fast, good for Chinese/Japanese |
| **ABBYY FineReader** | Commercial, high accuracy | Expensive, best-in-class accuracy |

---

## Useful Resources

- **Official Documentation:** [https://tesseract-ocr.github.io/](https://tesseract-ocr.github.io/)
- **GitHub:** [https://github.com/tesseract-ocr/tesseract](https://github.com/tesseract-ocr/tesseract)
- **Language Data:** [https://github.com/tesseract-ocr/tessdata](https://github.com/tesseract-ocr/tessdata)
- **Training Tutorial:** [https://tesseract-ocr.github.io/tessdoc/Training-Tesseract.html](https://tesseract-ocr.github.io/tessdoc/Training-Tesseract.html)

---

## Quick Command Reference

```bash
# Basic OCR
tesseract image.png output

# Specify language
tesseract image.png output -l hun

# Multiple languages
tesseract image.png output -l eng+hun

# Output formats
tesseract image.png output pdf
tesseract image.png output hocr
tesseract image.png output tsv

# Page segmentation mode
tesseract image.png output --psm 6

# OCR engine mode
tesseract image.png output --oem 1

# Combined options
tesseract image.png output -l eng --psm 6 --oem 1 pdf txt

# Config options
tesseract image.png output -c tessedit_char_whitelist=0123456789

# List languages
tesseract --list-langs

# Version info
tesseract --version
```

---

## License

Tesseract is released under the **Apache License 2.0**.

---

**Last Updated:** 2026-04-13 
**Author:** Miklos Greczi 
**Repository:** docs-repository
