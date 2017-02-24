# TensorFlow Convolutional AutoEncoder

This project provides utilities to build a deep Convolutional AutoEncoder (CAE) in just a few lines of code.

This project is based only on TensorFlow.


## Experiments

`convolutional_autoencoder.py` shows an example of a CAE for the MNIST dataset.

The structure of this conv autoencoder is shown below:

![autoencoder structure](https://cloud.githubusercontent.com/assets/13087207/23150628/cd447882-f7c2-11e6-938a-b8e672d71760.png)

The encoding part has 2 convolution layers (each followed by a max-pooling layer) and a fully connected layer. This part
would encode an input image into a 20-dimension vector (representation). And then the decoding part, which has 1 fully connected layer
and 2 convolution layers, would decode the representation back to a 28x28 image (reconstruction).

Training was done using GTX1070 GPU, batch size 100, 100000 passes.

Trained weights (saved in the saver directory) of the 1st convolutional layer are shown below:
![weights](logs/conv_1_weights.png)
And here's some of the reconstruction results:
![reconstruction](logs/reconstructions.png)

## Implementation

### Un-pooling

Since the max-pooling operation is not injective, and TensorFlow does not have a built-in unpooling method,
we have to implement our own approximation.
But it is actually easy to do so using TensorFlow's [`tf.nn.conv2d_transpose()`](https://www.tensorflow.org/api_docs/python/nn/convolution#conv2d_transpose) method.

The idea was to replace each entry in the pooled map with an NxM kernel with the original entry in the upper left,
where N and M are the shape of the pooling kernel.

![un-pooling](https://cloud.githubusercontent.com/assets/13087207/22672037/77e521c6-ec9f-11e6-9aba-119f954cd9f8.png)

This is equivalent to doing transpose of conv2d on the input map 
with a kernel that has 1 on the upper left and 0 elsewhere. 
Therefore we could do this trick with `tf.nn.conv2d_transpose()` method.


