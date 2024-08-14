> [!NOTE]
>This project uses computer vision to implement a Rock-Paper-Scissors game where two players can play using hand gestures detected by a webcam. The code uses Python's OpenCV and MediaPipe libraries to detect hand landmarks and determine the hand gesture for each player.

OUTPUT

https://github.com/user-attachments/assets/b2d2ee80-0230-4017-ae68-77a88ed48176

-------------------------------------------------------------------------------------
## Requirements

- Python 3.x
- OpenCV
- MediaPipe

-------------------------------------------------------------------------------------

Install the necessary packages using the following commands:

```bash
pip install opencv-python mediapipe
```

-------------------------------------------------------------------------------------

How It Works

-------------------------------------------------------------------------------------

1. Import Libraries

```python
Copy code
import cv2 as cv
import mediapipe as mp
```

- mp_drawing = mp.solutions.drawing_utils
- mp_drawing_styles = mp.solutions.drawing_styles
- mp_hands = mp.solutions.hands
- cv2: Used for image processing and video capture.
- mediapipe: Provides hand landmark detection and drawing utilities.
- mp_drawing: Utility to draw hand landmarks on the image.
- mp_hands: A MediaPipe module used for hand detection.

-------------------------------------------------------------------------------------


2. Define the Function to Identify Hand Gesture

```python
def getHandMove(hand_landmarks):
    landmarks = hand_landmarks.landmark
    if all([landmarks[i].y < landmarks[i+3].y for i in range(9, 20, 4)]): return "rock"
    elif landmarks[13].y < landmarks[16].y and landmarks[17].y < landmarks[20].y: return "scissors"
    else: return "paper"
```

- getHandMove(): This function takes the detected hand landmarks and determines whether the hand gesture is "rock," "paper," or "scissors" based on the relative positions of certain landmarks.

-------------------------------------------------------------------------------------


3. Initialize Video Capture and Game Variables

```python
vid = cv.VideoCapture(0)
clock = 0
p1_move = p2_move = None
gameText = ""
success = True
```
- cv.VideoCapture(0): Captures video from the default webcam.
- clock: A variable used to manage the timing of the game.
- p1_move, p2_move: Variables to store the moves of Player 1 and Player 2.
- gameText: A string to display messages on the screen.
- success: A boolean flag to indicate whether the hand detection was successful.

-------------------------------------------------------------------------------------

4. Start the Game Loop with Hand Detection

```python
with mp_hands.Hands(model_complexity=0,
                    min_detection_confidence=0.5,
                    min_tracking_confidence=0.5) as hands:
    while True:
        ret, frame = vid.read()
        if not ret or frame is None: break
        frame = cv.cvtColor(frame, cv.COLOR_BGR2RGB)

        results = hands.process(frame)

        frame = cv.cvtColor(frame, cv.COLOR_RGB2BGR)
        if results.multi_hand_landmarks:
            for hand_landmarks in results.multi_hand_landmarks:
                mp_drawing.draw_landmarks(frame,
                                          hand_landmarks,
                                          mp_hands.HAND_CONNECTIONS,
                                          mp_drawing_styles.get_default_hand_landmarks_style(),
                                          mp_drawing_styles.get_default_hand_connections_style())
 ```                                         
- with mp_hands.Hands(...) as hands: Initializes the hand detection model.
- vid.read(): Captures a frame from the webcam.
- cv.cvtColor(): Converts the image from BGR to RGB format and vice versa.
- hands.process(frame): Processes the frame to detect hand landmarks.
- mp_drawing.draw_landmarks(): Draws the detected hand landmarks on the frame.

-------------------------------------------------------------------------------------

5. Display Game Status and Countdown
   
```python
        if 0 <= clock < 20:
            success = True
            gameText = "Ready?"
        elif clock < 30: gameText = "3..."
        elif clock < 40: gameText = "2..."
        elif clock < 50: gameText = "1..."
        elif clock < 60:
            gameText = "GO!"
```
clock: Manages the countdown and game states. Displays "Ready?" initially, followed by a countdown "3...", "2...", "1...", and finally "GO!" when players are expected to show their gestures.

-------------------------------------------------------------------------------------

6. Capture Players' Moves

```python
        elif clock == 60:
            hls = results.multi_hand_landmarks
            if hls and len(hls) == 2:
                p1_move = getHandMove(hls[0])
                p2_move = getHandMove(hls[1])
            else:
                success = False
```
- clock == 60: At this moment, the program checks the players' gestures.
- hls: Stores the detected hand landmarks.
- getHandMove(hls[0]): Determines Player 1's move.
- getHandMove(hls[1]): Determines Player 2's move.
- success = False: If there aren't exactly two hands detected, the round is marked as unsuccessful.

-------------------------------------------------------------------------------------

7. Determine the Winner

```python
        elif clock < 100:
            if success:
                gameText = f"Player 1 played {p1_move}. Player 2 played {p2_move}."
                if p1_move == p2_move: gameText = f"{gameText} Game is tied."
                elif p1_move == "paper" and p2_move == "rock": gameText = f"{gameText} Player 1 wins."
                elif p1_move == "rock" and p2_move == "scissors": gameText = f"{gameText} Player 1 wins."
                elif p1_move == "scissors" and p2_move == "paper": gameText = f"{gameText} Player 1 wins."
                else: gameText = f"{gameText} Player 2 wins."
            else:
                gameText = "Didn't play properly!"
```
- gameText: Displays the result of the round based on the players' moves.

-------------------------------------------------------------------------------------

8. Display Clock and Game Text on the Frame

```python
        cv.putText(frame, f"Clock: {clock}", (50, 50), cv.FONT_HERSHEY_PLAIN, 2, (0,255,255), 2, cv.LINE_AA)
        cv.putText(frame, gameText, (50, 80), cv.FONT_HERSHEY_PLAIN, 2, (0,255,255), 2, cv.LINE_AA)
        clock = (clock + 1) % 100
```
- cv.putText(): Displays the clock and game text on the frame.
- clock = (clock + 1) % 100: Updates the clock for the next loop iteration.

-------------------------------------------------------------------------------------

9. Show the Result and Handle Exiting the Game
```python
        # Show result
        cv.imshow('frame', frame)

        if cv.waitKey(1) & 0xFF == ord('q'): break

vid.release()
cv.destroyAllWindows()
```

- cv.imshow('frame', frame): Displays the frame with the game status.
- cv.waitKey(1) & 0xFF == ord('q'): Checks if the 'q' key is pressed to exit the game.
- vid.release(): Releases the webcam.
- cv.destroyAllWindows(): Closes all OpenCV windows.

-------------------------------------------------------------------------------------

Running the Game
- To run the game, simply execute the script in your Python environment
- Once the game starts, the countdown will begin, and both players should show their hand gestures (rock, paper, or scissors) when the text "GO!" appears on the screen. The game will then determine the winner based on the gestures detected.
- Press 'q' at any time to exit the game.

-------------------------------------------------------------------------------------

> [!NOTE]
> If you are intrested in projects like this "Games made using python and OpenCV (computer vision)" then you should read my other documents-
> Flappy Bird game using OpenCV & Python [Read Document](https://github.com/pranavvss/Flappy-Bird-using-Open-CV)
> Google Dinosaur game using OpenCV (Use your hands to jump your dino) [Read Document](https://github.com/pranavvss/Google-Dinosaur-game-using-Open-CV)

