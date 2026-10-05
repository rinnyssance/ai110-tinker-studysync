# StudySync — Testing & Refactoring

This project was completed as part of **CodePath AI110**. The goal of this assignment was to practice writing tests, refactoring existing Python code, working with modules, and testing unusual inputs without changing the intended behavior of the program.

## Project Overview

StudySync is a small Python application that scores study sessions.

A study session receives a combined score based on study time and focus. The program then applies a streak bonus and assigns the session a rating:

- **Great**
- **Good**
- **OK**
- **Meh**
- **Skip**

Rather than building the application from scratch, this assignment focused on working with an existing codebase and improving its structure safely.

## What I Did

### 1. Tested the Existing Code

Before changing the program, I ran the existing pytest test to establish a working baseline.

The starter project already tested whether a score of `90` returned `"Great"`.

```python
def test_session_rating_boundary_90_is_great():
    assert session_rating(90) == "Great"
```

I added another boundary test to verify that a score of `80` returns `"Good"`:

```python
def test_session_rating_boundary_80_is_good():
    assert session_rating(80) == "Good"
```

After adding my test, I ran:

```bash
python -m pytest
```

Both tests passed:

```text
2 passed
```

This gave me a baseline before refactoring the program.

### 2. Refactored the Scoring Logic

The `apply_streak_bonus()` function originally lived inside `scoring.py`.

Although the function worked correctly, the assignment required moving it into the shared `scoring_helpers.py` module.

I moved:

```python
def apply_streak_bonus(combined_score: int, streak_days: int) -> int:
    boosted = combined_score + streak_days * 2
    return min(boosted, 100)
```

into:

```text
scoring_helpers.py
```

Then I imported it into `scoring.py`:

```python
from scoring_helpers import apply_streak_bonus
```

This allowed the rest of the program to continue using `apply_streak_bonus()` without changing how the application behaved.

After the refactor, I ran the application and the tests again to make sure everything still worked.

```bash
python scoring.py
python -m pytest
```

The tests continued to report:

```text
2 passed
```

## Breaker Inputs

I also experimented with unusual values to see how `session_rating()` behaved outside of its normal expected inputs.

I tried:

```python
session_rating(-10)
session_rating(150)
session_rating(87.5)
```

None of these inputs crashed the program.

The results showed that the current function accepts values outside a typical `0–100` score range:

- `-10` is classified as `"Skip"`
- `150` is classified as `"Great"`
- `87.5` is classified as `"Good"`

The decimal input worked reasonably with the existing comparison logic. However, the negative and above-100 values showed that `session_rating()` currently does not validate its input range.

For this assignment, I left the behavior unchanged because the goal was to investigate the edge cases and decide whether additional handling would be necessary rather than automatically changing working code.

## Challenges I Ran Into

One challenge happened while moving `apply_streak_bonus()`.

After adding this import to `scoring.py`:

```python
from scoring_helpers import apply_streak_bonus
```

I initially received:

```text
ImportError: cannot import name 'apply_streak_bonus' from 'scoring_helpers'
```

Pytest also failed during test collection because importing `scoring.py` triggered the same error.

The issue helped me understand that adding an import is not enough—the function actually has to exist in the module being imported.

After making sure `apply_streak_bonus()` was correctly placed in `scoring_helpers.py` and saving the file, I ran the program and pytest again.

Both tests passed.

This was useful because I got to see how a problem in one module can prevent tests in another file from even being collected.

## What I Learned

This assignment helped me understand the difference between **changing code behavior** and **refactoring code structure**.

I practiced:

- Writing pytest tests with `assert`
- Testing boundary values
- Establishing tests before refactoring
- Moving functions between Python modules
- Importing functions from another file
- Debugging an `ImportError`
- Testing edge cases
- Using tests to verify that a refactor did not break existing behavior

The biggest takeaway for me was that tests are useful before making changes, not just afterward. Having passing tests before the refactor gave me something concrete to compare against after I reorganized the code.

## Project Structure

```text
ai110-tinker-studysync/
├── scoring.py
├── scoring_helpers.py
├── test_scoring.py
├── requirements.txt
└── README.md
```

## Running the Project

Create and activate a virtual environment:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

Install the dependencies:

```powershell
pip install -r requirements.txt
```

Run the program:

```powershell
python scoring.py
```

Run the tests:

```powershell
python -m pytest
```

## Author

**Erin Joel Moore**  
GitHub: [@rinnyssance](https://github.com/rinnyssance)

---

*Completed for CodePath AI110.*
