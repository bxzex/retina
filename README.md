# Retina

A small convolutional network that generates its own training data, trains in the tab, and then reads the digits you draw.

https://bxzex.github.io/retina/

There's no MNIST download and no pretrained weights. The training digits are rendered at load time from fonts already on your machine, at random sizes, angles and offsets, then centred by mass the same way MNIST centres its digits.

The network is two conv and pool layers into a dense layer, about 5,000 parameters, and every gradient is written by hand. I checked the backward pass against numerical gradients. It reaches around 98% on a held-out split in under a minute.
