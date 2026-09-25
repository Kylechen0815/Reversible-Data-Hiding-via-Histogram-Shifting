# Reversible Data Hiding via Histogram Shifting

A Python implementation of Reversible Data Hiding (RDH) using the classic Histogram Shifting technique for grayscale images. This approach enables embedding arbitrary binary payloads into an image with minimal visual distortion while guaranteeing 100% lossless recovery of the original cover image upon message extraction.

---

## Overview

Unlike conventional lossy steganography methods (such as standard LSB replacement), Reversible Data Hiding (RDH) ensures that both the embedded secret message and the original host image can be accurately reconstructed without any permanent degradation. This project implements the histogram modification framework:
- Identifies the peak and zero (or minimum) points in the image intensity histogram.
- Shifts intermediate pixel values to create an empty histogram bin adjacent to the peak.
- Modifies peak pixels to embed binary bits.
- Extracts the exact bitstream and reverses pixel shifts to restore the original host image.

---

## Visualizations and Experimental Results

### 1. Histogram Distribution and Bin Shifting
Histogram analysis showing peak frequency detection and bin relocation between peak and zero points.
<img width="1799" height="949" alt="Histogram Analysis and Modification" src="https://github.com/user-attachments/assets/b9e95296-63fb-45e8-ab91-de8cfe2f9e01" />

### 2. Original vs. Stego Image Comparison
Visual fidelity comparison between the uncompressed original image and the stego image carrying hidden data.
<img width="475" height="464" alt="Original vs Embedded Comparison" src="https://github.com/user-attachments/assets/299794b7-7e9d-446a-ad79-ab5deea64262" />

### 3. Absolute Difference Residuals
Pixel-level absolute difference map illustrating the subtle localized modifications introduced by the embedding process.
<img width="1141" height="496" alt="Absolute Difference Map" src="https://github.com/user-attachments/assets/953ea742-6f75-46f6-bbf5-cfeac0ad2b19" />

### 4. Lossless Reconstruction
Verified reconstruction showing perfect restoration of cover pixel values with high Peak Signal-to-Noise Ratio (PSNR).
<img width="558" height="468" alt="Restored Cover Image" src="https://github.com/user-attachments/assets/14943088-7bd4-462c-919e-97890e3cff20" />

---

## Algorithmic Workflow

### 1. Peak and Zero Point Determination
1. Computes the 256-bin grayscale histogram $H(x)$ of the input image.
2. Finds the peak point $P = \arg\max H(x)$ that yields the maximum embedding capacity.
3. Finds the nearest zero point $Z$ where $H(Z) = 0$ (or the minimum point if no true zero exists).

### 2. Data Embedding (Histogram Shifting)
1. Converts the secret text string into an 8-bit binary bitstream.
2. For all pixels with intensities lying strictly between $P$ and $Z$, shifts their values by $+1$ (assuming $P < Z$) to vacate the bin at $P + 1$:
   $$I'(x, y) = I(x, y) + 1, \quad \text{for } P < I(x, y) < Z$$
3. Sequentially scans pixels with intensity equal to $P$:
   - If the secret bit is `1`, increments the pixel value to $P + 1$.
   - If the secret bit is `0`, retains the pixel value as $P$.

### 3. Data Extraction and Reversible Image Restoration
1. Traverses the stego image to read pixels valued at $P$ and $P + 1$:
   - Pixel $= P \implies$ extracted bit is `0`.
   - Pixel $= P + 1 \implies$ extracted bit is `1`.
2. Groups extracted bits into 8-bit ASCII characters to recover the original string.
3. Reverts pixel intensities back to their exact original values:
   - Sets pixels with value $P + 1$ back to $P$.
   - Decrements pixels in range $(P, Z]$ by 1.
4. Generates a bit-identical reproduction of the original input image.

### 4. Quality Evaluation Metrics
The pipeline computes:
- **Mean Squared Error (MSE)**:
  $$MSE = \frac{1}{M \times N} \sum_{i=0}^{M-1} \sum_{j=0}^{N-1} [I(i, j) - I'(i, j)]^2$$
- **Peak Signal-to-Noise Ratio (PSNR)**:
  $$PSNR = 10 \cdot \log_{10}\left(\frac{255^2}{MSE}\right) \text{ dB}$$

---

## Tech Stack and Dependencies

- **Language**: Python 3.8+
- **Libraries**:
  - `numpy` (Array operations and histogram computations)
  - `opencv-python` (Image I/O and absolute difference calculations)
  - `matplotlib` (Plotting histograms, difference maps, and multi-panel comparisons)

---

## Installation and Execution

### 1. Installation
Clone the repository and install dependencies:
```bash
git clone https://github.com/Kylechen0815/picture_merging.git
cd picture_merging
pip install numpy opencv-python matplotlib
