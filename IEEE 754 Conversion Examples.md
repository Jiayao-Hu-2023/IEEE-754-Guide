# Understanding IEEE 754 Floating-Point Conversion
## 🧮 A Step-by-Step Guide with Examples (Single & Double Precision)

> 📘 **Target Audience:** Computer science students, developers, and anyone curious about how computers represent real numbers.
>
> 👨‍🎓 **Author:** Jiayao Hu

[English Version](<IEEE 754 Conversion Examples.md>) | [中文版本](<IEEE 754 Conversion Examples zh.md>)

---

## 1. 🎯 What is IEEE 754?

The **IEEE 754 Standard** defines how floating-point numbers are stored in computers. It specifies two common formats:

| Format | Total Bits | Sign | Exponent | Mantissa (Fraction) | Bias |
|--------|-----------|------|----------|---------------------|------|
| **Single Precision (SP)** | 32 | 1 bit | 8 bits | 23 bits | 127 |
| **Double Precision (DP)** | 64 | 1 bit | 11 bits | 52 bits | 1023 |

The general formula for the value represented is:

$$
\text{Value} = (-1)^{\text{sign}} \times 1.\text{mantissa} \times 2^{\text{exponent} - \text{bias}}
$$

> 💡 **Key Insight:** The leading `1.` in the mantissa is *implicit* (hidden bit) for normalized numbers. We only store the fractional part after the binary point.

---

## 2. 🔄 The Conversion Algorithm: Decimal → IEEE 754

To convert a decimal number to IEEE 754 format, follow these steps:

### Step-by-Step Process

1. **🔢 Convert the absolute value to binary**
   - Convert the integer part using repeated division by 2.
   - Convert the fractional part using repeated multiplication by 2.

2. **📐 Normalize the binary number**
   - Shift the binary point so that exactly one `1` appears to the left of the point.
   - Express as: $1.\text{fraction} \times 2^{\text{exponent}}$

3. **➕ Determine the fields**
   - **Sign bit:** `0` for positive, `1` for negative.
   - **Exponent:** Add the bias (127 for SP, 1023 for DP) to the true exponent, then convert to binary.
   - **Mantissa:** Take the fractional part after the leading `1.`, pad with zeros to fill 23 bits (SP) or 52 bits (DP).

4. **🧩 Assemble the binary string**
   - Concatenate: `[Sign][Exponent][Mantissa]`

---

## 3. 📚 Example 1: Converting `9.75`

Let's convert the decimal number $9.75$ to both Single Precision and Double Precision.

### Step 1: Convert to Binary

- Integer part: $9_{10} = 1001_2$
- Fractional part: $0.75 \times 2 = 1.5$ → `1`
  - $0.5 \times 2 = 1.0$ → `1`
  - $0.0$ → stop

So:

$$
9.75_{10} = 1001.11_2
$$

### Step 2: Normalize

$$
1001.11_2 = 1.00111_2 \times 2^3
$$

Here, the **true exponent** is $E = 3$.

### Step 3: Determine Fields

| Field | Calculation | Value |
|-------|-------------|-------|
| **Sign** | Positive | `0` |
| **SP Exponent** | $3 + 127 = 130$ | `10000010` |
| **DP Exponent** | $3 + 1023 = 1026$ | `10000000010` |
| **Mantissa** | `00111` + padding | `00111000000000000000000` (SP) / `0011100...0` (DP, 52 bits) |

### Step 4: Assemble

**Single Precision (32-bit):**

$$
\underbrace{0}_{\text{Sign}}\ \underbrace{10000010}_{\text{Exp}}\ \underbrace{00111000000000000000000}_{\text{Mantissa}}
$$

**Hex:** `0x41180000`

**Double Precision (64-bit):**

$$
\underbrace{0}_{\text{Sign}}\ \underbrace{10000000010}_{\text{Exp}}\ \underbrace{0011100000000000000000000000000000000000000000000000}_{\text{Mantissa}}
$$

**Hex:** `0x4023800000000000`

> ✅ **Verification:** $(-1)^0 \times 1.00111_2 \times 2^3 = 1001.11_2 = 9.75_{10}$ ✓

---

## 4. 📚 Example 2: Converting `-42.625`

Now let's handle a **negative** number with both integer and fractional parts.

### Step 1: Convert to Binary

