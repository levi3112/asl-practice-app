# ASL Practice App
This app is designed as a learning tool that allows users to check their proficiency in American Sign Language (ASL) through AI-powered quizzes after engaging with ASL content on YouTube. For ASL recognition, I utilized a top-performing model from a Kaggle competition and referenced educational content from YouTube channels such as ASLU and Start ASL. I integrated these resources to create this app as a Proof of Concept (PoC) to demonstrate the technical feasibility of the idea. While the UI/UX is still in its initial stage and has room for improvement, the focus of this project was on showcasing the underlying technical capabilities.

Also, the gesture classification model used in this project references the models created by [@hoyso48](https://www.kaggle.com/hoyso48) and [@ohkawa3](https://www.kaggle.com/chack3). I was truly impressed by the exceptional quality. I sincerely appreciate the generosity in sharing both the models and the Jupyter Notebook on Kaggle.

## About the App

### Features

#### 1. Finger Spelling Practice
- Learn and practice finger spelling for the alphabet (A-Z).

#### 2. Basic Vocabulary Practice
- Access and practice approximately 200 essential ASL vocabulary words.

This interactive format allows users to actively reinforce their ASL skills through practice and immediate feedback. For a detailed demonstration, please refer to the following video.

## App Demo

### [1. Finger Spelling Practice](https://youtu.be/b0ze2TyXaEc)
[![Finger Spelling Practice](https://img.youtube.com/vi/b0ze2TyXaEc/0.jpg)](https://youtu.be/b0ze2TyXaEc)

### [2. Basic Vocabulary Practice](https://youtu.be/PfMti7SdjAI)
[![Basic Vocabulary Practice](https://img.youtube.com/vi/PfMti7SdjAI/0.jpg)](https://youtu.be/PfMti7SdjAI)

## Technology Stack

The app was developed using the following technologies:
- Front-end: React, TypeScript, MUI, MediaPipe
- Back-end: Flask, Python, TensorFlow

## Architecture Overview

The following diagram illustrates the key components and their interactions:

![System Architecture](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3g7zj0050kdbcz9jgwp9.png)

### 1. Finger Spelling Recognition
- The front-end runs a custom gesture recognition classification model directly to process user gestures.
- The model outputs the classification results in text format, which are then displayed on the front-end interface.

### 2. Basic Vocabulary Recognition
- The front-end uses MediaPipe’s Holistic model to capture the xyz coordinates of 543 landmarks frame by frame.
- These landmark data are transmitted to the back-end in real-time using Socket.IO and are stored sequentially.
- Once sufficient landmark data has been accumulated, the back-end executes a basic vocabulary gesture recognition classification model.
- The classification model processes the accumulated data and returns the recognition results in a list format, which are then sent back to the front-end for display.

## Insights and Challenges

### 1. Real-time Performance Achieved
The app successfully achieved sufficient real-time performance for both the Finger Spelling and Basic Vocabulary features, ensuring smooth and responsive interactions for users during gesture recognition.

### 2. Technology Choice: REST API vs. Socket.IO
The Kaggle ASL competition model used in this project was designed for implementation in TFLite format, which could have been executed directly in the browser. However, for this Proof of Concept (PoC), Flask and Python were chosen to implement the gesture recognition functionality as a REST API to prioritize ease of data processing and development efficiency.

By the way, when the frame data accumulated on the back end is visualized, it appears as follows. This serves as the input data for the gesture recognition classification model.

[![Finger Spelling Practice](https://img.youtube.com/vi/AgZ0c_rVt80/0.jpg)](https://youtu.be/AgZ0c_rVt80)

#### *Challenges with REST API:*
Initially, the front-end recorded and accumulated frame data, sending it to the back-end in bulk via a POST request, but the server-side preprocessing required before executing the model introduced a slight delay, resulting in a noticeable lag between performing gestures and receiving recognition results.

#### *Solution with Socket.IO:*
To address this issue, Socket.IO was used instead of the REST API, transmitting frame data to the server incrementally in real-time and enabling stepped pre-processing, which successfully eliminated the lag observed in the REST API implementation.

#### *Scalability Concern:*
While Socket.IO proved effective, it may face performance issues under heavy server load or high concurrent connections due to increased processing demands on the back end, a scalability risk that was not fully tested within the scope of this project and remains an area for future investigation.

### 3. MediaPipe Model Integration Challenges

The app uses MediaPipe’s Gesture Recognition model for Finger Spelling and the Holistic model for Basic Vocabulary in the front-end.

#### *Error Encountered:*
When loading the Gesture Recognition model after the Holistic model, an error occurred despite ensuring cleanup through the useEffect lifecycle by calling the model instance's close() method.

```
Failed to load MediaPipe gesture model: RuntimeError: Aborted(Module.arguments has been replaced with plain arguments_ (the initial value can be provided on Module, but after startup the value is only looked for on a local variable of that name)) at abort (holistic_solution_si…wasm_bin.js:9:17640) at Object.get (holistic_solution_si…_wasm_bin.js:9:7759) at vision_wasm_internal.js:9:2905 at async createGestureRecognizer (Quiz.tsx:49:35)
```

#### *Workaround:*
The error was eventually resolved by explicitly setting `window.Module` to `undefined` to clear the previous state.

#### *Unresolved Root Cause:*
While the workaround fixed the issue, the exact cause of the error and why the solution worked remain unclear. This aspect requires further investigation for a robust understanding.

## Unresolved Issues

### 1. Dynamic Gestures (J and Z) for Finger Spelling
- The current Gesture Model for finger spelling does not support dynamic gestures such as "J" and "Z," as these require handling temporal elements. Implementing models like Long Short-Term Memory (LSTM) or Transformer would be necessary, but this has not yet been addressed.
- MediaPipe’s Gesture Recognizer provides a customization tool that allows users to create classification models relatively easily.- Link: Hand gesture recognition model customization guide. For a deeper understanding of how this customization tool works, refer to the following GitHub repository. According to the implementation, the create method in gesture_recognizer.py utilizes TensorFlow Keras to build neural networks for classification models. However, it is important to note that dynamic gestures are not supported by default, and customizations such as adding layers would be required to handle them.

Related link: [MediaPipe Gesture Recognizer](https://github.com/google-ai-edge/mediapipe/tree/master/mediapipe/model_maker/python/vision/gesture_recognizer)

### 2. Playback of Learning Content
- The playback of learning content is currently implemented as a simple iframe embedding of YouTube videos, which leads to usability challenges in terms of controls.
- YouTube provides a Player API Reference for iframe Embeds, which offers more granular control over video playback. However, it was put on hold because it could not be implemented as expected.

Related link: [Player API Reference](https://developers.google.com/youtube/iframe_api_reference?hl=ja)

## Planned Features

- User login functionality
- Improvement of learning content display
- Management of learning progress and records
- Optimization and personalization of quiz questions
- Addition of new quiz methods (e.g., entering an address using Finger Spelling)

## References

- [Google - Isolated Sign Language Recognition](https://www.kaggle.com/competitions/asl-signs)
- [Google - American Sign Language Fingerspelling Recognition](https://www.kaggle.com/competitions/asl-fingerspelling)
- [Hand gesture recognition model customization guide](https://github.com/google-ai-edge/mediapipe/tree/master/mediapipe/model_maker/python/vision/gesture_recognizer)
- [MediaPipe Model Maker](https://ai.google.dev/edge/mediapipe/solutions/model_maker)
- [ASLU (Website)](https://lifeprint.com/)
- [ASLU (YouTube)](https://www.youtube.com/@aslu)
- [Start ASL](https://www.youtube.com/@StartASL)
