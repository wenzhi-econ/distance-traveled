# Python formatting instructions (data cleaning)

## 1. Scope, priorities, and rule strength

### 1.1. Scope

- Apply the universal instructions in this guide to new Python codes.
- Do not reformat or refactor unrelated codes merely to apply this guide.
- Apply the data-cleaning profile only to procedural data-cleaning and transformation scripts. Ordinary packages, reusable applications, command-line tools, and multiprocessing programs do not automatically follow that profile.

### 1.2. Priorities

Use the following order when instructions conflict:

1. Follow the current task's explicit requirements.
2. Preserve existing behavior, public interfaces, variable meanings, and file organization unless the task asks for a change.
3. Follow the project's supported Python version, enforced configuration, and tests.
4. Follow this guide.
5. Use reference scripts only as examples. This guide takes precedence when a reference script is inconsistent with it.

If this guide conflicts with enforced project configuration, follow the project configuration and briefly report the conflict.

### 1.3. Rule strength

- **Required:** Rules that use `must`, `never`, `do not`, or a direct instruction such as `Use` or `Put` are requirements.
- **Preferred:** Rules that use `prefer` or `should` describe the normal default. Depart from them only for a clear reason, such as correctness, compatibility, performance, or an enforced project convention.
- **Optional:** Rules that use `may` describe an allowed choice, not a requirement.

## 2. Universal Python instructions

- Prefer clear, explicit code over compact or clever code, especially in data-cleaning scripts.

### 2.1. Basic layout

- Use 100 characters as the line-length target for code, comments, docstrings, error messages, and the script header.
  - A line may exceed the target when splitting a long string, URL, identifier, import path, or machine-defined literal would reduce clarity or change its contents.
  - Do not change spaces or other characters inside a string merely to satisfy the target.
  - The target does not apply to the `Run` command in the script header.
- Use four spaces for each indentation level. Never use tabs.
- Use blank lines within a procedure only to separate meaningful logical units.
- Do not leave trailing whitespace.
- Never use backslashes for line continuation. Write multiline expressions inside parentheses.

### 2.2. Naming

- Use `snake_case` for variables, functions, and modules.
- Use `PascalCase` for ordinary classes.
- Use `UPPER_SNAKE_CASE` for module-level bindings that should be treated as constants.
- Use names that describe the object or its role.
  - Avoid vague names such as `data`, `df`, `temp`, `result`, or `x` when a more informative name is available.
- Use consistent prefixes and suffixes when they communicate a stable meaning:
  - Prefer an `id_` prefix for identifiers, such as `id_user`.
  - Use `is_` or `has_` for Boolean variables that describe an observation.
  - Use `flag_` for a temporary Boolean row mask used for selection or assignment.
  - `n_` for scalar counts.
  - `input_`, `output_`, and `temp_` for file paths with those roles.
- Use a leading underscore only for genuinely internal variables or short-lived unpacked values.
- Keep abbreviations conventional and unambiguous.

### 2.3. Imports and dependencies

- Put imports immediately after the script header.
- Divide imports into three groups, with one blank line between groups:
  1. Python standard-library imports.
  2. Third-party imports.
  3. Project-local imports.
- Sort imports alphabetically within each group unless the project's configured Ruff or isort rules specify another order.
- Put `from __future__ import ...` immediately after the script header and before the three import groups when such an import is needed.
- Never use wildcard imports.
- Import only the names that the script uses.

### 2.4. Paths and global settings

- Use `pathlib.Path` for local filesystem paths.
  - Do not assemble paths through manual string concatenation.
  - Keep paths in the code platform independent. Use forward-slash project-relative paths in the header.
  - Do not change the process working directory merely to make relative paths work.
  - When the `Run` command sets `--cwd` to the project root, resolve project-relative paths from that working directory.
  - If a module must work from any working directory, derive the project root from `__file__` or receive it as an explicit argument. Do not guess the root.
- Use descriptive constant bindings for configuration, schemas, labels, and paths.
- Use relative import to import the file `E:/Dropbox/E_Projects/DistanceTraveled/codes/main.py` for local filesystem paths.
  - The file defines global parameters for important folders, e.g., `DIR_OUTPUTS`, `DIR_TEMPDATA` and helper functions to handle paths, e.g., `relative_path`, `ensure_directory`, `ensure_parent`.

