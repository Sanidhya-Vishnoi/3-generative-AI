# Azure AI Face Analysis

A Python script that uses **Azure AI Face** to detect faces in an image and extract detailed facial attributes — including head pose, occlusion, and accessories — then saves an annotated image with bounding boxes around each detected face.

---

## What It Does

This script sends an image to the Azure Face API and returns detailed information about every face detected. It prints per-face attributes to the console and saves an annotated copy of the image with each face highlighted.

| Feature | Description |
|---|---|
| **Face Detection** | Locates all faces in the image |
| **Head Pose** | Reports yaw, pitch, and roll angles of each face |
| **Occlusion Detection** | Checks if forehead, eyes, or mouth are obscured |
| **Accessories** | Identifies accessories like glasses or masks |
| **Face Annotation** | Saves an image with green bounding boxes around each face |

---

## Prerequisites

- Python 3.7+
- An [Azure AI Face](https://azure.microsoft.com/en-us/products/ai-services/ai-vision) resource (endpoint + key)
- An image file containing faces (defaults to `images/face1.jpg`)

---

## Installation

1. **Install dependencies:**
   ```bash
   pip install python-dotenv azure-ai-vision-face pillow matplotlib
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
python 07-analyze-faces.py

# Use a custom image
python 07-analyze-faces.py images/group.jpg
```

### Example Output

```
3 faces detected.

Face number 1
 - Head Pose (Yaw): -12.5
 - Head Pose (Pitch): 3.2
 - Head Pose (Roll): 1.8
 - Forehead occluded?: False
 - Eye occluded?: False
 - Mouth occluded?: False
 - Accessories:
   - glasses

Face number 2
 - Head Pose (Yaw): 5.1
 - Head Pose (Pitch): -2.4
 - Head Pose (Roll): 0.3
 - Forehead occluded?: False
 - Eye occluded?: False
 - Mouth occluded?: True
 - Accessories:
   - mask

Annotating faces in image...
  Results saved in detected_faces.jpg
```

---

## Output Files

| File | Contents |
|---|---|
| `detected_faces.jpg` | Original image with light green bounding boxes and labels around each detected face |

---

## Understanding Head Pose Values

| Value | Meaning |
|---|---|
| **Yaw** | Left/right rotation (negative = turned left, positive = turned right) |
| **Pitch** | Up/down tilt (negative = looking down, positive = looking up) |
| **Roll** | Side tilt (head tilting toward shoulder) |

---

## How It Differs from Image Analysis (06)

| Feature | Image Analysis (06) | Face Analysis (08) |
|---|---|---|
| Azure service | Azure AI Vision | Azure AI Face |
| People detection | Location only (bounding box) | Detailed attributes per face |
| Attributes returned | None | Head pose, occlusion, accessories |
| Use case | General scene understanding | Identity and facial analysis |

---

## Project Structure

```
.
├── 07-analyze-faces.py   # Main script
├── .env                  # Your Azure credentials (do not commit!)
└── images/
    └── face1.jpg         # Default input image
```

---

## Notes

- Never commit your `.env` file to version control. Add it to `.gitignore`.
- The script sets `return_face_id=False` — face IDs are not returned, so this script cannot be used for face identification or matching.
- The Azure Face API has **responsible AI restrictions** — some attributes (e.g. emotion, age, gender) require approved access and are not used in this script.
- Results are saved to `detected_faces.jpg` in the script's working directory, overwriting any previous run.
