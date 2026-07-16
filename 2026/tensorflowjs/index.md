# A Friendly Dive into How TensorFlow.js Actually Works


<!--more-->

Wait… a web page can run AI? Yep. Thanks to TensorFlow.js, your browser isn’t just for reading articles, watching videos, or endlessly scrolling social media. It can load neural networks, process webcam frames, recognize speech, and make predictions locally in real time— feeling a bit like giving your browser a brain. 

But how does it actually pull this off? Let’s take a look under the hood.

## Four big ideas, one clever library

Everything TensorFlow.js does hangs off four core concepts. Get these, and the rest of the architecture snaps into focus.

![Four idea cards: 01 Data — everything is a tensor; 02 API — two ways in (Core Ops and Layers API); 03 Execution — multiple backends; 04 Lifecycle — cradle to deploy](/img/four-ideas.png) 

### Idea 1 — Tensors: how TensorFlow.js represents data

Think of tensors as the universal language of TensorFlow.js. A number is a tensor. A list is a tensor. An image? A tensor with more dimensions. Three things make them special:

They're **immutable** — every operation creates a new tensor rather than modifying the old one. They can **live in GPU memory**, which is how the fast backends get their speed. And they **scale in dimensions**, from a single value all the way up to batches of images.

![From a 1D vector to a 2D matrix to a 3D image tensor to a 4D batch of images](/img/tensor-dimensions.svg)

*Same idea, more dimensions: from a single row of numbers to a whole batch of images.*

### Idea 2 — APIs: how TensorFlow.js defines the computation

Here's the genuinely clever part. When your code says "multiply these matrices," TensorFlow.js doesn't have one way to do that — it has **five**. A backend selector inspects the environment and routes every operation to the fastest engine available, falling back gracefully when fancy hardware isn't around.

![Model code flows into the backend selector, which routes to WebGPU, WebGL, WASM, or CPU, producing predictions](/img/backend-selector.svg)

*Write the model once. The selector picks the engine at runtime.*

| Backend | Think of it as | Best for |
|---|---|---|
| WebGPU | Modern GPU compute, no disguises | The fastest option, where supported |
| WebGL | A GPU smuggled in via graphics shaders | Most browsers today |
| WASM | A supercharged CPU | Small models, wide compatibility |
| CPU | The universal fallback | Always works |
| Node.js | Native TensorFlow, no browser needed | Servers |

### Idea 3 — Backends: where the computation runs

Here's the rite of passage every TensorFlow.js developer goes through: your demo runs beautifully for two minutes, then your GPU memory quietly fills up and the tab keels over.

Why? Because tensors **aren't automatically garbage collected**. Many of them live in GPU memory, which JavaScript's garbage collector can't see. Every intermediate tensor you create sticks around until you explicitly let it go.

The fix is delightfully simple: wrap temporary work in `tf.tidy()`, and it sweeps up every intermediate tensor when the function returns. For tensors you manage by hand, call `dispose()` when you're done.

```javascript
// Without tidy: leaks `a` and `b` into GPU memory 
// With tidy: intermediates are cleaned up automatically 
const result = tf.tidy(() => {
  const a = tf.tensor([1, 2, 3]);
  const b = a.square();   // intermediate — swept up
  return b.sum();          // survives the tidy
});
```

> **Rule of thumb:** if you created a tensor and didn't return it, someone has to clean it up — and that someone is `tf.tidy()`, or it's you.


### Idea 4 — Lifecycle: load, train, manage, save, and deploy

You've got two paths to a working model. Build and train it **entirely in JavaScript**, or **import a trained TensorFlow or Keras model** from the Python world. Either way, after conversion a deployed model is refreshingly boring: a `model.json` file describing the architecture, plus binary weight shards. Static files. Host them anywhere you'd host an image.

![Pipeline: train, convert, model.json plus weight shards, browser, prediction](/img/deployment-pipeline.svg)

*Train anywhere → convert once → serve as static files → predict in the tab.*


## Takeaway: how it works under the hood


TensorFlow.js isn’t just “TensorFlow in JavaScript.” It’s a system where each layer has a clear responsibility. Tensors represent data, the APIs describe computations, backends execute those computations on available hardware, and lifecycle tools manage models from training to deployment. Together, these layers let you write a few lines of JavaScript while TensorFlow.js handles the messy details of memory management, hardware acceleration, and model execution behind the scenes.

## Easter egg: where Teachable Machine fits

Teachable Machine is one of those tools. It hides almost all of TensorFlow.js behind a friendly interface: you collect examples, label classes, and click Train. Behind those buttons, TensorFlow.js converts your data into tensors, fine-tunes a pre-trained model, executes the computation using the browser’s available backend, and exports a model.json file together with its weights. That's how the magic works!


Next, let's see another black box behind TM - transform learning! 

Cheers!

## Primary sources

- [Smilkov, Thorat, Assogba, et al. (2019). *TensorFlow.js: Machine Learning for the Web and Beyond* (arXiv:1901.05350)](https://arxiv.org/pdf/1901.05350) — The official TensorFlow.js paper introducing its architecture, tensors, APIs, and execution backends.

- [TensorFlow.js Official Documentation](https://tensorflow.github.io/tfjs/) · [GitHub Repository](https://github.com/tensorflow/tfjs) — Official documentation, source code, and backend implementations.

- [TensorFlow.js Guide: Tensors and Operations](https://www.tensorflow.org/js/guide/tensors_operations) — Tensor creation, operations, `tf.tidy()`, and `tf.memory()`.

- [TensorFlow.js Guide: Platform and Environment](https://www.tensorflow.org/js/guide/platform_environment) — Backend selection, WebAssembly, `setBackend()`, and execution environments.

- [TensorFlow.js Guide: Model Conversion](https://www.tensorflow.org/js/guide/conversion) — `tensorflowjs_converter`, weight sharding, Grappler optimization, and model loading.

- [TensorFlow.js Guide: Layers API for Keras Users](https://www.tensorflow.org/js/guide/layers_for_keras_users) — Keras mapping, model training, and lifecycle management.

- [TensorFlow.js API Reference](https://js.tensorflow.org/api/latest/) — Complete API reference for models, tensors, layers, and backends.

- [TensorFlow Blog: *Introducing the WebAssembly Backend for TensorFlow.js*](https://blog.tensorflow.org/2020/03/introducing-webassembly-backend-for-tensorflow-js.html) — Background on the WASM backend, XNNPACK, and performance.