### 2.5. Reporting in console

- The file `E:/Dropbox/E_Projects/DistanceTraveled/codes/main.py` contains some helper functions that are useful for reporting in console.
- In particular, functions `start_run`, `report_status`, `report_progress`, `finish_run` are useful to report key steps, status, progress during a script run.
- Use these helper functions to help the users understand the script run status and key statistics.

## 3. Universal comments and documentation

### 3.1. Header comments

- Each script must begin with a triple-quoted header.
- Use the field names and order shown below.
- Instructions for the inputs and outputs:
  - Use `Inputs` and `Outputs`, even when there is only one item.
  - List multiple inputs and outputs as `(a)`, `(b)`, and so on. Refer to the same letters in `Descriptions of outputs`.
  - State the observational unit of every dataset output whenever it is relevant.
  - Under an input, identify the earlier script that constructs it when this is useful.
- Instructions for the `Run` command:
  - Basically all my Python scripts should be executed with the `conda run` command.
  - A common template is as follows:
    - `conda run --live-stream --name <conda-env> --cwd "<path/to/project_root>" python -m relative.path.to.script`.
    - Make sure you specify the arguments `--live-stream`, `--name`, `--cwd`.
  - There is no maximum line length requirement for the `Run` command specified in the header commend. You should put the whole command in one line (regardless of its length).
- Instructions for the numbered notes:
  - Use numbered notes for sample restrictions, definitions, assumptions, non-obvious corrections, and other information needed to interpret the script.
  - Number every list consecutively. Do not repeat or skip a number.
- Specific instructions for a utility module:
  - Use `Not applicable.` for a section that does not apply to a utility module.
  - Utility modules may add `Dependencies` and `To be imported by` after `Run`.
- Update the date and all paths whenever the script's interface changes.

```python
"""
Task:
    A one-line description of the task.

Inputs:
(a) path/to/input_1.dta
(b) path/to/input_2.csv
    <== Constructed by "path/to/earlier_script.py"

Outputs:
(a) path/to/output_1.dta
(b) path/to/output_2.csv

Descriptions of outputs:
(1) Output (a) is at individual level, with one row per individual.
(2) Output (b) is at company level, with one row per company.

Run:
    conda run --live-stream --name <conda-env> --cwd "<path/to/project_root>" python -m relative.path.to.script`

Notes:
(1) State the key sample restriction.
(2) Define an important constructed variable.
(3) Record an important assumption or data limitation.


Wang Wenzhi
Time: YYYY-MM-DD
"""
```

### 3.2. Comment blocks to split every script

- Divide every Python script into numbered top-level blocks using the comment style shown below.
- Instructions for workflow scripts:
  - Use `Step 1` for global settings, paths, schemas, and other preparation.
  - Give later steps consecutive numbers in execution order. 
  - Give each step a short action-oriented title that accurately describes the whole block.
- Instructions for second-level blocks:
  - Add a second-level sub-step only when a top-level step contains at least two substantial parts.
  - Number sub-steps as `S-X-Y`, where `X` is the parent step and `Y` starts at 1.
  - Keep a step or sub-step intact. Do not scatter code for the same transformation across distant
    parts of the script.
- Instructions for utility modules and other scripts organized around independent definitions:
  - Replace `Step` with `Function`, `Class`, or `Block` as appropriate.
  - Number the top-level blocks consecutively.
- Add two empty lines above and below each top-level or second-level comment block.

```python
# <>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>
# <> Step 2. Read and validate the raw dataset
# <>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>#<>


raw_user = pd.read_csv(INPUT_USER, dtype="string", low_memory=False)


# >>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>
# >> S-2-1. Validate the user identifiers
# >>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>#>>
```

### 3.3. Explanatory comments as a standalone string literal

I prefer to use numbered explanatory comments to explain the important procedures or key conditions or iterations.

- These explanatory comments will be standalone (unused) `Notes` block as string expressions.
- Explain why the code is needed, what assumption it uses, or what invariant it protects.
- Number the main points as `(1)`, `(2)`, and so on. Use `(a)`, `(b)`, and so on for nested points.
- Use a raw triple-quoted string, `R"""`, when the notes contain literal backslashes or regular expressions that Python could otherwise interpret as escape sequences.
- Do not merely translate an obvious line of code into English.
- Write these comments as complete sentences with normal capitalization and punctuation.

```python
"""
Notes:
(1) First, diagnose duplicate keys without changing the data.
(2) Then, retain one row only when all non-key values agree.
    (a) Preserve a flag showing that the retained row came from a duplicate group.
    (b) Raise an error when conflicting values cannot be resolved by a stated rule.
"""
```

Inline comments should start with `#`.
  - Put an inline comment on its own line when an end-of-line comment would make the code crowded.
  - Write inline comments as complete sentences with normal capitalization and punctuation.

