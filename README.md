Here's a README file for your AI Workout Assistant project:

# AI Workout Assistant

An web application to help everyone do workouts wherever and whenever. Supported by the pose detector feature to analyze every pose and auto count the number of repetitions made. So, let's create a healthy life by doing workouts every day!

![AI Workout Assistant Thumbnail](./public/img/social-media-thumbnail.png)

Pose Detector and Classification in the AI Workout Assistant application fully runs on the client side, so no image data leaves the user's device. Videos or images processed by the program will be automatically deleted.

## How it Works

Image data obtained from video or webcam will be processed by a pose detector using the MoveNet model to generate keypoints. These keypoints are used for repetition calculations and serve as input for classifying workout types with a Dense Neural Network (DNN) model.

![How AI Workout Work](./public/img/how-it-work-ai-workout.png)

## How to Run Locally

- **Prerequisites:** You'll need to have [Git](https://git-scm.com/), [Node](https://nodejs.org/), and [NPM](https://www.npmjs.com/package/npm) installed and running on your machine.
- Open terminal/powershell/command prompt, then clone this repository:  
    ```bash
    cd ai-workout-assistant
    ```
- Install dependencies:
    ```bash
    pip install npm
    ```
- Once the installation is done, you can run the app locally:
    ```bash
    npm run start-dev
    ```
- Then open [http://localhost:8080](http://localhost:8080) to see your app.

## Generate Your Own Workout

1. Open the app locally or visit [aiworkout.live](https://aiworkout.live/).
2. Collect dataset (keypoints):  
    - **Using webcam:** Open the settings menu, turn on `Developer Mode`, and click the `Record Keypoints` button. Click again when finished recording.  
      **Note:** The app only records keypoints while it's playing, and the result will be in CSV format. The value of each keypoint is always in resolution 640x360 (when using a webcam only).
    - **Via upload video:** Open the settings menu, turn on `Developer Mode`, and click `Upload Video`.  
      **Note:** The value of each keypoint will depend on the resolution of the video. High-resolution videos will reduce FPS. You can switch between video and webcam while recording (not recommended as it can cause different resolutions of keypoints).
3. Generate a new model and train it:  
   The model is a binary classification to determine whether the activity is a workout or not (e.g., push-up as positive, not push-up as negative). In the previous step, you collected positive class data. For negative class data, you can use this [video](https://www.youtube.com/watch?v=jJCd3sOuO2M) as a reference (convert first to get keypoints using step 2). After collecting both positive and negative data, open this [Colab](https://colab.research.google.com/drive/1t1t0H6xKit5uup7hFLOqGxD9cbPVcMxv?usp=sharing) and follow the instructions. You will obtain the model in TFJS format for use in the next step.  
   **Note:** To continue to the next step, you need to run the app locally (see `How to Run` above).
4. Put the model into the app:  
   Create a new folder `./public/tfjs-model/your-workout`, then move the TFJS model and bin files into the new folder.
5. Create a new configuration:  
    - Change the file: `./public/mock-data/workout.json`:  
        ```json
        "nameWorkout": ["Push Up", "Squat", "Your Workout"],
        "slugWorkout": ["push-up", "squat", "your-workout"],
        "duration": ["1 Minutes", "3 Minutes", "5 Minutes", "7 Minutes"]
        ```
      **Note:**  
        - `nameWorkout`: Replace `Your Workout` with your workout name.
        - For `slugWorkout`, `your-workout` must match the model folder name.
        - You can change the `duration` in minutes.
    - Create a new file: `./public/rules/your-workout.json` with the following config (use `./public/rules/push-up.json` as reference):  
        ```json
        {
            "poseDetectorConfig": {
                "model": "MoveNet",
                "detectorConfig": {},
                "estimationConfig": {}
            }
        }
        ```
        **Note:**  
        - `Model`: Use `MoveNet` or `PoseNet`. `BlazePose` is not yet implemented as it has 33 keypoints, differing from MoveNet or PoseNet's 17 keypoints. (Ref: https://github.com/tensorflow/tfjs-models/tree/master/pose-detection).
        - `detectorConfig` and `estimationConfig` can use default configurations. To change this, currently, the only way is to modify the relevant file (`./src/handlers/poseHandler.js`).
        
        ```json
        {
            "classifierConfig": {
                "path": "./tfjs-model/your-workout/model.json",
                "label": ["Other", "Your Workout"]
            }
        }
        ```
        **Note:**  
        - `path`: Change to your model path.
        - `label`: Must be ordered alphabetically.
        
        ```json
        {
            "rulesCountConfig": {
                "nameWorkout": "Your Workout",
                "nameStage": ["Down", "Up"],
                "pathImageStage": ["./img/iconmonstr-caret-down-circle-filled-64.png", "./img/iconmonstr-caret-up-circle-filled-64.png"],
                "pathAudioStage": ["./audio/go-down-from-google-translate.webm", "./audio/go-up-from-google-translate.webm"],
                "anglePoint": {
                    "7": {
                        "spouseIdx": [9, 5],
                        "rangeAngle": [
                            { "min": 0, "max": 90 },
                            { "min": 150, "max": 180 }
                        ]
                    }
                }
            }
        }
        ```
        **Note:**  
        - `nameWorkout`: Replace `Your Workout` with your workout name.
        - `nameStage`: For example, in push-ups, there are two stages: Down and Up. The order is important; if it starts with Down, then 1 repetition equals Down->Up.
        - `pathImageStage`: Use either local images (put in `./public/img`) or URL images (outside source).
        - `pathAudioStage`: Prefer using WebM format with a duration of less than 2 seconds. Use local audio files (put in `./public/audio`) or URL audio (outside source).
        - `anglePoint`: For example, index keypoint 7 has spouse index keypoints 9 and 5. `rangeAngle` specifies the angle range for each stage. To help determine index keypoints, please refer to the image below:
        
        ![Ilustration Keypoints](https://camo.githubusercontent.com/b8a385301ca6b034d5f4807505e528b4512a0aa78507dec9ebafcc829b9556be/68747470733a2f2f73746f726167652e676f6f676c65617069732e636f6d2f6d6f76656e65742f636f636f2d6b6579706f696e74732d3530302e706e67)  
        Source image: (https://github.com/tensorflow/tfjs-models/tree/master/pose-detection)
6. Run the app to test:
    ```bash
    npm run start-dev
    ```
    Then open [http://localhost:8080](http://localhost:8080) to see your own workout and have fun!

## Screenshot

![1](https://github.com/user-attachments/assets/271e27d2-44b5-40a6-9183-6c8e358ebc78)



Feel free to adjust any sections or formatting as needed!