- Integer part: $42_{10} = 101010_2$
- Fractional part: $0.625 \times 2 = 1.25$ → `1`
  - $0.25 \times 2 = 0.5$ → `0`
  - $0.5 \times 2 = 1.0$ → `1`

So:

$$
42.625_{10} = 101010.101_2
$$

### Step 2: Normalize

$$
101010.101_2 = 1.01010101_2 \times 2^5
$$

The **true exponent** is $E = 5$.

### Step 3: Determine Fields

| Field | Calculation | Value |
|-------|-------------|-------|
| **Sign** | Negative | `1` |
| **SP Exponent** | $5 + 127 = 132$ | `10000100` |
| **DP Exponent** | $5 + 1023 = 1028$ | `10000000100` |
| **Mantissa** | `01010101` + padding | `01010101000000000000000` (SP) / `0101010100...0` (DP, 52 bits) |

### Step 4: Assemble

**Single Precision (32-bit):**

$$
\underbrace{1}_{\text{Sign}}\ \underbrace{10000100}_{\text{Exp}}\ \underbrace{01010101000000000000000}_{\text{Mantissa}}
$$

**Hex:** `0xC22A8000`

**Double Precision (64-bit):**

$$
\underbrace{1}_{\text{Sign}}\ \underbrace{10000000100}_{\text{Exp}}\ \underbrace{0101010100000000000000000000000000000000000000000000}_{\text{Mantissa}}
$$

**Hex:** `0xC045500000000000`

> ✅ **Verification:** $(-1)^1 \times 1.01010101_2 \times 2^5 = -101010.101_2 = -42.625_{10}$ ✓

---

## 5. 📚 Example 3: Converting `0.15625`

This example demonstrates handling a **pure fraction** (value < 1) with a **negative exponent**.

### Step 1: Convert to Binary

- Integer part: $0_{10} = 0_2$
- Fractional part:
  - $0.15625 \times 2 = 0.3125$ → `0`
  - $0.3125 \times 2 = 0.625$ → `0`
  - $0.625 \times 2 = 1.25$ → `1`
  - $0.25 \times 2 = 0.5$ → `0`
  - $0.5 \times 2 = 1.0$ → `1`

So:

$$
0.15625_{10} = 0.00101_2
$$

### Step 2: Normalize

$$
0.00101_2 = 1.01_2 \times 2^{-3}
$$

The **true exponent** is $E = -3$.

### Step 3: Determine Fields

| Field | Calculation | Value |
|-------|-------------|-------|
| **Sign** | Positive | `0` |
| **SP Exponent** | $-3 + 127 = 124$ | `01111100` |
| **DP Exponent** | $-3 + 1023 = 1020$ | `01111111100` |
| **Mantissa** | `01` + padding | `01000000000000000000000` (SP) / `010000...0` (DP, 52 bits) |

### Step 4: Assemble

**Single Precision (32-bit):**

$$
\underbrace{0}_{\text{Sign}}\ \underbrace{01111100}_{\text{Exp}}\ \underbrace{01000000000000000000000}_{\text{Mantissa}}
$$

**Hex:** `0x3E200000`

**Double Precision (64-bit):**

$$
\underbrace{0}_{\text{Sign}}\ \underbrace{01111111100}_{\text{Exp}}\ \underbrace{0100000000000000000000000000000000000000000000000000}_{\text{Mantissa}}
$$

**Hex:** `0x3FC4000000000000`

> ✅ **Verification:** $1.01_2 \times 2^{-3} = 0.00101_2 = 0.15625_{10}$ ✓

---

## 6. 🔄 Converting IEEE 754 Back to Decimal

Now let's go in the opposite direction! Given a hex representation of an IEEE 754 number, how do we recover the decimal value?

### The Algorithm

1. **🔍 Convert hex to binary** and split into [Sign | Exponent | Mantissa].
2. **📊 Calculate the true exponent:** $\text{True Exp} = \text{Binary Exponent} - \text{Bias}$
3. **🔢 Reconstruct the significand:** $1.\text{mantissa}_2$ (insert the hidden bit).
4. **🧮 Apply the formula:**

$$
\text{Value} = (-1)^{\text{sign}} \times (1 + \sum_{i=1}^{n} m_i \cdot 2^{-i}) \times 2^{\text{true exponent}}
$$

---

