# Coding

I'm using this repo README as a landing page for my twitch stream https://www.twitch.tv/kalicoding. 
Usually I'll be working on https://github.com/sonos/tract

# Activation functions

* We are interested in Element wise operations:
  * result is independent of tensor geometry (only accept scalar operations, no broadcasting of tensors)
  * output = f(x) for x in input 
  
  * Relu(x) = max(0, x)
     * iteration in Rust
     * fused assembly implementation
        (if Relu follows a linear op, it is fused with the linear op)
  * Affine = alpha * x + beta
  * LeakyRelu = if x >= 0 { x } else { alpha * x}
  * ThresholdRelu = if x >= alpha { x } else { 0 }
  * ScaledTanh = alpha * tanh(beta * x)
  * HardSigmoid = min(max(alpha * x + beta), 0, 1)
  * Sofsign = x / (1 + abs(x))
  * Softplus = log(1 + e^x)
  * Celu = max(0,x) + min(0,alpha * exp(x / alpha) - 1)
  * Elu = if x < 0 { alpha * (exp(x) - 1) } else { x }
  * HashSwish = x * max(0(min(1, alpha * x + beta))) (alpha = 1/6 and beta 1/2)
  * Selu = if x < 0 { gamma * (alpha * e^x - alpha) } else { gamma * x }
  * Mish(x) = x * tanh(softplus(x)) = x * tanh(ln(1 * e^x))
    
## Tract implementation in assembly on armv8, armv7, intel fma
    Tanh(x) - (1 - e^{-2x})/(1 + e^{-2x})
    Sigmoid(x) - 1/(1 + e^{-x})
    Erf(x)
    * rational approximation:
      P(x)/Q(x) where P and Q are polys
    * Do we want to support rational function evaluation ?

## Can we make a faster exp ? what about log ?
  *  e^x <-       
