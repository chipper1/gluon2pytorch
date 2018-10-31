# gluon2pytorch

[![GitHub License](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Python Version](https://img.shields.io/badge/python-2.7%2C3.6-lightgrey.svg)](https://github.com/nerox8664/gluon2pytorch)

Gluon to PyTorch model convertor with script generation.

## Installation

```
git clone https://github.com/nerox8664/gluon2pytorch
cd gluon2pytorch
pip install -e . 
```


## How to use

It's the convertor of Gluon graph to a Pytorch model file + weights.

Firstly, we need to load (or create) Gluon Hybrid model:

```

class ReLUTest(mx.gluon.nn.HybridSequential):
    def __init__(self):
        super(ReLUTest, self).__init__()
        from mxnet.gluon import nn
        with self.name_scope():
            self.conv1 = nn.Conv2D(3, 32)
            self.relu = nn.Activation('relu')

    def hybrid_forward(self, F, x):
        x = F.relu(self.relu(self.conv1(x)))
        return x


if __name__ == '__main__':
    net = ReLUTest()
    
    # Make sure it's hybrid and initialized
    net.hybridize()
    net.collect_params().initialize()

```

The next step - call the converter:

```
    pytorch_model = gluon2pytorch(net, dst_dir=None, pytorch_module_name='ReLUTest')
```

Finally, we can check the difference

```
    input_np = np.random.uniform(-1, 1, (1, 3, 224, 224))

    gluon_output = net(mx.nd.array(input_np))
    pytorch_output = pytorch_model(torch.FloatTensor(input_np))
    check_error(gluon_output, pytorch_output)
```

## Supported layers

Layers:

* Linear
* Conv2d
* MaxPool2d
* Global average pooling (as special case of AdaptiveAvgPool2d)
* BatchNorm2d

Reshape:

* Flatten

Activations:

* ReLU
* Sigmoid

Element-wise:

* Addition
* Concatenation

## Models converted with pytorch2keras

* ResNet*
* SeNet
* DenseNet*

## Code snippets
Look at the `tests` directory.

## License
This software is covered by MIT License.