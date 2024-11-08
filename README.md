# Emotion Detection Game

## Overview

The **Emotion Detection Game** is an interactive application that challenges players to mimic specific facial expressions to score points within a time limit. Utilizing computer vision and machine learning techniques, the game detects and classifies user emotions in real-time using a webcam. It's built with Python and leverages libraries such as OpenCV, Dlib, Scikit-learn, and Streamlit.

## Features

- **Real-Time Emotion Detection**: Detects facial expressions using your webcam.
- **Interactive Gameplay**: Match the target emotion to score points.
- **Audio Feedback**: Includes background music and sound effects.
- **Image Capture**: Saves images of successful expressions during gameplay.
- **Customizable Model**: Train your emotion detection model with the provided scripts.

## Table of Contents

- [Demo](#demo)
- [Installation](#installation)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Dataset Preparation](#dataset-preparation)
- [Model Training](#model-training)
- [Training Process and Results](#training-process-and-results)
- [Acknowledgments](#acknowledgments)
- [License](#license)

## Demo



## Installation

### Prerequisites

- **Python 3.7** or higher
- **Webcam** for real-time emotion detection

### Clone the Repository

```bash
git clone https://github.com/baovie4444/emotion_detection_game.git
cd emotion_detection_game
```

### Create a Virtual Environment

It's recommended to use a virtual environment to manage dependencies:

```bash
python -m venv venv
```

Activate the virtual environment:

- On Windows:

  ```bash
  venv\Scripts\activate
  ```

- On macOS/Linux:

  ```bash
  source venv/bin/activate
  ```

### Install Dependencies

Install the required Python packages:

```bash
pip install -r requirements.txt
```

### Additional Files

#### Dlib's Pre-trained Model

Download the facial landmark predictor from [Dlib's website](http://dlib.net/files/shape_predictor_68_face_landmarks.dat.bz2):

1. Download `shape_predictor_68_face_landmarks.dat.bz2`.
2. Extract the `.dat` file.
3. Place `shape_predictor_68_face_landmarks.dat` in the project root directory.

#### Audio Files

Ensure you have the following audio files in the project directory:

- `point.wav` - Sound effect when scoring a point.
- `background.wav` - Background music during gameplay.
- `end.wav` - Sound effect when the game ends.


#### Dataset

Prepare a dataset of facial images for training the emotion detection model, using the FER 2013 dataset from Kaggle.

- Organize images into folders named after the emotions (e.g., `happy`, `sad`, `neutral`, `surprised`).
- Place the dataset in `data/train` and `data/test` directories.

## Usage

### Running the Game

Start the Streamlit application:

```bash
streamlit run app.py
```

This command will open the game in your default web browser.

### Gameplay Instructions

1. **Start the Game**: Click the **"🎮 Start Game"** button in the sidebar.
2. **View Target Emotion**: The target emotion is displayed; mimic this expression.
3. **Score Points**: Hold the expression for a short duration to score.
4. **Time Limit**: You have 60 seconds to score as many points as possible.
5. **Game Over**: Your final score is displayed when time runs out.

## Project Structure

```
emotion-detection-game/
├── app.py
├── trainmodel.ipynb
├── data/
│   ├── train/
│   └── test/
├── model/
│   └── emotion_model_100.pkl
├── shape_predictor_68_face_landmarks.dat
├── haarcascade_frontalface_default.xml
├── requirements.txt
├── README.md
├── point.wav
├── background.wav
└── end.wav
```

- **`app.py`**: Main application code for the game.
- **`trainmodel.ipynb`**: Notebook to train the emotion detection model.
- **`data/`**: Directory for training and testing datasets.
- **`model/`**: Contains the saved machine learning model.


## Dataset Preparation

To train the emotion detection model, you'll need a dataset of facial images labeled with emotions.

### Collect Images

Download from https://www.kaggle.com/datasets/msambare/fer2013 then remove angry, disgust, fear emotions

### Organize Dataset

Organize the images into the following structure:

```
data/
├── train/
│   ├── happy/
│   ├── sad/
│   ├── neutral/
│   └── surprised/
└── test/
    ├── happy/
    ├── sad/
    ├── neutral/
    └── surprised/
```


## Model Training

### Train the Model

Use the `trainmodel.ipynb` notebook to train the SVM classifier:


- The script performs hyperparameter tuning using GridSearchCV.
- The trained model is saved as `emotion_model_100.pkl` in the `model/` directory.

## Training Process and Results

### Data Preprocessing

- **Facial Landmark Extraction**: Used Dlib's `shape_predictor_68_face_landmarks.dat` to extract 68 facial landmarks from each image.
- **Data Normalization**: Normalized landmarks by scaling to a standard coordinate system to reduce variations due to face size and position.
- **Flattening**: Converted 2D landmark coordinates into a 1D feature vector for model input.

### Model Architecture

- **Algorithm**: Support Vector Machine (SVM) with probabilistic outputs.
- **Library**: Scikit-learn's `SVC` classifier.
- **Features**: The normalized facial landmark coordinates.

### Hyperparameter Tuning

Performed hyperparameter tuning using GridSearchCV with 3-fold cross-validation:

- **Parameter Grid**:
  - `C`: [0.1, 1, 10, 100]
  - `gamma`: [0.001, 0.01, 0.1, 1]
  - `kernel`: ['rbf', 'linear']
- **Scoring Metric**: Weighted F1 Score.

**Best Parameters Found**:

- `C`: 10
- `gamma`: 0.01
- `kernel`: 'rbf'

### Training Results

After training the model with the best hyperparameters, we evaluated its performance on the test set.

#### Classification Metrics

- **Accuracy**: **80.94%**
- **Weighted F1 Score**: **81.06%**

#### Classification Report

```
              precision    recall  f1-score   support

           happy       0.91      0.90      0.91       268
         neutral       0.74      0.75      0.75       235
            sad       0.68      0.71      0.70       210
      surprised       0.90      0.86      0.88       200

    accuracy                           0.81       913
   macro avg       0.81      0.81      0.81       913
weighted avg       0.81      0.81      0.81       913
```

#### Confusion Matrix

|                   | Predicted Happy | Predicted Neutral | Predicted Sad | Predicted Surprised |
|-------------------|-----------------|-------------------|---------------|---------------------|
| **Actual Happy**     |       242       |        7         |       18       |          2          |
| **Actual Neutral**   |        8        |       177        |      41       |         9          |
| **Actual Sad**       |        14        |        38         |     149       |         9          |
| **Actual Surprised** |        1        |        17         |       10       |        172          |



#### Interpretation

- The model performs best on **happy** and **surprised** expressions, with precision and recall around 90%.
- **Neutral** and **sad** expressions have lower precision and recall, indicating the model sometimes confuses these expressions.
- Overall, the model achieves a good balance between precision and recall across all classes.

### Model Saving

The trained model and label encoder are saved as a pickle file for use in the game application:

```python
with open('model/emotion_model_100.pkl', 'wb') as f:
    pickle.dump({'model': best_model, 'label_encoder': label_encoder}, f)
```

## Acknowledgments

- **[Dlib](http://dlib.net/)**: For the facial landmark detector.
- **[OpenCV](https://opencv.org/)**: For real-time computer vision.
- **[Streamlit](https://streamlit.io/)**: For the interactive web application framework.
- **[Scikit-learn](https://scikit-learn.org/)**: For machine learning algorithms.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

*Feel free to contribute to this project by submitting issues or pull requests.*