### 3.4. Docstrings of functions and classes

- Give every function, method, and class a multiline docstring. Never use a one-line docstring such as `"""Return the value."""`.
- Follow the NumPy docstring style consistently. Do not mix it with headings such as `Parameters:` or `Returns:` from another docstring style.
- Begin with a concise summary in plain English.
- Include `Parameters`, `Returns`, `Raises`, `Attributes`, and `Notes` only when they apply.
- For a small helper, the summary may be sufficient. A three-line docstring is still required.
- If a function returns `None`, omit `Returns` unless the in-place effect needs explanation.
- Describe shapes, units, observational levels, accepted categories, and mutation behavior when they are important to correct use.
- If there is a `Notes` section, always use numbered points.
- Keep types in the signature and the docstring consistent.
- Use reasonable type hints for every parameter and return value. Avoid `Any` when a narrower type is practical.
- Use a raw docstring when it contains literal backslash sequences that must be displayed unchanged.

```python
def format_share(numerator: float, denominator: int) -> str:
    """
    Format a ratio as a percentage string.

    Parameters
    ----------
    numerator : float
        Value used in the numerator.
    denominator : int
        Nonnegative value used in the denominator.

    Returns
    -------
    str
        Percentage rounded to one decimal place.

    Raises
    ------
    ValueError
        Raised when `denominator` is negative.

    Notes
    -----
    (1) A zero denominator returns an empty string.
    (2) Missing inputs must be handled before calling this function.
    """
    if denominator < 0:
        raise ValueError("denominator must be nonnegative")

    if denominator == 0:
        return ""

    return f"{numerator / denominator:.1%}"
```

## 4. Universal code organization

### 4.1. Functions and classes

- Give each function one clear responsibility.
- Prefer pure functions when practical. If a function mutates an argument, state this in its docstring.
- Return a transformed object instead of relying on hidden global state.
- Pass non-obvious arguments by keyword at call sites.
- Use a nested helper only when it is meaningful solely inside one parent function and does not need independent testing or reuse.
- Use a class only when state, invariants, or a coherent interface justify it. Do not create a class simply to hold a collection of unrelated helper functions.

### 4.2. Expressions and collections

- Put long lists, dictionaries, schemas, and column groups on multiple lines with one logical item per line.
- Avoid multiple statements on one line.
- Avoid deeply nested conditional expressions.
- Store reusable column groups, category orders, labels, and mappings in named collections near the code that uses them.
- Use raw multiline strings for substantial SQL statements and regular expressions when this improves readability.

## 5. Data-cleaning and transformation profile

### 5.1. Scope

This profile applies only to procedural data-cleaning and transformation scripts.

### 5.2. A procedural style

Common instructions for one data-cleaning script:

- Keep the main workflow linear so that a reader can run it line by line and inspect the DataFrame after each important transformation.
- Do not wrap the full workflow in a `main` function or a large class.
- Do not add an `if __name__ == "__main__":` block merely to wrap the linear workflow.
- A script may use a `main` function and an `if __name__ == "__main__":` guard when command-line
  behavior, multiprocessing, safe importing, or reuse requires them.
- Use functions for repeated operations, well-defined data-transformation logic, or a complex transformation that becomes clearer when named. Do not create a function for every small sequence of statements.

### 5.3. Multiple stage-specific scripts

Because a full data cleaning and transformation pipeline often involves with multiple stages, I prefer to keep one stage in a separate Python script. 

