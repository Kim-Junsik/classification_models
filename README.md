# Classification models Zoo
Pretrained classification models for Keras

### Models: 
- [ResNet](https://arxiv.org/abs/1512.03385) models converted from MXNet:
  - [ResNet18](https://github.com/qubvel/classification_models/blob/master/imgs/graphs/resnet18.png)
  - [ResNet34](https://github.com/qubvel/classification_models/blob/master/imgs/graphs/resnet34.png)
  - [ResNet50](https://github.com/qubvel/classification_models/blob/master/imgs/graphs/resnet50.png)
  - ResNet101
  - ResNet152
- [ResNeXt](https://arxiv.org/abs/1611.05431) models converted from MXNet:
  - ResNeXt50
  - ResNeXt101
- [ResNet](https://arxiv.org/abs/1512.03385) models with [squeeze and excitation](https://arxiv.org/abs/1709.01507) block:
  - SE-ResNet18 (fine-tuned MXNet ResNet18 model)
  - SE-ResNet34 (fine-tuned MXNet ResNet34 model)
  - SE-ResNet50 (converted from [PyTorch](https://github.com/Cadene/pretrained-models.pytorch))
  - SE-ResNet101 (converted from [PyTorch](https://github.com/Cadene/pretrained-models.pytorch))
  - SE-ResNet152 (converted from [PyTorch](https://github.com/Cadene/pretrained-models.pytorch))
- [ResNeXt](https://arxiv.org/abs/1611.05431) models with [squeeze and excitation](https://arxiv.org/abs/1709.01507) block:
  - SE-ResNeXt50 (converted from [PyTorch](https://github.com/Cadene/pretrained-models.pytorch))
  - SE-ResNeXt101 (converted from [PyTorch](https://github.com/Cadene/pretrained-models.pytorch))
  
| Model     | Classes |      Weights       | Acc@1 | Acc@5|
|-----------|:-------:|:----------------------------:|:------:|:------:|
| ResNet18  | 1000  | `imagenet` | 0.6848 |0.8868|
| ResNet34  | 1000  | `imagenet` | 0.7241 |0.9097|
| ResNet50  | 1000<br>11586  |`imagenet`<br>`imagenet11k-place365ch` | 0.7503<br>-  |0.9256<br>- |
| ResNet101 | 1000  | `imagenet` | 0.7680   |0.9328 |
| ResNet152 | 1000<br>11221 | `imagenet`<br>`imagenet11k` | 0.7689<br>-  | 0.9331<br>-|
| ResNeXt50 | 1000 | `imagenet` | -  |- |
| ResNeXt101 | 1000 | `imagenet` | -  |- |
| SEResNet18  | 1000  | `imagenet` | - | - |
| SEResNet34  | 1000  | `imagenet` | - | - |
| SEResNet50   | 1000  | `imagenet` | 0.7644 |0.9302|
| SEResNet101  | 1000  | `imagenet` | 0.7792 |0.9400|
| SEResNet152  | 1000  | `imagenet` | 0.7834 |0.9408|
| SEResNeXt50  | 1000  | `imagenet` | 0.7874 |0.9430|
| SEResNeXt101  | 1000  | `imagenet` | - | - |

### Installation
PyPi package:
```bash
$ pip install image-classifiers
```
Latest version:
```bash
$ pip install git+https://github.com/qubvel/classification_models.git
```

### Example  

Imagenet inference example:  
```python
import numpy as np
from skimage.io import imread
from skimage.transfrom import resize
from keras.applications.imagenet_utils import decode_predictions

from classification_models import ResNet18
from classification_models.resnet import preprocess_input

# read and prepare image
x = imread('./imgs/tests/seagull.jpg')
x = resize(x, (224, 224)) * 255    # cast back to 0-255 range
x = preprocess_input(x)
x = np.expand_dims(x, 0)

# load model
model = ResNet18(input_shape=(224,224,3), weights='imagenet', classes=1000)

# processing image
y = model.predict(x)

# result
print(decode_predictions(y))
```

Model fine-tuning example:
```python
import keras
from classification_models import ResNet18

# prepare your data
X = ...
y = ...

n_classes = 10

# build model
base_model = ResNet18(input_shape=(224,224,3), weights='imagenet', include_top=False)
x = keras.layers.AveragePooling2D((7,7))(base_model.output)
x = keras.layers.Dropout(0.3)(x)
output = keras.layers.Dense(n_classes)(x)
model = keras.models.Model(inputs=[base_model.input], outputs=[output])

# train
model.compile(optimizer='SGD', loss='categorical_crossentropy', metrics=['accuracy'])
model.fit(X, y)
```
