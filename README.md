# Driving Lane Detection

## Lane Detection in Video Frames

This project uses OpenCV to detect and track driving lane markings in video footage. The program processes each frame through a pipeline of perspective transformation, color filtering, edge detection, and Hough Line Transform to identify left and right lane boundaries. Detected lanes are overlaid on the original frame and displayed in real time. A center lane line is calculated when both lanes are visible.

## Features

- **Perspective Transform**: Converts the front-facing camera view to a bird's eye view for more accurate lane detection.
- **Color-Based Filtering**: Uses HSV color space to isolate yellow lane markings.
- **Edge Detection**: Applies Canny edge detection to identify lane boundaries.
- **Line Detection**: Uses Hough Line Transform to extract lane lines from edge-detected frames.
- **Temporal Smoothing**: Retains previous lane positions for up to 10 frames when detection fails, preventing jitter.
- **Center Lane Calculation**: Computes the lane center by averaging the left and right lane positions.
- **Real-Time Processing**: Processes and displays each frame in real time.

## Project Structure

- `main.py`: Main script containing the full lane detection pipeline — video capture, frame processing, lane detection, tracking, and display.
- `lanevid.mp4`: Sample driving video used as input.

## How It Works

1. **Open Video File**: Reads the video file frame by frame.
2. **Perspective Transform**: Warps each frame to a bird's eye view using a 4-point perspective transform.
3. **Color Filtering**: Converts to HSV color space and creates a mask for yellow lane markings (H: 10-40, S: 90-255, V: 90-255).
4. **Edge Detection**: Applies Canny edge detection on both the grayscale and color-masked images.
5. **Line Detection**: Detects line segments using probabilistic Hough Line Transform.
6. **Slope Filtering**: Filters detected lines by slope, keeping only near-vertical lines (|slope| > 100) that represent lane markings.
7. **Lane Separation**: Separates lines into left and right lanes based on their x-position relative to the frame center. Selects the line closest to center on each side.
8. **Temporal Smoothing**: If no valid line is detected for a side, reuses the previous detection for up to 10 frames.
9. **Center Line**: When both lanes are detected, draws a center lane line by averaging the two.
10. **Inverse Transform**: Warps the annotated bird's eye view back to the original perspective.
11. **Display**: Shows the processed frame with lane overlays in an output window.

## Usage

**Prepare the Video File:**

Place your video file in the same directory as `main.py`. Update the `video = cv2.VideoCapture("lanevid.mp4")` line in `main.py` with your video file path if using a different file.

**Run the Program:**

```bash
python main.py
```

**View Output:**

A window named "output" will display each frame with detected lane lines overlaid in yellow.

**Exit:**

Press `q` to stop the program at any time.

## Pipeline Details

### Perspective Transform
Defines source points around the frame center and maps them to a rectangular region, producing a top-down view that makes lane lines appear more vertical and easier to detect.

### Color Detection (HSV)
- **Lower bound**: [10, 90, 90]
- **Upper bound**: [40, 255, 255]
- Targets yellow lane markings. The grayscale image is combined with the color mask using bitwise AND.

### Edge Detection (Canny)
Two passes:
- **Pass 1**: Thresholds 30-200 on the grayscale bird's eye view.
- **Pass 2**: Thresholds 10-20 on the color-masked image.

### Hough Line Transform
```
rho: 1 pixel
theta: pi/180
threshold: 10 votes
maxLineGap: 50 pixels
minLineLength: 5 pixels
```

### Lane Tracking
- Lines are classified as left or right based on position relative to frame center.
- The closest line to center on each side is selected.
- When detection fails, the previous lane position persists for up to 10 frames before being replaced.

## Requirements

- Python 3.x
- OpenCV (`cv2`)
- NumPy

Install dependencies:

```bash
pip install opencv-python numpy
```
