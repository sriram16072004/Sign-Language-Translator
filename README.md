# Sign-Language-Translator
 Designed and developed a web-based application to translate sign language gestures into text and audio. 
 Implemented front-end using HTML, CSS, and JavaScript with Python-based logic integration. 
 Followed SDLC practices including requirement analysis, development, testing, and debugging. 
 Improved accessibility and user experience through modular design and clear UI components. 
 Collaborated with team members to integrate features and resolve technical issues. 

 # CODE
 <!DOCTYPE html>
<html>
<head>
  <title>Sign Language Translator</title>
  <script src="https://cdn.jsdelivr.net/npm/@tensorflow/tfjs@latest"></script>
  <script src="https://cdn.jsdelivr.net/npm/@teachablemachine/image@latest"></script>
  <style>
    body {
      text-align: center;
      font-family: Arial;
      background: #f4f4f4;
    }
    h1 {
      margin-top: 20px;
    }
    #webcam-container {
      margin-top: 20px;
    }
    #label-container {
      font-size: 24px;
      margin-top: 20px;
      color: green;
    }
    button {
      padding: 10px 20px;
      font-size: 16px;
      margin-top: 20px;
      cursor: pointer;
    }
  </style>
</head>

<body>

<h1>Sign Language Translator</h1>
<button onclick="init()">Start Camera</button>
<div id="webcam-container"></div>
<div id="label-container"></div>

<script>
  const URL = "YOUR_MODEL_URL/";  

  let model, webcam, labelContainer, maxPredictions;

  async function init() {
      const modelURL = URL + "model.json";
      const metadataURL = URL + "metadata.json";

      model = await tmImage.load(modelURL, metadataURL);
      maxPredictions = model.getTotalClasses();

      const flip = true;
      webcam = new tmImage.Webcam(300, 300, flip);
      await webcam.setup();
      await webcam.play();
      window.requestAnimationFrame(loop);

      document.getElementById("webcam-container").appendChild(webcam.canvas);
      labelContainer = document.getElementById("label-container");
  }

  async function loop() {
      webcam.update();
      await predict();
      window.requestAnimationFrame(loop);
  }

  async function predict() {
      const prediction = await model.predict(webcam.canvas);
      let highestProb = 0;
      let detectedSign = "";

      for (let i = 0; i < maxPredictions; i++) {
          if (prediction[i].probability > highestProb) {
              highestProb = prediction[i].probability;
              detectedSign = prediction[i].className;
          }
      }

      if (highestProb > 0.85) {
          labelContainer.innerHTML = "Detected Sign: " + detectedSign;
          speak(detectedSign);
      } else {
          labelContainer.innerHTML = "Show Clear Sign...";
      }
  }

  function speak(text) {
      const speech = new SpeechSynthesisUtterance(text);
      speech.lang = "en-US";
      window.speechSynthesis.speak(speech);
  }
</script>

</body>
</html>
