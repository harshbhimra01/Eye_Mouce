# Eye Controlled Mouse

This project implements an eye-controlled mouse using Python, OpenCV, MediaPipe, and PyAutoGUI. The application uses facial landmarks to track eye movements and simulate mouse control on your screen, allowing you to move the cursor and perform clicks with just your eye movements.

## Features

- **Eye Tracking**: The program uses a webcam to track the user's eyes and moves the cursor based on the position of the eyes.
- **Click Simulation**: By blinking or closing the eyes for a short duration, the program simulates a mouse click.
- **Real-Time Processing**: Utilizes the MediaPipe library for real-time face and eye landmark detection.

## Requirements

- Python 3.x
- OpenCV
- MediaPipe
- PyAutoGUI

### Installation

You can install the required packages using pip:

```bash
pip install opencv-python mediapipe pyautogui
```

## How It Works

1. **Webcam Initialization**: The program captures video frames using your computer's webcam.

2. **Face Landmark Detection**: It processes each frame to detect facial landmarks, focusing on the eyes using MediaPipe's FaceMesh.

3. **Eye Position Mapping**: The detected eye position is mapped to the screen coordinates, allowing you to move the mouse cursor by moving your eyes.

4. **Click Detection**: A click event is triggered when the eye-blink or eye-closing gesture is detected.

### Code Overview

```python
import cv2
import mediapipe
import pyautogui

# Initialize webcam
cam = cv2.VideoCapture(0)

# Initialize MediaPipe FaceMesh for facial landmark detection
face_mesh = mediapipe.solutions.face_mesh.FaceMesh(refine_landmarks=True)

# Get screen width and height
screen_w, screen_h = pyautogui.size()

while True:
    _, frame = cam.read()  # Read frame from the webcam
    frame = cv2.flip(frame, 1)  # Flip frame horizontally
    rgb_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)  # Convert to RGB
    output = face_mesh.process(rgb_frame)  # Process frame to get facial landmarks
    landmark_points = output.multi_face_landmarks
    frame_h, frame_w, _ = frame.shape

    if landmark_points:
        landmarks = landmark_points[0].landmark

        # Iterate over specific landmarks for eye tracking
        for id, landmark in enumerate(landmarks[474:478]):
            x = int(landmark.x * frame_w)
            y = int(landmark.y * frame_h)
            cv2.circle(frame, (x, y), 3, (0, 255, 0))  # Draw circle on eye landmarks

            if id == 1:
                screen_x = screen_w / frame_w * x
                screen_y = screen_h / frame_h * y
                pyautogui.moveTo(screen_x, screen_y)  # Move cursor to eye position

        # Detect eye-blink for click event
        left = [landmarks[145], landmarks[159]]
        for landmark in left:
            x = int(landmark.x * frame_w)
            y = int(landmark.y * frame_h)
            cv2.circle(frame, (x, y), 3, (0, 255, 255))  # Draw circle on blink landmarks

        if (left[0].y - left[1].y) < 0.010:
            pyautogui.click()  # Trigger mouse click
            pyautogui.sleep(1)

    cv2.imshow('Eye Controlled Mouse', frame)  # Display the frame
    cv2.waitKey(1)
```

### How to Run

1. **Connect a Webcam**: Ensure your webcam is connected and functional.

2. **Run the Script**: Execute the Python script in your terminal or IDE.

3. **Control the Mouse**: Move your eyes to control the mouse cursor. Blink to simulate a mouse click.

### Notes

- **Calibration**: Adjust the sensitivity and calibration based on your screen size and eye movement for optimal performance.

- **Performance**: The application may require a powerful CPU for real-time processing. Ensure your system meets the necessary hardware requirements.

- **Safety**: Frequent usage may cause eye strain. Take regular breaks to avoid discomfort.

## Troubleshooting

- **Webcam Not Detected**: Ensure that the correct camera index is set in `cv2.VideoCapture(0)`.

- **Performance Issues**: Reduce the resolution of the webcam feed if you experience lag.

- **Cursor Not Moving**: Ensure proper lighting conditions for better eye-tracking accuracy.

## Future Improvements

- **Customization**: Add options to customize sensitivity, calibration, and gesture settings.

- **Eye Blink Duration**: Implement a more sophisticated algorithm to differentiate between intentional blinks and normal eye closures.

- **Additional Gestures**: Incorporate more eye gestures for other mouse actions like right-clicking or scrolling.

## Contributing

Contributions are welcome! Please feel free to submit a pull request or open an issue for any bugs or feature requests.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.
