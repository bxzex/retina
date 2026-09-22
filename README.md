# Retina

A convolutional network that builds its own training set, trains itself in the
browser tab, and then reads the digits you draw. Every filter and every feature
map is shown as it fires.

Live: https://bxzex.github.io/retina/

## No model, no dataset, no download

There is no MNIST here to fetch and no pretrained weights to load. The training
set is rendered at load time from the fonts the machine already has: each digit
is drawn in one of nine families at a random size, rotation, scale and offset,
with a little noise, then recentred by centre of mass the way MNIST centres
its own digits. That last step is why a digit drawn in the corner of the pad
still lands where the network expects it.

## The network

```
input 1×28×28
conv 3×3 → 8×26×26   relu
maxpool 2 → 8×13×13
conv 3×3 → 16×11×11  relu
maxpool 2 → 16×5×5
flatten 400
dense → 10           softmax
```

5,258 parameters. Convolution, max pooling, the dense layer and every gradient
between them are written out by hand: the softmax and cross entropy collapse to
`p - onehot` at the logits, max pooling routes each gradient back to whichever
input won its window, and ReLU zeroes whatever was negative on the way forward.
Optimiser is minibatch SGD with momentum at 0.9.

## Verification

The backward pass is checked against numerical differentiation. Perturbing
individual weights by 1e-3 and comparing the central difference to the analytic
gradient agrees to within 0.02% on weights in all three layers. One sampled
weight disagrees, and it is worth being precise about why: its gradient is about
2.6e-4, small enough that nudging it flips a ReLU on or off, which is the known
failure mode of gradient checking through a kink rather than a fault in the
derivation.

Trained on its own generated data it reaches **98% on the held-out split** in
under a minute at roughly 3,500 samples per second, all on one thread.

## Notes

One HTML file. No libraries, no GPU, no build step.

Built by [bxzex](https://bxzex.com).
