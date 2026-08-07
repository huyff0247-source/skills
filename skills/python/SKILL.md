---
name: python
description: Python best practices: typing, async, project structure, testing, packaging. Use when writing Python code, scripts, or packages.
trigger: \bPython\b ;; \bpython\b ;; \bpip\b ;; \bvenv\b ;; \bpoetry\b ;; \bpytest\b ;; \basyncio\b ;; \basync def\b ;; \bFastAPI\b ;; \bDjango\b ;; \bFlask\b ;; \btype hint\b ;; \bdataclass\b ;; \bpydantic\b ;; viết Python ;; Python script
---

# Python Best Practices (Compact)

## Project Structure
```
myproject/
├── src/myproject/     # Package source
│   ├── __init__.py
│   ├── core.py
│   └── cli.py
├── tests/
│   ├── test_core.py
│   └── conftest.py    # Shared fixtures
├── pyproject.toml     # Config (replaces setup.py)
├── README.md
└── .gitignore
```

## Type Hints (Always)
```python
from typing import Optional, Union

def get_user(user_id: int) -> Optional[dict]:
    ...

# Python 3.10+: use | for Union
def process(data: dict[str, int] | None) -> list[str]:
    ...

# Protocol for duck typing
from typing import Protocol
class Readable(Protocol):
    def read(self) -> str: ...

def consume(source: Readable) -> None: ...
```

## Async/Await
```python
import asyncio

# ❌ Blocking in async
async def bad():
    time.sleep(1)  # blocks event loop!

# ✅ Non-blocking
async def good():
    await asyncio.sleep(1)

# Run CPU-bound work in thread pool
result = await asyncio.to_thread(cpu_heavy_function, arg1)

# Gather concurrent tasks
users, orders = await asyncio.gather(
    fetch_users(),
    fetch_orders(),
)
```

## Pydantic for Data Validation
```python
from pydantic import BaseModel, Field, field_validator

class OrderCreate(BaseModel):
    user_id: int
    items: list[str] = Field(min_length=1)
    quantity: int = Field(gt=0, le=100)

    @field_validator("items")
    @classmethod
    def no_duplicates(cls, v: list[str]) -> list[str]:
        if len(v) != len(set(v)):
            raise ValueError("Duplicate items not allowed")
        return v
```

## Testing
```python
import pytest
from unittest.mock import AsyncMock

# Fixtures with scope
@pytest.fixture(scope="session")
def db():
    return create_test_db()

# Parametrize
@pytest.mark.parametrize("qty,expected", [(1, True), (0, False), (-1, False)])
def test_validate_quantity(qty, expected):
    assert validate_quantity(qty) == expected

# Async tests
@pytest.mark.asyncio
async def test_fetch_user(mock_db):
    mock_db.get.return_value = {"id": 1}
    result = await fetch_user(1, db=mock_db)
    assert result["id"] == 1
```

## Anti-patterns
- Mutable default arguments: `def f(items=[])` → `def f(items=None): items = items or []`
- Bare `except:` → `except Exception:` at minimum.
- `from module import *` — pollutes namespace, breaks tooling.
- `requirements.txt` without pinned versions → `package==1.2.3`.
- Mixing sync and async in same codebase without clear boundary.