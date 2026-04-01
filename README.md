# Land-Cover-Classification
This repo is about using DL-based models to predict the class of each pixel from multispectral satellite images. 

### 🌍 Study Area

The data used in this project focuses on **Kerinci Regency**, located in **Jambi Province, Sumatra, Indonesia**. The region includes ecologically significant areas such as:

- Tropical forests
- Agricultural lands
- Urban expansion zones

Satellite data were clipped to a region of interest (ROI) that covers parts of the **Kerinci Seblat National Park** and surrounding areas, known for its rich biodiversity and dynamic land-cover changes. The images were exported from a Google Earth Engine (GEE).

<img width="723" height="688" alt="image" src="https://github.com/user-attachments/assets/f601fae4-5e5f-47a4-abe7-484fdd962c3e" />

## 📂 Data Sources

This project uses satellite imagery and labeled land cover data to train a U-Net model for pixel-wise classification. The data is composed of:

- **Land Cover Labels (Ground Truth)**  
  - File: `LC_Image_v1.tif`  
  - Description: A raster image in which each pixel value represents a specific land cover class (e.g., forest, urban, water).  
  - The ground truth labels are displayed in the figure below.
  <img width="1150" height="818" alt="image" src="https://github.com/user-attachments/assets/b66e3b71-4270-4bb2-8080-5f1417afa058" />

- **Land Cover Metadata**  
  - File: `lc.json`  
  - Description: A JSON file defining land cover class IDs, names, and color palettes.  
  - Purpose: Used to normalize and visualize land cover classes.

- **Landsat Satellite Imagery**  
  - File: `Landsat_Kerinci_2023_v1.tif`  
  - Description: Multi-band Landsat imagery (e.g., bands 4–7) used as input features for classification.  
  - Preprocessing: Pixel values are scaled to surface reflectance.

- **(Optional) ALOS PALSAR Radar Imagery**  
  - File: `Palsar_Kerinci_2023_v1.tif`  
  - Description: Radar backscatter data that can be integrated with optical imagery to enhance classification performance (not yet fully integrated in the current pipeline).

---

## 🧪 Preprocessing Pipeline

This section describes the preprocessing steps used to prepare satellite imagery and land cover data for training a U-Net model for pixel-wise land cover classification.

### 1. 🔹 Load and Normalize Land Cover Classes
- Land cover metadata is read from `lc.json`, containing:
  - Class IDs (`values`)
  - Class names (`label`)
  - Hex color codes (`palette`)
- Each land cover class is assigned a **normalized integer value** starting from 1 to ensure compatibility with deep learning models.
- Color palettes and labels are mapped for later use in visualization and interpretation.

### 2. 🗺️ Load and Remap Ground Truth Label Image
- The land cover (label) image is loaded from a `.tif` file.
- Each pixel’s original class ID is remapped to its corresponding **normalized class value** using the previously defined mapping.
- A custom colormap is applied to visualize the labeled image.

### 3. 🛰️ Load and Normalize Satellite Imagery
- Multispectral input data (e.g., Landsat) is loaded from a `.tif` file.
- Bands are scaled to reflectance range (0–1) by dividing by `1e4`.
- A **false-color composite** (e.g., NIR–SWIR1–SWIR2) is generated for visual inspection using `rescale_intensity`.

### 4. 📦 Patch Sampling for Supervised Learning
- Image patches of size **128 × 128 pixels** are extracted from both the satellite image and the label image.
- For each unique land cover class:
  - 20 center points are randomly sampled from pixels belonging to that class.
  - Border points are filtered out to avoid incomplete patches.
  - For each center point, a bounding box is calculated to extract a patch.
- The resulting patch coordinates are stored in a dictionary for later batch extraction and model training.

### 5. 📊 Optional Interactive Visualization (Plotly)
- An interactive map of the remapped land cover image is rendered using Plotly:
  - Hover tooltips show land cover class names.
  - Colors match the provided palette.
  - Supports zooming, panning, and exporting.

---

### ✅ Output
- A set of **clean, labeled patches** ready for training a U-Net model.
- Normalized inputs and labels.
- Tools for both visual verification and further analysis.

