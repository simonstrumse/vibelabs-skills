---
name: transparency-generation
description: Generate images with transparent backgrounds using difference matting technique. Use when creating assets with transparency, extracting alpha channels, batch generating transparent images via Vertex AI, or working with PNG transparency.
allowed-tools: Read, Write, Bash, Glob, Grep
---

# Transparency Generation via Difference Matting

Generate AI images with high-quality alpha channels (transparency) using the **difference matting technique**. This method captures semi-transparent elements (hair, glass, smoke) that simple background removal cannot.

## When to Use

- Product photography with transparent backgrounds
- Logo/icon generation requiring clean edges
- Character art with fine details (hair, fur)
- Overlay graphics for compositing
- Any case requiring true alpha channel with partial transparency

## When NOT to Use

- Objects that are pure black or pure white (fundamental limitation)
- Quick iterations where transparency isn't critical
- When native transparency tools are available (SD + Layer Diffusion)

## The Technique

### Mathematical Foundation

Given white background image (W) and black background image (B):
- **Alpha extraction**: `A = 1 - (W - B)`
- **Color reconstruction**: `C = B / A` (where A > 0)

This captures partial transparency that single-background methods miss.

## Workflow

### Step 1: Generate White Background Image

```python
prompt = "YOUR_PROMPT_HERE"
seed = 12345  # CRITICAL: Use same seed for both

white_config = {
    "prompt": f"{prompt}, white background, studio lighting",
    "seed": seed,
    "sampleCount": 1,
    "addWatermark": False,  # Required for determinism
    "aspectRatio": "1:1",
    "model": "gemini-3-pro-image-preview"  # Lock exact version
}
```

### Step 2: Generate Black Background Image

```python
black_config = {
    "prompt": f"{prompt}, black background, studio lighting",
    "seed": seed,  # SAME seed
    "sampleCount": 1,
    "addWatermark": False,
    "aspectRatio": "1:1",
    "model": "gemini-3-pro-image-preview"  # SAME model version
}
```

### Step 3: Validate Image Similarity

```python
from skimage.metrics import structural_similarity as ssim
import cv2

def validate_similarity(white_path, black_path, threshold=0.85):
    """Ensure images are same scene (seed worked correctly)"""
    white = cv2.imread(white_path, cv2.IMREAD_GRAYSCALE)
    black = cv2.imread(black_path, cv2.IMREAD_GRAYSCALE)

    score = ssim(white, black)

    if score < threshold:
        raise ValueError(
            f"Images too different (SSIM={score:.3f}). "
            f"Seed may not be deterministic. Regenerate with locked parameters."
        )

    return score
```

### Step 4: Extract Alpha Channel

```python
import numpy as np
import cv2

def extract_alpha_difference_matting(white_path, black_path,
                                     blur_kernel=3, threshold=0.01):
    """
    Extract alpha using difference matting.

    Args:
        white_path: Path to white background image
        black_path: Path to black background image
        blur_kernel: Median blur kernel size (reduces noise)
        threshold: Minimum alpha value (removes artifacts)

    Returns:
        rgba_image: RGBA image with extracted alpha
        alpha_channel: Alpha channel for inspection
    """

    # Load as float32 for precision
    white = cv2.imread(white_path, cv2.IMREAD_COLOR).astype(np.float32) / 255.0
    black = cv2.imread(black_path, cv2.IMREAD_COLOR).astype(np.float32) / 255.0

    # Validate same dimensions
    if white.shape != black.shape:
        raise ValueError(f"Size mismatch: {white.shape} vs {black.shape}")

    # Extract alpha: A = 1 - (W - B)
    # Average across color channels for robustness
    diff = white - black
    alpha = 1.0 - np.mean(diff, axis=2)

    # Clamp to valid range [0, 1]
    alpha = np.clip(alpha, 0.0, 1.0)

    # Remove noise below threshold
    alpha[alpha < threshold] = 0.0

    # Smooth alpha channel (reduces artifacts)
    if blur_kernel > 0:
        alpha_uint8 = (alpha * 255).astype(np.uint8)
        alpha_smooth = cv2.medianBlur(alpha_uint8, blur_kernel)
        alpha = alpha_smooth.astype(np.float32) / 255.0

    # Reconstruct original color: C = B / A
    # Avoid division by zero
    color = np.zeros_like(black)
    mask = alpha > 0.01

    for c in range(3):
        color[:,:,c] = np.where(
            mask,
            black[:,:,c] / np.maximum(alpha, 0.01),
            0
        )

    color = np.clip(color, 0.0, 1.0)

    # Combine into RGBA
    rgba = np.dstack((color, alpha))
    rgba_uint8 = (rgba * 255).astype(np.uint8)
    alpha_uint8 = (alpha * 255).astype(np.uint8)

    return rgba_uint8, alpha_uint8
```

### Step 5: Save Results

```python
from PIL import Image

# Save with PIL to ensure proper PNG alpha
rgba_pil = Image.fromarray(rgba_image, mode='RGBA')
rgba_pil.save("output_transparent.png", "PNG")

# Save alpha channel for inspection
cv2.imwrite("output_alpha.png", alpha_channel)
```

## Best Practices

### Prompt Engineering

- **Add background explicitly**: "white background" or "black background"
- **Add lighting**: "studio lighting" for even illumination
- **Avoid shadows**: "no shadows" or "floating object"
- **Subject distance**: "product photography" style keeps subject centered

### Seed Determinism (CRITICAL)

To ensure white and black images are identical:
- Lock exact model version (not "latest")
- Set `addWatermark: false` (watermarks break determinism)
- Use identical seed, sampleCount, aspectRatio, guidanceScale
- Generate sequentially (avoid model version drift)
- Validate with SSIM before processing

### Edge Cases to Handle

1. **Pure black/white objects**: Add rim lighting in prompt
2. **Colored shadows**: Increase subject-background distance
3. **Noise in alpha**: Increase `blur_kernel` parameter
4. **Failed similarity check**: Regenerate with stricter parameters

## Quality Validation Checklist

Before accepting results:
- [ ] SSIM similarity score > 0.85
- [ ] Alpha channel is grayscale (no color)
- [ ] Alpha values in valid range [0, 255]
- [ ] Edges are smooth (no jagged pixels)
- [ ] No color fringing on edges
- [ ] Semi-transparent areas look correct (hair, glass)
- [ ] Background is fully transparent (alpha = 0)

## Troubleshooting

| Problem | Cause | Solution |
|---------|-------|----------|
| Images look completely different | Seed not deterministic | Lock all parameters, set `addWatermark: false` |
| Noisy alpha channel | Pixel-level variation | Increase `blur_kernel` to 5 or 7 |
| Color fringing on edges | Background not pure white/black | Verify background RGB values |
| Black/white objects disappear | Fundamental limitation | Add rim lighting or colored backgrounds |
| Similarity check fails | Model version changed | Lock model to exact version string |

## Vertex AI Specific Notes

- Gemini models **do not natively support transparency**
- Requesting "transparent background" generates fake checkerboard
- Input PNGs with alpha are converted to RGB
- **This workflow is the best workaround**

## Technical Notes

### Why This Works Better Than Background Removal

- **Captures partial transparency**: Hair, glass, smoke, etc.
- **Mathematically sound**: Solves the matting equation exactly
- **No AI guessing**: Pure algorithmic extraction
- **High quality edges**: Better than segmentation models

### Limitations

- Requires 2x generation cost (white + black)
- Cannot handle pure black or pure white objects
- Requires deterministic seed (not all models support)
- Subject must differ from background color