## 7. 📚 Example 4: SP → Decimal (`0xC1480000`)

### Step 1: Convert to Binary

$$
\text{0xC1480000} = 1100\ 0001\ 0100\ 1000\ 0000\ 0000\ 0000\ 0000_2
$$

Split into fields:
- **Sign:** `1` (bit 31)
- **Exponent:** `10000010` (bits 30–23)
- **Mantissa:** `10010000000000000000000` (bits 22–0)

### Step 2: Calculate True Exponent

$$
\text{Exponent}_{\text{binary}} = 10000010_2 = 130_{10}
$$

$$
\text{True Exponent} = 130 - 127 = 3
$$

### Step 3: Reconstruct the Significand

With the hidden bit:

$$
1.10010000000000000000000_2
$$

### Step 4: Calculate the Value

$$
\text{Value} = (-1)^1 \times 1.1001_2 \times 2^3 = -1100.1_2
$$

Converting to decimal:

$$
-1100.1_2 = -(8 + 4 + 0.5) = \boxed{-12.5_{10}}
$$

> 🎉 Result: `0xC1480000` (SP) represents **-12.5**

---

## 8. 📚 Example 5: DP → Decimal (`0x4024000000000000`)

### Step 1: Convert to Binary

$$
\text{0x4024000000000000} = 0100\ 0000\ 0010\ 0100\ 0000\ ...\ 0000_2
$$

Split into fields:
- **Sign:** `0` (bit 63)
- **Exponent:** `10000000010` (bits 62–52)
- **Mantissa:** `0100000000000000000000000000000000000000000000000000` (bits 51–0)

### Step 2: Calculate True Exponent

$$
\text{Exponent}_{\text{binary}} = 10000000010_2 = 1026_{10}
$$

$$
\text{True Exponent} = 1026 - 1023 = 3
$$

### Step 3: Reconstruct the Significand

With the hidden bit:

$$
1.0100000000000000000000000000000000000000000000000000_2
$$

### Step 4: Calculate the Value

$$
\text{Value} = (-1)^0 \times 1.01_2 \times 2^3 = 1010_2
$$

Wait — let's be more precise. The mantissa is `01`, so:

$$
1.01_2 = 1 + 0 \times 2^{-1} + 1 \times 2^{-2} = 1 + 0.25 = 1.25_{10}
$$

Then:

$$
1.25 \times 2^3 = 1.25 \times 8 = \boxed{10.0_{10}}
$$

Actually, let's double-check: $1.01_2 \times 2^3 = 1010_2 = 8 + 2 = 10_{10}$. ✓

> 🎉 Result: `0x4024000000000000` (DP) represents **10.0**

---

## 9. 📝 Quick Reference Cheat Sheet

| Task | Formula / Rule |
|------|----------------|
| **SP Bias** | $127$ |
| **DP Bias** | $1023$ |
| **SP Exponent Bits** | $8$ (range: $-126$ to $+127$) |
| **DP Exponent Bits** | $11$ (range: $-1022$ to $+1023$) |
| **Normalized Form** | $1.\text{ffff} \times 2^{E}$ |
| **Decimal Value** | $(-1)^{S} \times (1 + M) \times 2^{E - \text{Bias}}$ |

### Special Values to Remember 🚨

| Pattern | Meaning |
|---------|---------|
| Exponent = all 0s, Mantissa = 0 | $\pm 0$ |
| Exponent = all 0s, Mantissa $\neq$ 0 | Denormalized number |
| Exponent = all 1s, Mantissa = 0 | $\pm \infty$ |
| Exponent = all 1s, Mantissa $\neq$ 0 | NaN (Not a Number) |

---

## 10. 🎯 Summary

| Example | Decimal | SP Hex | DP Hex |
|---------|---------|--------|--------|
| 1 | $9.75$ | `0x41180000` | `0x4023800000000000` |
| 2 | $-42.625$ | `0xC22A8000` | `0xC045500000000000` |
| 3 | $0.15625$ | `0x3E200000` | `0x3FC4000000000000` |
| 4 | $-12.5$ | `0xC1480000` | — |
| 5 | $10.0$ | — | `0x4024000000000000` |

> 💻 **Pro Tip:** Use Python's `struct.pack('!f', value)` or online IEEE 754 converters to verify your manual calculations!

---

*If you found this guide helpful, give it a ⭐ on GitHub!*