Here are instructions for multiple stage-specific scripts:
- Put one major pipeline stage in one Python script when the full transformation has several stages.
  - A stage may read outputs from earlier stages, but it must not depend on state from later stages.
  - Each stage must be independently runnable from the project root once its declared inputs exist.
  - Do not import an executable pipeline script to obtain one of its DataFrames, variables, or side effects. Read the earlier stage's saved output instead.
  - Save intermediate datasets after major transformations when they form a stable stage boundary or are needed by later scripts. Do not save a file after every minor operation.
- Instructions about the helper functions:
  - Define stage-specific helper functions in that stage's script before their first use.
  - Put helpers shared by multiple stages in a separate utility module.
  - A utility module must define settings, functions, types, or classes without running a data pipeline as an import side effect.

### 5.4. Typical order of a data-cleaning script

A typical data-cleaning script should use this order:

1. Header.
2. Imports.
3. Step 1: settings, paths, schemas, label dictionaries, and type definitions.
4. Stage-specific helper functions, if needed.
5. Read the declared inputs.
6. Clean and transform the data in numbered steps.
7. Run simple informational diagnostics.
8. Write outputs and report completion.

### 5.5. Specific requirements for `pandas`-based codes

#### 5.5.1. Reading and preparing inputs

- Read each input from a named `Path` declared in Step 1.
- Specify options such as `low_memory=False` deliberately rather than accepting behavior that may vary across chunks.
- Preserve an original row identifier when it will help trace corrections or duplicate handling.
- Normalize whitespace and empty strings explicitly. Do not accidentally turn missing values into the literal strings `"nan"` or `"None"`.

#### 5.5.2. DataFrame transformations

- By default, a transformation function returns a new DataFrame and does not modify a DataFrame supplied by the caller.
- A function may mutate a DataFrame when copying it would be prohibitively expensive or the API  specifically requires mutation. Make the mutation clear in the function name and docstring.
- Reuse the same descriptive DataFrame name during a linear sequence of transformations when the object's role and observational level do not change.
- Use a new DataFrame name when the old and new versions must coexist or when the transformation changes the object's role or observational level.
- Avoid `inplace=True`.
- Use `.copy()` when creating a DataFrame that will be modified from a filtered slice.
- Use `.loc[row_mask, column_name]` for conditional assignment.
  - Give a complicated mask a descriptive `flag_` name and reuse it instead of repeating the full condition.
- Instructions on merge practices:
  - Specify merge keys, merge type, and expected cardinality. Use `validate` when pandas can verify the cardinality.
  - Check unmatched keys after important merges. Do not allow an accidental many-to-many merge.
- Sort explicitly before operations whose results depend on order, such as `cumcount`, `shift`, or the construction of sequence indices.
- Prefer vectorized pandas or NumPy operations to row-by-row iteration. Use batching when the input is too large to process safely in memory.

```python
survey_clean = survey.loc[eligible_sample].copy()

survey_clean["age"] = pd.to_numeric(survey_clean["age_raw"], errors="coerce")
flag_invalid_age = survey_clean["age"].notna() & ~survey_clean["age"].between(18, 100)
survey_clean.loc[flag_invalid_age, "age"] = pd.NA
survey_clean["is_invalid_age"] = flag_invalid_age
```

#### 5.5.3. Saving outputs

Instructions for output writing behavior:

- By default, do not overwrite an existing final output. Raise `FileExistsError` unless the task or an explicit configuration setting permits replacement.
- A reproducible intermediate output may be replaced by default when the script header clearly identifies it as an intermediate output.
- Treat an output as expensive to reproduce when recreating it requires a long-running computation, paid or remote data access, or manual work.
- For a final output that is expensive to reproduce, write to a temporary file, validate it, and atomically replace the final file only after successful completion.
  - Create the temporary file in the final file's directory and use a suffix accepted by the output writer.
  - Remove the temporary file after a failed write or validation.
- After writing, report the output path, row count, observational level, and any other essential completion statistics.

Instructions for the saved datasets:

- Write only the intended final columns and put identifier variables first.
- Do not write a DataFrame index unless the index is an intentional output variable.
