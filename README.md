# Coding

I'm using this repo README as a landing page for my twitch stream https://www.twitch.tv/kalicoding. 
Usually I'll be working on https://github.com/sonos/tract

# Activation functions

* We are interested in Element wise operations:
  * result is independent of tensor geometry (only accept scalar operations, no broadcasting of tensors)
  * output = f(x) for x in input 

* "simple ops + if/then/else"

  * Relu(x) = max(0, x)
  * Affine = alpha * x + beta
  * LeakyRelu = if x >= 0 { x } else { alpha * x }
  * ThresholdRelu = if x >= alpha { x } else { 0 }
  * HardSigmoid = min(max(alpha * x + beta), 0, 1)
  * Sofsign = x / (1 + abs(x))
  * HashSwish = x * max(0(min(1, alpha * x + beta))) (alpha = 1/6 and beta 1/2)
 
## Proposed virtual machine def

 * 4 big registers (mapped to one or several hardware vector registers)
   * armv8, maybe one big register may be 4 NEON registers (4*4 f32 values or 4*8 f16 values)
   * 16 NEON registers are used, 16 for housekeeping (pre-fetch caching + operators)
 * focusing on "simple" batch
   * BigRegs are a,b,c,d
   * min, max, abs, +, *, /, ite, ifpos
   * constants
 * calling convention: framework preloads x into a, expects y in a

## Moves, load consts to any reg, unary, binary, ternary on a,b,c only

 * Relu: load(b, 0) | max
 * Affine: load(b, alpha) | mul | load(v, beta) | add
 * LeakyRelu: b <- a | load(a, alpha) | mul | c <- a | ifpos
 * ThresholdRelu: c <- a | load(b, alpha) | sub | b <- c | load(c, 0) | ifpos
 * Softsign: c <- a | abs | load(b, 1) | add | recip | b <- c | mul
 * HardSwish: load(b, alpha) | mul | load(b, beta) | add | load(b, 1) | min | load(b, 0) | max

## With a <- a#K (K constant)

 * Relu: max(0)
 * Affine: mul(alpha) | add(beta)
 * LeakyRelu: b <- a | mul(alpha) | c <- a | ifpos
 * ThresholdRelu: b <- a | sub(alpha) | load(c, 0) | ifpos
 * Softsign: b <- a | abs | add(1) | recip | mul
 * HardSwish: mul(alpha) | add(beta) | min(0) | max(1)

# combination of operands
   * all moves 
      * 16 moves ops (any to any)
      * other ops have all fixed registers: unary ops a <- a, binary a <- a#b, ternary a <- a#b#c
   * all operands can be const or any registers
      * with 4 big resisters, if a then b else c needs 64 combination
   * ifte (a <- cond in a, then in b, else in c)

* Complicated activations functions
  * computed from exp, log, tang, etc as seperate operators

  * ScaledTanh = alpha * tanh(beta * x)
  * Softplus = log(1 + e^x)
  * Celu = max(0,x) + min(0,alpha * exp(x / alpha) - 1)
  * Elu = if x < 0 { alpha * (exp(x) - 1) } else { x }
  * Selu = if x < 0 { gamma * (alpha * e^x - alpha) } else { gamma * x }
  * Mish(x) = x * tanh(softplus(x)) = x * tanh(ln(1 * e^x))

## Current state of tract implementation in assembly on armv8, armv7, intel fma

    Tanh(x) - (1 - e^{-2x})/(1 + e^{-2x})
    Sigmoid(x) - 1/(1 + e^{-x})
    Erf(x)
    * rational approximation:
      P(x)/Q(x) where P and Q are polys
    * Do we want to support rational function evaluation ?

## Can we make a faster exp ? what about log ?
  *  e^x <-       
