
# Modified SHA-3 ASIC-Resistant Design – ChatGPT Conversation Summary

## 🎯 Objective
Design a modified SHA-3 permutation core for a cryptocurrency that:
- **Performs efficiently on FPGAs**
- **Is extremely power-inefficient on ASICs**
- Uses **per-epoch customization** to dynamically change the 100-bit permutation logic.

---

## 🔍 Key Concepts Discussed

### Quadratic Crossbar Cost
- **Crossbar power/capacitance cost grows O(n²)**.
- Implementing a **1600×1600-bit permutation** is 256× more power-hungry than a 100×100 one.

### FPGA Advantage
- FPGAs handle small dynamic routing tasks easily.
- ASICs can't afford to hardwire for dynamic, random 100-bit sub-permutations.

---

## 🧠 Anti-ASIC Strategies

### ✅ Strategy 1: 100-Bit Wire Permutation
- After SHA-3’s χ layer, insert a 100-bit wire permutation that:
  - Randomly selects 100 wires out of 1600
  - Shuffles them in a new pattern per epoch
- Forces ASICs to build or emulate a massive 1600×1600 crossbar.

### ✅ Strategy 2: Use of SHA-3 θ and χ
- θ: Global parity trees (wide fan-in/fan-out) → breaks localized processing.
- χ: 5-bit nonlinear S-box → prevents bitwise serialization.
- When you scramble outputs between χ and θ → **hard serialization barrier**.

---

## 🔧 Implementation Plan

### Modified Round Structure
```
[θ] → [ρπ] → [χ (with possible shuffle)] → [100-bit wire permutation] → [θ]
```

- χ may be shuffled or customized per round.
- The 100-bit permutation is generated per epoch (Python script).
- θ mixes the whole state again, ensuring global nonlocality.

---

## 🛠️ Python VHDL Generator

The script:
- Selects 100 random bit indices
- Shuffles them and emits VHDL assignments
- Preserves unchanged wiring for other bits

### Sample Output Format
```vhdl
permuted(77) <= chi_out(921);
permuted(200) <= chi_out(31);
-- ...remaining unchanged bits
permuted(12) <= chi_out(12);
```
Used between the χ and θ stages.

---

## ✅ Result

### FPGA:
- Re-synthesized once per epoch
- Static logic → minimal runtime cost

### ASIC:
- Forced to choose between:
  - Huge 1600×1600 switch fabric (power)
  - Complex reconfiguration (slow)
  - Time-serialization (blocked by data dependencies)

---

## 📁 Files Created

- `generate_sha3_custom_round.py`: Python script to generate VHDL
- `sha3_custom_round.vhdl`: Output per-epoch modified SHA-3 round file

---

## 🔄 Next Steps

- Add full θ and Keccak permutation if needed
- Integrate into full mining core
- Automate epoch-to-synthesis workflow
