# Coding

I'm using this repo README as a landing page for my twitch stream https://www.twitch.tv/kalicoding. 
Usually I'll be working on https://github.com/sonos/tract

Past broadcasts on https://www.youtube.com/channel/UCHzHGOiKEAalDk5atWpBLmg

# Current work: Activation functions VM
 * Youtube playlist: https://www.youtube.com/playlist?list=PL2L_7bZXXxQuNpcxjg8gipegP32pmwVZF
 * tract issue: https://github.com/sonos/tract/pull/1008 

Computing neural networks efficiently requires to evaluate "activation functions". They are simple functions operating on floats. They are your basic mid-school math function: y = f(x). Each value in the output tensor is the result of evaluating the function on the value at that same place in the input tensor. So basically, you can ignore all tensor geometry, they are a simple `foreach` looping over a math expression, which makes them great candidates for SIMD optimisation.

But we don't want to write all of them in assembly for all architectures. So we're doing a Virtual Machine. It will run a "microcode" over a slice of values. Each activation function must be translated to microcode only once, and the microcode interpreter must be implemented on top of each architecture of interest, only once.

The tract issue https://github.com/sonos/tract/pull/1008 comments contains
* comments contains an inventory of common activation functions (from ONNX operator list https://github.com/onnx/onnx/blob/main/docs/Operators.md)
* an early translation of them using a couple of simple operators that map to most SIMD architectures

* List of the operators in our microcode. here: https://github.com/sonos/tract/blob/08e0c52105394fcadb393b3bd8fa2faa30c8045a/linalg/src/frame/activations.rs#LL27C2-L27C2
* Rust POC implementation of the interpreter https://github.com/sonos/tract/blob/08e0c52105394fcadb393b3bd8fa2faa30c8045a/linalg/src/generic/activations.rs#L4
* "microcode" for the main activations functions https://github.com/sonos/tract/blob/08e0c52105394fcadb393b3bd8fa2faa30c8045a/linalg/src/frame/activations/definitions.rs

