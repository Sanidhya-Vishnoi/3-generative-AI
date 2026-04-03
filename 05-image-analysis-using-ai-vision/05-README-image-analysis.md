# Azure AI Image Analysis

A Python script that uses **Azure AI Vision** to analyze an image and extract rich visual information — including captions, tags, detected objects, and people — then saves annotated versions of the image highlighting what was found.

---

## What It Does

This script sends an image to Azure AI Vision and runs a multi-feature analysis in a single API call. It then draws bounding boxes around detected objects and people, saving the annotated results as new image files.

| Feature | Description |
|---|---|
| **Caption** | Generates a single natural language description of the image |
| **Dense Captions** | Generates multiple captions for different regions of the image |
| **Tags** | Lists descriptive keywords detected in the image |
| **Object Detection** | Identifies and locates specific objects with bounding boxes |
| **People Detection** | Identifies and locates people with bounding boxes |

---

## Prerequisites

- Python 3.7+
- An [Azure AI Vision](https://azure.microsoft.com/en-us/products/ai-services/ai-vision) resource (endpoint + key)
- An image file to analyze (defaults to `images/street.jpg`)

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
#Note: the environment variable names must match those used in the code, optionally you can store these in Azure Key Vault (AKV) & access  using managed identity.

3. **Add your image** to an `images/` folder, or pass a custom path as a command-line argument.

---

## Usage

```bash
# Use the default image
python 05-image-analysis.py

# Use a custom image
python 05-image-analysis.py images/myimage.jpg
```

### Example Output

```
Analyzing images/street.jpg

Caption:
 Caption: 'a busy street with cars and pedestrians' (confidence: 92.45%)

Dense Captions:
 Caption: 'a red car parked on the side of the road' (confidence: 88.12%)
 Caption: 'a person walking on the pavement' (confidence: 85.67%)

Tags:
 Tag: 'outdoor' (confidence: 99.10%)
 Tag: 'road' (confidence: 97.34%)
 Tag: 'vehicle' (confidence: 94.20%)

Objects in image:
 car (confidence: 91.00%)
 person (confidence: 87.50%)
  Results saved in objects.jpg

People in image:
 BoundingBox(x=120, y=45, width=80, height=200) (confidence: 87.50%)
  Results saved in people.jpg
```

---

## Output Files

| File | Contents |
|---|---|
| `objects.jpg` | Original image with cyan bounding boxes around detected objects |
| `people.jpg` | Original image with cyan bounding boxes around detected people |

---

## Project Structure

```
.
├── 05-image-analysis.py   # Main script
├── .env                   # Your Azure credentials (do not commit!)
└── images/
    └── street.jpg         # Default input image
```

---

## Notes

- Never commit your `.env` file to version control. Add it to `.gitignore`.
- People are only annotated if their detection confidence exceeds **20%** (`confidence > 0.2`).
- The script accepts any common image format supported by Pillow (JPEG, PNG, BMP, etc.).
- Dense captions may return many results — the number depends on the complexity of the image.
