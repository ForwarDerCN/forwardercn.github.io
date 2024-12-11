# Gabor Filter and Feature Extraction

<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$'], ["\\(","\\)"]],["\(", "\)"],
            displayMath: [["$$","$$"], ["\\[","\\]"]],["\[", "\]"]
            }
        });
    </script>
</head>

## **Introduction to Gabor Filters**

A **Gabor filter** is a linear filter commonly used in image processing, especially for tasks like texture analysis, edge detection, and feature extraction. It is based on the work of Dennis Gabor and combines spatial and frequency domain information. Below is a detailed explanation of Gabor filters:

---

## **Definition of a Gabor Filter**

A Gabor filter is defined as the product of a sinusoidal wave and a Gaussian envelope:

\\[
g(x, y) = \exp\left(-\frac{x'^2 + \gamma^2 y'^2}{2\sigma^2}\right) \cdot \cos\left(2\pi f x' + \phi\right)
\\]

- **Sinusoidal Component**: Provides frequency selectivity, characterized by frequency \( f \) and phase offset \( \phi \).
- **Gaussian Envelope**: Provides spatial weighting, characterized by standard deviation \( \sigma \) and aspect ratio \( \gamma \).

Here, \\((x', y')\\) are rotated coordinates:
\\[
x' = x \cos\theta + y \sin\theta, \quad y' = -x \sin\theta + y \cos\theta
\\]
where \\( \theta \\) is the orientation of the filter.

---

## **Parameters of a Gabor Filter**
1. **Central Frequency \\( f \\)**: Controls the frequency component of the filter response.
2. **Orientation \\( \theta \\)**: Specifies the directional selectivity of the filter.
3. **Scale \\( \sigma \\)**: Determines the spatial extent of the Gaussian envelope.
4. **Aspect Ratio \\( \gamma \\)**: Controls the elliptical shape of the filter.
5. **Phase Offset \\( \phi \\)**: Adjusts the phase of the sinusoidal component.

---

## **Characteristics of Gabor Filters**
- **Orientation Selectivity**: Sensitive to edges and patterns in specific directions.
- **Frequency Selectivity**: Captures features at specific spatial frequencies.
- **Localization**: Combines spatial and frequency domain properties, making it effective for localized feature extraction.

---

## **Applications**
1. **Texture Analysis**: Extracts texture features from images.
2. **Edge Detection**: Detects edges in specific orientations.
3. **Biological Vision Modeling**: Simulates the response of simple cells in the human visual cortex.
4. **Feature Extraction**: Used in computer vision tasks to capture frequency and orientation-based features.

---

## **Advantages**
- Good localization in both spatial and frequency domains.
- Effective for detecting directionality and scale changes in images.

## **Disadvantages**
- Parameter tuning can be complex and application-dependent.
- Computational cost is relatively high.

---

## **Implementation**

In practice, Gabor filters are often discretized for digital image processing. Libraries like OpenCV and MATLAB provide easy-to-use implementations. Below is an example in Python:

```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

# Create a Gabor kernel
def create_gabor_kernel(ksize, sigma, theta, lambd, gamma, psi):
    return cv2.getGaborKernel((ksize, ksize), sigma, theta, lambd, gamma, psi, ktype=cv2.CV_32F)

# Apply the Gabor filter
image = cv2.imread('example.jpg', cv2.IMREAD_GRAYSCALE)
gabor_kernel = create_gabor_kernel(21, 8, np.pi/4, 10, 0.5, 0)
filtered_image = cv2.filter2D(image, cv2.CV_8UC3, gabor_kernel)

# Display the results
plt.subplot(1, 2, 1), plt.imshow(image, cmap='gray'), plt.title('Original Image')
plt.subplot(1, 2, 2), plt.imshow(filtered_image, cmap='gray'), plt.title('Filtered Image')
plt.show()
```

## **Explanation of Parameters in Code**
- `ksize`: Kernel size.
- `sigma`: Standard deviation of the Gaussian envelope.
- `theta`: Orientation of the filter.
- `lambd`: Wavelength of the sinusoidal wave.
- `gamma`: Aspect ratio.
- `psi`: Phase offset.

By varying these parameters, the Gabor filter can extract features of different orientations and frequencies from the image.