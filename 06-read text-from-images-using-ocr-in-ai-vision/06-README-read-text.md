# Azure AI OCR — Read Text in Images

A Python script that uses **Azure AI Vision** to extract and read text from images using OCR (Optical Character Recognition), then saves annotated versions of the image highlighting detected lines and individual words.

---

## What It Does

This script reads an image, sends it to Azure AI Vision, and extracts all detected text. It outputs the text to the console with confidence scores per word, and saves two annotated images — one marking each line of text, and one marking each individual word.

| Feature | Description |
|---|---|
| **Text Extraction** | Reads all text found in the image |
| **Line Detection** | Identifies and outlines each line of text |
| **Word Detection** | Identifies and outlines each individual word |
| **Confidence Scores** | Shows how confident the model is for each word |
| **Annotated Output** | Saves two annotated image files highlighting lines and words |

---

## Prerequisites

- Python 3.7+
- An [Azure AI Vision](https://azure.microsoft.com/en-us/products/ai-services/ai-vision) resource (endpoint + key)
- An image file containing text (defaults to `images/Lincoln.jpg`)

---

## Installation

1. **Install dependencies:**
   ```bash
   pip install python-dotenv azure-ai-vision-imageanalysis pillow matplotlib
   ```

2. **Set up your environment variables** in a `.env` file:
   ```env
   AI_SERVICE_ENDPOINT=https://<your-resource>.cognitiveservices.azure.com/
   AI_SERVICE_KEY=your_azure_api_key
   ```

3. **Add your image** to an `images/` folder, or pass a custom path as a command-line argument.
#Note: the environment variable names must match those used in the code, optionally you can store these in Azure Key Vault (AKV) & access  using managed identity.


---

## Usage

```bash
# Use the default image
python 06-read-text.py

# Use a custom image
python 06-read-text.py images/sign.jpg
```

### Example Output

```
Reading text in images/Lincoln.jpg

Text:
 Four score and seven years ago
 our fathers brought forth on this continent

Individual words:
  Four (Confidence: 0.99%)
  score (Confidence: 0.98%)
  and (Confidence: 0.99%)
  seven (Confidence: 0.97%)
  ...

Annotating lines of text in image...
  Results saved in lines.jpg

Annotating individual words in image...
  Results saved in words.jpg
```

---

## Output Files

| File | Contents |
|---|---|
| `lines.jpg` | Original image with cyan polygons drawn around each line of text |
| `words.jpg` | Original image with cyan polygons drawn around each individual word |

---

## How It Differs from Image Analysis (06)

| Feature | Image Analysis (06) | Read Text (07) |
|---|---|---|
| Azure feature used | CAPTION, TAGS, OBJECTS, PEOPLE | READ |
| Purpose | Understand what's in an image | Extract text from an image |
| Output | Descriptions and labels | Raw text + confidence scores |
| Annotation | Bounding boxes (rectangles) | Bounding polygons (rotated) |
| Use case | General scene understanding | OCR / document scanning |

---

## Project Structure

```
.
├── 06-read-text.py   # Main script
├── .env             # Your Azure credentials (do not commit!)
└── images/
    └── Lincoln.jpg  # Default input image
```

---

## Notes

- Never commit your `.env` file to version control. Add it to `.gitignore`.
- The script uses **bounding polygons** (not rectangles) for annotation — this handles rotated or skewed text more accurately.
- Works best with clear, well-lit images. Handwritten or stylized text may have lower confidence scores.
- Only the first text block (`blocks[0]`) is processed — for multi-block documents, the loop can be extended.
