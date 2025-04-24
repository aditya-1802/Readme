# Readme

1. Install Required Libraries
Make sure you have the necessary libraries installed:

pip install opencv-python opencv-python-headless numpy


2. Code for Live Background Removal
Here's the Python code for live background removal using your webcam and replacing the background with a new image:

###################Code START##############################

import cv2
import numpy as np

# Function to change the background in real time
def live_background_removal(background_image_path):
    # Capture video from webcam
    cap = cv2.VideoCapture(0)

    # Load the new background image
    background = cv2.imread(background_image_path)

    while True:
        # Read a frame from the webcam
        ret, frame = cap.read()
        if not ret:
            print("Failed to grab frame.")
            break

        # Resize the background image to match the frame size
        background_resized = cv2.resize(background, (frame.shape[1], frame.shape[0]))

        # Convert the frame to grayscale
        gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)

        # Apply Gaussian blur to the grayscale image to reduce noise
        blurred = cv2.GaussianBlur(gray, (5, 5), 0)

        # Threshold the image to get a binary mask
        _, mask = cv2.threshold(blurred, 120, 255, cv2.THRESH_BINARY)

        # Invert the mask: Foreground = 255 (white), Background = 0 (black)
        mask_inv = cv2.bitwise_not(mask)

        # Create a foreground image by applying the mask to the frame
        foreground = cv2.bitwise_and(frame, frame, mask=mask)

        # Create a background image by applying the inverted mask to the new background
        background_part = cv2.bitwise_and(background_resized, background_resized, mask=mask_inv)

        # Combine the foreground and the background to get the final result
        result = cv2.add(foreground, background_part)

        # Show the original frame and the result with the new background
        cv2.imshow("Original Video", frame)
        cv2.imshow("Background Removed", result)

        # Exit if the 'q' key is pressed
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break

    # Release the webcam and close windows
    cap.release()
    cv2.destroyAllWindows()

background_image_path = r'C:/user/download/img.jpg'  # Set your background image path here
live_background_removal(background_image_path)
###################Code END##############################


# Example usage
background_image = 'new_background.jpg'  # Path to the new background image
live_background_removal(background_image)
Explanation:
Capture Video: We use OpenCV to capture video from the webcam using cv2.VideoCapture(0). The 0 indicates the default webcam.

Load Background: The background image (background_image) is loaded using cv2.imread().

Grayscale and Thresholding:

The video frame is converted to grayscale using cv2.cvtColor().

We apply a Gaussian blur (cv2.GaussianBlur()) to reduce noise in the frame.

Then, we threshold the image to create a binary mask using cv2.threshold(), where pixels above a certain value are considered part of the foreground (object).

Foreground and Background Masking:

The foreground is extracted by applying the mask to the frame (cv2.bitwise_and()).

The background is resized to match the frame's dimensions, and the mask is inverted to extract the background from the new image.

Combine Foreground and Background: We combine the extracted foreground and the new background using cv2.add().

Real-Time Display: The original frame and the result (with the background removed and replaced) are displayed using cv2.imshow().

Exit: Press the 'q' key to stop the video capture and close the windows.

Notes:
Background Image Size: The background image should have a similar size to the webcam frame. The code automatically resizes the background to match the frame's dimensions, but ensure your new background image is big enough and of good quality.

Thresholding: The cv2.threshold() value may need to be adjusted depending on your specific use case, lighting, and background.

Key Press to Exit: Press q to stop the webcam and close the application.
