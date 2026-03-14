# Sequence Trainer — Application Specification

A terminal-based drill tool for practicing number sequence recognition. The user is repeatedly shown a number sequence with the final term hidden and must type the correct answer before time runs out.

---

## States

The application cycles through three screens:

```
Configuration → Testing → Results → (back to Configuration)
```

Ctrl+C exits from any screen.

---

## Configuration Screen

The user customizes their session before starting.

### Sections

Navigation moves between sections with **Tab** (forward) and **Shift-Tab** (backward). Within each section, **Up/Down** arrow keys move the cursor. The active section is visually highlighted.

#### 1. Sequence Types

A multi-select checklist of all available sequence types. Each entry shows a checkbox and the type name.

- **Space** or **Enter** toggles the highlighted type on/off.
- At least one type must be enabled to start.

#### 2. Difficulty

Single-select from three options: **Easy**, **Medium**, **Hard**.
Default: **Medium**.

#### 3. Sequence Length

Single-select number of visible terms shown per question.
Options: **5**, **6**, **7**.
Default: **6**.

#### 4. Time Limit

Single-select session duration.
Options: **1 min**, **5 min**, **10 min**, **15 min**.
Default: **5 min**.

#### 5. Start Button

Activating this (Enter or **S** from anywhere) begins the session. Disabled when no types are selected.

### Presets

Two firm-specific presets can be applied at any time with a single key:

| Key | Preset        | Types enabled                                                        |
|-----|---------------|----------------------------------------------------------------------|
| O   | Optiver       | Arithmetic, Geometric, Quadratic, Fibonacci, Tribonacci, Alternating |
| F   | Flow Traders  | Arithmetic, Geometric, Fibonacci, Squares, Cubes, Alternating        |

Applying a preset replaces the current type selection.

### Configuration Keybindings Summary

| Key              | Action                                  |
|------------------|-----------------------------------------|
| Up / Down        | Move cursor within current section      |
| Space            | Toggle item (Types section)             |
| Enter            | Toggle (Types) or activate (Start)      |
| Tab              | Next section                            |
| Shift-Tab        | Previous section                        |
| O                | Apply Optiver preset                    |
| F                | Apply Flow Traders preset               |
| S                | Start session                           |
| Q                | Quit application                        |

---

## Testing Screen

### Layout

- **Status bar** — elapsed time and time remaining.
- **Sequence display** — visible terms separated by spaces, followed by a blank placeholder for the hidden answer.
- **Input field** — text prompt where the user types their answer.
- **Timer gauge** — a progress bar showing remaining time. Color shifts green → yellow → red as time decreases (above 50%, 20–50%, below 20% remaining).

### Session Flow

1. A question is randomly selected from the enabled sequence types.
2. The sequence (visible terms + blank) is displayed.
3. The user types a signed integer answer and presses **Enter** to submit, or **Esc** to skip.
4. Immediately after submit or skip, the next question is shown.
5. When the timer expires the session ends automatically.

### Input Rules

- Only decimal digits and a leading minus sign are accepted.
- **Backspace** deletes the last character.
- A minus sign is only allowed as the first character.

### Testing Keybindings Summary

| Key        | Action                   |
|------------|--------------------------|
| 0–9, -     | Build answer string      |
| Backspace  | Delete last character    |
| Enter      | Submit answer            |
| Esc        | Skip current question    |
| Q          | End session early        |

---

## Results Screen

Displayed after the session ends (by timer expiry or early exit).

### Controls

Three buttons are shown: **Restart**, **Quit**.
Tab / Right and Shift-Tab / Left cycle focus between buttons. Enter or Space activates the focused button.

Direct keyboard shortcuts also work:

| Key | Action                   |
|-----|--------------------------|
| R   | Restart (back to Config) |
| Q   | Quit application         |

---

## Sequence Types

All terms and answers are signed integers. Each sequence is generated fresh and randomly for every question.

### Arithmetic

Each term increases (or decreases) by a constant step.

