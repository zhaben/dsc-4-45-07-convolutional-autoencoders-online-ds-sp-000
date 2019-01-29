
# Convolutional Autoencoders


## Introduction 
In this lesson , we will look at Convolutional autoencoders (CAEs). Autoencoders have been very popular in the area of image analysis and generative arts. Previously we saw that using fully connected architectures with autoencoders, we can conduct meaningful experiments in this domain. For larger images, and more complicated tasks, Convolutional Autoencoders provide extra features towards reducing the computational cost and improving the quality of the model output. This lesson is aimed at explaining how CAEs work and how do they differ from other types of Autoencoders. 

*Note: You are advised to refer back to the section on CNNs for details on convolution process and conv/pooling layers.*

## Objectives
You will be able to: 

- Understand how convolution is used with autoencoders
- Describe the Encoder and Decoder components of a CAE
- Provide nasic understanding for application areas for CAEs

## Convolutions - Recap

In the previous section, we looked at convolutional neural networks (CNNs) and how convolutional filter are used to learn features from images. Here is what a simple convolution process might look like :
<img src='conv.gif'>

Above image shows a convolution between a 4x4x1 input and a 3x3x1 convolutional filter. The result is a 2x2x1 activation map. 
> **Convolution is defined as the integral of the product of two functions $f(t)$ and $g(t)$ after one is reversed and shifted** 

$$f(t) * g(t) \stackrel{\text{def}}{=} \int_{-\infty}^{\infty}f(\tau)g(t-\tau)d\tau$$

We have seen that autoencoders can be potentially trained to $decode(encode(x))$ inputs living in an n-dimensional space like images. In the image domain where the signals are finite, this formula becomes:
$$O(i, j) = \sum_{u=-2k-1}^{2k+1}\sum_{v=-2k -1}^{2k +1}F(u, v)I(i -u, j -v)$$

- $O(i,j)$ is the output pixel, in position (i,j)
- $2(k)+1$ is the side of a square, odd convolutional filter
- $F$ is the convolutional filter
- $I$ is the input image

## CAEs (Convolutional Autoencoders)

With CAEs, optimal filter definition is learnt by the model, instead of the analyst defining the filters as we saw previously. CAEs have shown state of the art performance for unsupervised learning of convolutional filters. Learned filters can be applied to any input in order to extract features. These features, then, can be used to perform image classification task that generally requires a compact representation of the input. 

The key difference between CNN and CAE is that the CNNs are trained end-to-end to learn filters as supervised learning algorithms. CAEs on the other hand, are trained only to learn filters able to extract features that can be used to __reconstruct the input__. 

CAEs scale well to big data because the number of parameters required to produce an activation map is always the same, no matter what the size of the input is. Therefore, CAEs are general purpose feature extractors differently from __AEs that completely ignore the 2D image structure__. In fact, __in AEs the image must be unrolled into a single vector and the network must be built following the constraint on the number of inputs__. CAEs treat an image during analysis , as an image i.e. 2D discrete data. 

CAEs replace fully connected layers by convolutional layers. These, along with pooling layers, convert the input from __wide and thin__ (let’s say 100 x 100 px with 3 channels — RGB) to __narrow and thick__. This helps the network extract visual features from the images, and therefore obtain a much more accurate latent space representation. 

<img src="conv2.png" width=700>

## CAE Encoder

We have seen with CNNs that single convolutional filter can’t learn to extract the great variety of patterns that compose an image. For this reason, every convolutional layer is composed of $n$ (hyper-parameter) convolutional filters, each with depth $D$, where D is the input dim. A convolution among an input volume $I={I_1,⋯,I_D}$ and a set of $n$ convolutional filters ${F_1^1,⋯,F_1^n}$, each with depth $D$, produces a set of activations maps with depth $n$:



$$O_m(i, j) = a\left(\sum_{d=1}^{D}{\sum_{u=-2k-1}^{2k+1}\sum_{v=-2k -1}^{2k +1}F^{(1)}_{m_d}(u, v)I_d(i -u, j -v)}\right) \quad m = 1, \cdots, n$$

Every convolution is wrapped by a non-linear activation function in order to improve the generalization.

$$O_m = a(I * F^{1}_{m} + b^{1}_m) \quad m = 1, \cdots, m$$

Here $b_m ^1$ is the bias for the m-th feature map. Activation maps are the encoding of the input $I$ in a low dimensional space that describe the number of parameters to learn.

We use decoding operation to reconstruct the input $I$ from the feature maps. Convolutional autoencoders are effectively CNNs, therefore the decoding operation is again a convolution.

### Input Padding 

We saw that convolution reduces the output’s dimensions and hence it is not possible to use a convolution to reconstruct a volume with the same spatial extent of its input. We can deal with this using the input padding. If we pad with zeros the input volume $I$, then the result of the first convolution can have a spatial extent greater than the one of $I$ and thus the second convolution can produce a volume with the original spatial extent of $I$.

If we want to pad $I$ by $2(2k+1)−2$ zeros ($(2k+1)−1$ per side), the encoding will produce a volume with width $O_w$ and height $O_h$ equals to:

$$O_w = O_h = (I_w + 2(2k +1) -2) - (2k + 1) + 1 = I_w + (2k + 1) - 1$$

<img src="conv3.gif" width=300>

## CAE Decoder

The $n$ feature maps from above is used as input to the decoder, in order to reconstruct the input image $I$ from this reduced representation. The hyper-parameters of the decoding convolution are fixed by the encoding architecture. The reconstructed image $Ĩ$  is the result of the convolution between the volume of feature maps convolutional filters volume $F^2$.

$$\tilde{I} = a(Z * F^{(2)}_{m} + b^{(2)})$$

Padding $I$ with the previously found amount of zeros, leads the decoding convolution to produce a volume with dimensions:

$$O_w = O_h = ( I_w + (2k + 1) - 1 ) -  (2k + 1) + 1 = I_w = I_h$$

Having input’s dimensions equals to the output’s dimensions, it possible to relate input and output using any loss function, like the MSE:
$$\mathcal{L}(I, \tilde{I}) = \frac{1}{2} || I - \tilde{I}||_{2}^{2}$$


## CAE Use Cases

### Image Reconstruction


CAEs can be useful for reconstruction of images, remove noise from picture, or reconstruct missing parts. Let’s see what a CAE can do to replace part of an image of an eye. 
<img src="conv4.png" width=600>

Similarly the idea of missing details from an image can be used to reconstruct an image with higher quality than the original as shown below 

<img src="details.png" width=800>

## Image colorization

CAEs do pretty well on colorizing the right parts of the image. It can understood shapes and associated colors in an image e.g. circles are red and squares are blue in the image below. The purple color comes from a blend of blue and red where the networks hesitates between a circle and a square.

<img src="conv5.png" width=600>

here are some more examples of image colorization

<img src="color.png" width=800>

## Further Reading 

[Exploiting the Potential of Standard Convolutional Autoencoders
for Image Restoration by Evolutionary Search](https://arxiv.org/pdf/1803.00370.pdf)

## Summary 

In this lesson we looked at convolutional Autoencoders that used the idea of convolution ( from CNNs) for different image analysis tasks. We looked at how the encoder/decoder setup works with such AEs and also highighted some application areas. Next, we will try to implement a simple CAE in Keras. 
