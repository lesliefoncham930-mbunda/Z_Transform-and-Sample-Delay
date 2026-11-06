clear; clc; close all;

x = [1 2 1];
n = 0:length(x)-1;

% Delay the signal by one sample
xDelayed = [0 x];
nDelayed = 0:length(xDelayed)-1;

figure;
subplot(2,1,1);
stem(n, x, 'filled');
title('Original Signal x[n]');
xlabel('Sample n'); ylabel('Amplitude');
grid on;

subplot(2,1,2);
stem(nDelayed, xDelayed, 'filled');
title('One-Sample Delay x[n-1]');
xlabel('Sample n'); ylabel('Amplitude');
grid on;
<img width="1920" height="1080" alt="Screenshot (134)" src="https://github.com/user-attachments/assets/9124a8a8-e0b0-497e-beb8-f4ccdcaf6aab" />

<img width="1920" height="1080" alt="Screenshot (134)" src="https://github.com/user-attachments/assets/f4410898-572e-4d85-a19d-49826b6e983c" />
# Z_Transform-and-Sample-Delay
# Z-Transform Lab: Signal Delay and a Two-Tap Averaging System

Author: Leslie Foncham Mbunda
Tools: MATLAB 

## Files

| File | Description |
|------|-------------|
| `ztransform_lab.m` | Main script: Part 1 (signal + delay), Part 2 (H(z) = 0.5 + 0.5z⁻¹) and Task 6 (H(z) = 0.8 + 0.2z⁻¹). Saves all figures to `screenshots/`. |
| `screenshots/` | Original, delayed and output signal plots. |

**How to run:** open `ztransform_lab.m` in MATLAB and press **Run**. Make sure a `screenshots` folder exists next to the script.

---

## Part 1: A signal and its Z-transform

x[0] = 1, x[1] = 2, x[2] = 1  →  X(z) = 1 + 2z⁻¹ + z⁻²

![Original and delayed signal](screenshots/part1_original_delayed.png)

### Q1. Which sample does each term in X(z) represent?

The Z-transform is X(z) = Σ x[n]·z⁻ⁿ. The coefficient is the sample value, and the power of z⁻¹ gives its position in time.

| Term | Coefficient | Power of z⁻¹ | Sample |
|------|-------------|--------------|--------|
| 1 (= 1·z⁰) | 1 | 0 | x[0] = 1 |
| 2z⁻¹ | 2 | 1 | x[1] = 2 |
| z⁻² (= 1·z⁻²) | 1 | 2 | x[2] = 1 |

### Q2. Z-transform of the delayed signal

The delayed signal is x[n−1] = {0, 1, 2, 1} for n = 0, 1, 2, 3, so

Z{x[n−1]} = 0 + 1·z⁻¹ + 2z⁻² + 1·z⁻³ = **z⁻¹ + 2z⁻² + z⁻³ = z⁻¹·X(z)**

### Q3. Why does a one-sample delay multiply X(z) by z⁻¹?

The exponent of z⁻¹ marks where each sample sits in time. Delaying the signal by one sample moves every sample one position later, from n to n+1. Each term x[n]·z⁻ⁿ therefore becomes x[n]·z⁻⁽ⁿ⁺¹⁾ = z⁻¹·x[n]·z⁻ⁿ. Every term picks up the same extra z⁻¹, so it factors out of the whole sum:

Z{x[n−1]} = Σ x[n−1]z⁻ⁿ = Σ x[k]z⁻⁽ᵏ⁺¹⁾ = z⁻¹·Σ x[k]z⁻ᵏ = z⁻¹X(z)  (with k = n−1)

So z⁻¹ works as the "one-sample delay" operator. The plot shows this too: the bottom stem plot is the same shape, shifted one sample to the right.

---

## Part 2: A system with a one-sample delay

y[n] = 0.5x[n] + 0.5x[n−1]  →  H(z) = 0.5 + 0.5z⁻¹

![Input and output for H(z) = 0.5 + 0.5z^-1](screenshots/part2_output_05_05.png)

### Q4. Hand calculation of y[0] to y[3]

Input (with the extra zero): x = {1, 2, 1, 0}, and x[−1] = 0.

| n | 0.5·x[n] | 0.5·x[n−1] | y[n] |
|---|----------|------------|------|
| 0 | 0.5·1 = 0.5 | 0.5·0 = 0 | **0.5** |
| 1 | 0.5·2 = 1.0 | 0.5·1 = 0.5 | **1.5** |
| 2 | 0.5·1 = 0.5 | 0.5·2 = 1.0 | **1.5** |
| 3 | 0.5·0 = 0 | 0.5·1 = 0.5 | **0.5** |

MATLAB output:

```
Output samples for H(z) = 0.5 + 0.5z^-1:
    0.5000    1.5000    1.5000    0.5000
```

The hand calculation and MATLAB give the same result. The output is a smoothed version of the input: the peak of 2 is spread over two samples (1.5, 1.5), and the signal is one sample longer because of the delay term.

### Q5. How `b = [0.5 0.5]` represents H(z)

In `filter(b, a, x)`, the vector `b` holds the numerator coefficients of H(z) in increasing powers of z⁻¹:

H(z) = b(1) + b(2)·z⁻¹ + b(3)·z⁻² + …

- `b(1) = 0.5` is the coefficient of z⁰, which weights the **current** input x[n].
- `b(2) = 0.5` is the coefficient of z⁻¹, which weights the **previous** input x[n−1].

`a = 1` means there is no feedback (the denominator is just 1), so the system is an FIR filter.

### Q6. Changing to H(z) = 0.8 + 0.2z⁻¹

Code change: `b = [0.8 0.2];` (in the script this is `b2`, so both versions run together).

![Input and output for H(z) = 0.8 + 0.2z^-1](screenshots/part2_output_08_02.png)

| n | 0.8·x[n] | 0.2·x[n−1] | y[n] |
|---|----------|------------|------|
| 0 | 0.8 | 0 | **0.8** |
| 1 | 1.6 | 0.2 | **1.8** |
| 2 | 0.8 | 0.4 | **1.2** |
| 3 | 0 | 0.2 | **0.2** |

MATLAB output:

```
Output samples for H(z) = 0.8 + 0.2z^-1:
    0.8000    1.8000    1.2000    0.2000
```

The **current input x[n]** now has more influence on the output, because its weight is 0.8 compared with 0.2 for the previous input x[n−1]. As a result, the output follows the input shape much more closely: the peak stays at n = 1 (1.8, close to the original 2), and the "tail" at n = 3 is small (0.2). With equal weights (0.5/0.5) the system was a true average that smeared the peak over two samples. With 0.8/0.2 the smoothing is weaker. The coefficients still add up to 1, so a constant input passes through with the same level (DC gain H(1) = 1).