```
a(n) = start + (n-1) * step
```

- `start` — random positive integer
- `step` — random nonzero integer (may be negative)

| Difficulty | start range | |step| range |
|------------|-------------|-------------|
| Easy       | 1–20        | 1–10        |
| Medium     | 1–100       | 1–20        |
| Hard       | 1–500       | 1–50        |

### Geometric

Each term is multiplied by a constant integer ratio.

```
a(n) = start * ratio^(n-1)
```

- `start` — random positive integer
- `ratio` — random integer ≥ 2
- Reject and regenerate if any term exceeds ±1,000,000 (overflow guard).

| Difficulty | start range | ratio range |
|------------|-------------|-------------|
| Easy       | 1–10        | 2–3         |
| Medium     | 1–20        | 2–4         |
| Hard       | 1–50        | 2–5         |

### Quadratic

Second-order polynomial in position index.

```
a(n) = A*n² + B*n + C    (n = 1, 2, 3, …)
```

| Difficulty | A range | B range | C range |
|------------|---------|---------|---------|
| Easy       | 1–2     | 0–3     | 0–10    |
| Medium     | 1–4     | 0–5     | 0–20    |
| Hard       | 1–6     | 0–10    | 0–50    |

### Fibonacci

Each term is the sum of the two preceding terms.

```
a(1) = seed_a,  a(2) = seed_b,  a(n) = a(n-1) + a(n-2)
```

Both seeds are random positive integers drawn independently from the same range.

| Difficulty | Seed range |
|------------|------------|
| Easy       | 1–5        |
| Medium     | 1–20       |
| Hard       | 1–50       |

### Tribonacci

Each term is the sum of the three preceding terms.

```
a(1..3) = seeds,  a(n) = a(n-1) + a(n-2) + a(n-3)
```

Three independent seeds drawn from the same range.

| Difficulty | Seed range |
|------------|------------|
| Easy       | 1–3        |
| Medium     | 1–10       |
| Hard       | 1–20       |

### Triangular

Consecutive triangular numbers starting from a random offset.

```
T(n) = n*(n+1)/2
```

The sequence is a window of consecutive T(n) values starting at a random `n = start_n`.

| Difficulty | start_n range |
|------------|---------------|
| Easy       | 1–4           |
| Medium     | 1–8           |
| Hard       | 1–15          |

### Squares (n²)

Consecutive perfect squares starting from a random offset.

```
a(i) = (start_n + i - 1)²
```

| Difficulty | start_n range |
|------------|---------------|
| Easy       | 1–4           |
| Medium     | 1–8           |
| Hard       | 1–12          |

### Cubes (n³)

Consecutive perfect cubes starting from a random offset.

```
a(i) = (start_n + i - 1)³
```

| Difficulty | start_n range |
|------------|---------------|
| Easy       | 1–3           |
| Medium     | 1–5           |
| Hard       | 1–8           |

### Alternating

Two independent arithmetic sub-sequences interleaved by position parity.

```
Even-indexed positions (0, 2, 4, …): A_start + k * A_step   (k = 0, 1, 2, …)
Odd-indexed positions  (1, 3, 5, …): B_start + k * B_step   (k = 0, 1, 2, …)
```

- `A_step` — random 1–5
- `B_step` — random 5–20

| Difficulty | A_start range | B_start range |
|------------|---------------|---------------|
| Easy       | 1–15          | 10–50         |
| Medium     | 1–30          | 10–100        |
| Hard       | 1–100         | 10–200        |

---

## Question Structure

Each question consists of:

- **visible_terms** — an ordered list of integers (length = configured sequence length)
- **answer** — the single integer that follows immediately after the visible terms

The answer is never shown during testing; it is only used internally to check correctness.

---

## Default Configuration

| Setting          | Default  |
|------------------|----------|
| Sequence types   | All nine |
| Difficulty       | Medium   |
| Sequence length  | 6 terms  |
| Time limit       | 5 min    |
