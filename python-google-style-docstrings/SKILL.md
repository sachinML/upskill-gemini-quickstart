---
name: python-google-style-docstrings
description: Write clear and comprehensive Python function docstrings following the Google style guide.
---

# Python Google Style Docstrings

## Overview

This guide provides instructions for writing clean, readable, and machine-parsable docstrings for Python functions, following the Google Python Style Guide. Correct docstrings are essential for code maintainability and are used by tools like Sphinx to auto-generate documentation.

## Core Structure

A Google-style docstring is organized into logical sections indented under the function definition. Pay close attention to the formatting of each section.

```python
def function_name(arg1: type, arg2: str = "default") -> dict:
    """One-line summary of the function's purpose.

    A more detailed description of the function's behavior, logic, or usage,
    if necessary. This part can span multiple lines.

    Args:
        arg1 (type): Description of the first argument.
        arg2 (str, optional): Description of the optional second argument.
            Defaults to "default".

    Returns:
        dict: A description of the returned dictionary, including its keys
        and the purpose of its values. For example: "A dictionary containing
        user information with keys 'name' and 'id'."

    Raises:
        ValueError: Explanation of when this exception is raised.
    """
    # function body
    pass
```

## Handling Specific Cases

### Optional Arguments
Mark optional arguments with `(type, optional)` and describe their default value in the description using `Defaults to ...`.

```python
def send_greeting(name: str, greeting: str = "Hello"):
    """Prints a formatted greeting to the console.

    Args:
        name (str): The name of the person to greet.
        greeting (str, optional): The greeting to use. Defaults to "Hello".

    Returns:
        None
    """
    print(f"{greeting}, {name}!")
```

### Complex Return Types (e.g., `dict`)
When a function returns a complex type like a dictionary or list, describe its structure and contents.

```python
def parse_user_data(data_string: str) -> dict:
    """Parses a string of user data into a dictionary.

    Args:
        data_string (str): A comma-separated string containing user name and ID,
                           e.g., "Alice,123".

    Returns:
        dict: A dictionary containing the user's name and ID, with keys
              'name' (str) and 'id' (int).
    """
    name, user_id = data_string.split(',')
    return {"name": name, "id": int(user_id)}
```

## Best Practices

1.  **Be Specific and Descriptive:**
    - The `Args` description should explain the purpose of the argument.
    - The `Returns` description should clearly state what is being returned. For `sum(a, b)`, a good description is `"The sum of a and b."`, not just `"An integer."`.
2.  **Handle No Return Value:** If a function does not have a `return` statement (or implicitly returns `None`), always include `Returns: None`.
3.  **Keep the Summary Line Short:** The first line should be a concise, imperative statement (e.g., "Calculate..." not "This function calculates...").
4.  **Match Type Hints:** The types in the docstring `(type)` should match the type hints in the function signature.