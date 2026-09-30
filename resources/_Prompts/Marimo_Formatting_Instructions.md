**Prompts:**

When creating, editing, or reviewing a marimo notebook:
- Read the complete personal formatting guide at: `path_to_this_guide`.
- Use the installed `marimo-notebook` skill and its relevant references.
- Follow the personal guide over general stylistic defaults and examples. Preserve runtime correctness and the project's environment; report conflicts.
- Default to editing the notebook's Python source directly. Do not start a live marimo server unless explicitly requested.


# 1. Marimo notebook formatting instructions

## 1.1. Purpose and priorities

Introduction to this guide:

- Use this guide when creating or editing marimo notebooks for data inspection, summary statistics, interactive figures, and reports for coauthors.
- The main goal is to make the Python source easy to read and change manually.
- A reader should be able to find the text, controls, sample definition, calculation, and presentation for a result without tracing a large collection of helpers.


Use this order when requirements conflict:

1. Follow the current task's explicit requirements.
2. Preserve existing results, variable meanings, public interfaces, and file organization unless the task asks for a change.
3. Respect the supported Python and marimo versions, enforced project configuration, and tests. Report any conflict with this guide rather than silently changing the environment or claiming exact compliance.
4. Follow this guide. Its marimo-specific rules override the general Python rules reproduced here.
5. Use reference notebooks only as examples.

Explanations of some words:

- Instructions such as "use," "must," and "do not" are requirements.
- "Prefer" and "should" describe the normal default, with exceptions for a clear reason such as correctness, compatibility, or performance.
- "May" introduces an option.
- Apply formatting to the requested work; do not refactor unrelated notebooks.

Reference notebooks show possible analyses and layouts; their inconsistent formatting, old dependencies, and project-specific assumptions are not requirements.

## 1.2. File structure and header

### 1.2.1. Header

Begin every notebook script with a triple-quoted header. Use the following field names and order. Keep each paragraph, list item, and the entire `Run` command on one source line; do not wrap header text to meet a line-length target.

```python
"""
Task:
    A one-line description of the task.

Inputs:
(a) path/to/input_1.parquet
(b) path/to/input_2.csv
    <== Constructed by "path/to/earlier_script.py"

Outputs:
(a) path/to/output_1.csv
(b) path/to/output_2.html

Descriptions of outputs:
(1) Output (a) is a category-level summary, with one row per category.
(2) Output (b) is a report of the selected sample and its summary statistics.

Run:
    conda run --live-stream --name <conda-env> --cwd "<path/to/project_root>" marimo edit --watch --no-token --skip-update-check path/to/notebook.py

Notes:
(1) State the key sample restriction and the observational unit.
(2) Define important constructed variables, weights, and denominators.
(3) Record assumptions, non-obvious corrections, and data limitations.


Wang Wenzhi
Time: YYYY-MM-DD
"""
```

- Always use `Inputs` and `Outputs`, including for a single item.
- Label file items `(a)`, `(b)`, and so on. Describe each dataset output's observational unit and use the same output letters in `Descriptions of outputs`.
- Number each separate numbered list consecutively, starting at `(1)`. The descriptions and notes are separate lists.
- Describe what the notebook actually reads and writes. If it writes no files, say `Not applicable.` under `Outputs`, and use `Not applicable.` under `Descriptions of outputs`. Do not list an export that the notebook does not perform.
- Replace the command placeholders with the actual environment and paths when known. Do not invent an environment name or claim an untested path works. Quote a notebook path if it contains spaces.
- Use the full flags `--live-stream`, `--name`, and `--cwd` for `conda run`, and `--watch`, `--no-token`, and `--skip-update-check` for `marimo edit`. Keep the command on one line regardless of its length.
- Update the date, paths, and descriptions whenever the notebook's interface changes.

### 1.2.2. Notebook scaffold and dependencies

Scaffold:

- After the header, keep the normal marimo scaffold: `import marimo`, `__generated_with`, `app = marimo.App(...)`, the cells, and the final `if __name__ == "__main__": app.run()` guard on separate lines.
- Preserve marimo's generated version value; for newly authored source, use the actual marimo version used to create or validate it. Do not fabricate a version or copy one blindly from an example.
- Specify a short and meaningful app title within, e.g., `marimo.App(app_title="Descriptive results")`.
  - The short app_title in the notebook scaffold may remain an ordinary string literal; the cell-local multiline-string rule (specified later) applies to text defined inside cells.
- The guard is required notebook structure and is an exception to the procedural Python preference against unnecessary main guards.

Dependencies:

- Put analysis imports in an early, meaningfully named import cell, including `import marimo as mo`.
- Inside that cell, group standard-library, third-party, and project-local imports, with a blank line between groups and alphabetical order within each group unless enforced configuration says otherwise.
- Import only what is used; never use wildcard imports. 
- Preserve an existing supported `app.setup` arrangement when the project needs it; do not introduce one just for formatting.

Inline script metadata:

- Do not add a `# /// script` dependency block unless the user explicitly requests inline script metadata.
- If explicitly requested, place it after the opening header when supported by the target tooling, and declare only verified requirements. 
- `__generated_with` is ordinary marimo version information, not an inline dependency block.

### 1.2.3. Reading order

Prefer this order in the source:

1. Header and marimo scaffold.
2. Imports.
3. Settings, paths, schemas, and shared category mappings.
4. Reusable helpers, if needed.
5. Input loading and common preparation.
6. Report sections, each keeping its heading, controls, calculation, figure, underlying table, and explanations nearby.
7. Optional diagnostics, appendices, and explicitly requested exports.

- This is a reading order, not a substitute for dependencies.
- Keep expensive input loading independent of figure controls where possible, so changing a display parameter does not reload the input.

## 1.3. Cells, names, and dependencies

### 1.3.1. Cell names and size

Cell names:

- Give every `@app.cell` function a unique, concrete `snake_case` name, such as `load_employment_data`, `define_controls`, `calculate_distribution`, or `render_report`.
- Do not leave repeated names such as `_` or `__`, and do not use names such as `cell_12`.
- Keep cell names distinct from the variables and helpers they define.

Size:

- Give each cell a coherent purpose.
- Normally separate control creation from calculations, and separate substantial calculations from presentation.
- Closely related display elements may share a cell.
- Avoid both a single giant cell and a separate cell for every trivial assignment.

### 1.3.2. Shared and local names

- A name defined at cell scope without a leading underscore is available to other cells. **Define each such name in only one cell.** This includes imports, functions, classes, loop targets, and unpacked values.
- Prefix cell-local text, intermediate calculations, masks, charts, and display-only objects with `_`. Names such as `_contents`, `_figure_title`, `_flag_eligible`, and `_table` may be reused in different cells.
- A leading underscore has a special meaning in marimo: it makes the name local to that cell. Do not pass an underscore-prefixed variable to another cell or put it in a cell's return list.
- Use a shared name for a DataFrame, UI control, or helper only when another cell needs it. Prefer names such as `occupation_distribution` and `ui_occupation_top_n` over `df` or `slider`.
- Within an ordinary helper function, use normal Python local names. Do not prefix every helper parameter and local variable with `_` solely because the helper is defined inside a cell.
- Keep cells free of circular references and hidden shared mutation. In particular, do not modify a DataFrame supplied by another cell in place. Create and return a new result with a distinct shared name.

### 1.3.3. Parameters and returns

- In delivered Python source, put each cell parameter on its own line and add a trailing comma after the last parameter.
- Put every returned name on its own line inside parentheses, also with a trailing comma.
- Apply this even to a single parameter or returned name.

```python
@app.cell
def prepare_category_counts(
    category_counts,
):
    eligible_category_counts = category_counts.loc[category_counts["count"].gt(0)].copy()
    return (
        eligible_category_counts,
    )
```

- For a cell with no parameters, keep the empty parentheses on the function-definition line, for example `def import_libraries():`. Choose a unique name that describes the cell's actual purpose.
- Use a bare `return` when there are no shared outputs to return; do not manufacture an empty multiline tuple.
- Keep dependencies and return lists consistent with the cell body, and return the shared definitions needed downstream.

### 1.3.4. Displayed output and wrapper documentation

Displayed output:

- The final expression in a cell body is its displayed output; the wrapper's `return` lists shared definitions.
- Display a chart, `mo.md(...)`, or a composed layout as that final expression, before the wrapper return.
- Do not use a return statement as a way to display an object.

Wrapper documentation:

- The generated `@app.cell` wrappers are exempt from the requirement (specified later) to give ordinary functions NumPy docstrings and type hints.
- Use the concrete cell name and meaningful internal notes instead.
- Actual helper functions and classes still follow the documentation rules in Section 7.

## 1.4. Editable text and Markdown

### 1.4.1. Keep text visible in the source

- Before rendering any Markdown, assign its contents to a local multiline triple-quoted string.
- Use descriptive names when several blocks coexist, such as `_section_title`, `_method_contents`, and `_interpretation_contents`.
- Do the same for human-facing figure titles, subtitles, axis and legend titles, notes, callout titles, control labels, and table and accordion labels.
- Put these assignments near the beginning of the cell that owns the text, after any calculations needed to interpolate values.

```python
_contents = """
# Distribution of employment spells

- Select a sample using the controls below.
- Expand the table to inspect the values behind the figure.
"""

mo.md(_contents)
```

- Keep paragraphs and list items on single source lines, with no maximum line length.
  - Add line breaks only for structure or intended meaning, such as new paragraphs, list items, equations, or code blocks.
  - Do not split prose into concatenated string fragments, add `\n` escapes for wrapping, or run automatic prose reflow.
  - This exception applies to all triple-quoted multiline strings in the notebook, including its header and explanatory notes. It does not require removing meaningful existing line breaks.

- Use `f"""..."""` when text depends on results or selections. Use `r"""..."""` for literal backslashes, such as LaTeX, and `rf"""..."""` when both are needed.
  - For a one-line title or label stored in a multiline string, use `.strip()` to remove surrounding whitespace.
  - Do not flatten Markdown lists or paragraphs to remove whitespace.

- Protocol strings, column identifiers, paths, color values, and format specifications may remain ordinary strings.
  - For example, `kind="info"`, `"country_code"`, and `"{:.1%}"` do not need their own multiline variables.
  - Keep editable display labels separate from stable category codes.
  - When a label mapping truly must be shared, define its wording once using local multiline strings in its owning cell and publish the assembled mapping under a descriptive shared name.
  - Do not spread one label across distant cells or create a general translation framework without a need.

### 1.4.2. Separate methods from interpretation

Use the following presentation convention consistently:

| Content | Presentation |
| --- | --- |
| Report titles, section headings, and neutral navigation | Ordinary `mo.md(...)` |
| How a result was constructed: inputs, restrictions, transformations, weights, denominators, and plotting choices | `mo.md(_method_contents).callout(kind="info", title=_method_title)` |
| What a result means: patterns, implications, and substantive interpretation | `mo.md(_interpretation_contents).callout(kind="danger", title=_interpretation_title)` |
| Operational problems, such as an empty selection or unavailable data | A clearly titled warning, normally `kind="warn"` |

- Here, `danger` is the user's chosen emphasis style for interpretation. It does not mean that the result is an error or statistically significant.
- Use a clear title so readers do not have to infer meaning from color.
- If one block mixes construction and interpretation, split it into two blocks.
- Both the callout body and title must come from local multiline strings.

- Prefer bullet points or numbered lists when explaining several steps or findings.
- Keep technical construction details out of the interpretation block, and avoid unsupported causal claims.
- Recompute dynamic statements when their underlying controls change; do not leave a conclusion hard-coded when it may become false for a different selection.

## 1.5. Interactive controls

Use these controls by default:

| Input | Control | Explicit settings |
| --- | --- | --- |
| Integer parameter, such as the number of displayed categories | `mo.ui.number` | Integer `value`, `step=1`, meaningful bounds where applicable, and `label` |
| One choice from a fixed set | `mo.ui.dropdown` | `options`, a valid initial `value`, and `label` |
| Several choices from a fixed set | `mo.ui.multiselect` | `options`, a valid initial list in `value`, and `label` |

- Do not replace these defaults with sliders or free-text inputs merely because a reference notebook uses them. Other controls are allowed when the requested interaction needs them.
- Assign each control used by other cells to a descriptive shared name, preferably with a `ui_` prefix.
  - Define its `label` from a local multiline string at creation time; a nearby heading is not a substitute. 
  - Display each figure's controls close to the figure and its table in the rendered report. Intentionally shared controls may appear in a clearly labelled common section. Keep the relevant source cells nearby where practical.
- Handle empty and invalid states deliberately.
  - A number may be cleared, a dropdown may have no selection, and a multiselect may be empty.
  - Do not silently turn "no categories selected" into "all categories."
  - Define and explain that behavior if the analysis requires it. 
  - Keep a numeric default within its bounds, and do not silently truncate a fractional input where an integer is required.
- Keep each figure's controls separate unless shared controls are an intentional part of the report. 
  - If a control's options depend on another control, create it in a downstream cell and choose a valid default. 
  - Ordinary figure updates should follow `.value` dependencies; do not add callbacks, state objects, or mutable global containers when straightforward reactive cells suffice.

## 1.6. Figures, tables, and report layout

The underlying table for most interactive figures:

- For most interactive figures, provide the corresponding underlying table as a `mo.ui.table` inside an expandable `mo.accordion`. 
  - If omitted because it is inappropriate, too costly, or disclosive, explain why.
- Every `mo.ui.table` call must explicitly specify `label`, `format_mapping`, and `show_column_summaries=False`, including diagnostic and appendix tables.
  - Use a descriptive local multiline label.
  - Define the format mapping nearby; use `{}` explicitly when no formatting is needed.
  - Prefer explicit `pagination` and `page_size` settings suited to the table size.
  - Keep numeric values numeric. Apply presentation formatting through `format_mapping`, using format strings or small documented functions that handle missing values where needed. Do not turn the analytical DataFrame into formatted strings or round its values before calculations. A share stored between 0 and 1 can use `"{:.1%}"`; a percentage already between 0 and 100 needs a different formatter.
- An accordion's item label is a dictionary key, not a `label=` argument. Store that key in a local multiline string and strip surrounding whitespace. Pass the table as the corresponding value.

Calculation of the results:

- Calculate the filtered summary once, then derive both the plot data and the table from it.
- Keep sample restrictions, weights, missing-value handling, and denominators consistent.
- Avoid recalculating a summary or ranking when only display settings change. Recalculate it when the sample, weights, grouping, or ranking measure changes. Put reusable calculations in cells that depend only on the inputs needed for those calculations, and apply display-only choices such as Top-N downstream.
  - For example, if an interactive UI element allows the user to choose the number of top occupations in employment to be plotted in the figure, it is more efficient to first calculate occupation employment and rank occupations once and then decide which occupations to show based on user's top-N choice than the way to rank occupations every time a user changes his top-N choice.

Layout:

- Use `mo.vstack` for the overall section and `mo.hstack` for related controls or side-by-side panels.
- Set spacing and alignment deliberately when needed, but avoid deeply nested layouts and unnecessary custom HTML or CSS.
- A usual report section contains a heading, controls, an `info` construction note, a figure, an expandable table, and a `danger` interpretation.
- Adapt the placement to the explanation. Use `mo.as_html` for objects that need conversion (e.g., Python lists or dicts to report); prefer `mo.ui.table` for DataFrames.

## 1.7. Remaining Python rules

### 1.7.1. Layout and naming

- Use four spaces per indentation level, no tabs, and no trailing whitespace.
- Use 100 characters as the target for ordinary code and `#` comments.
  - Exempt triple-quoted strings and header run commands as described above.
  - Allow an indivisible URL, identifier, import path, or machine-defined literal to exceed the target when splitting it would harm clarity or correctness.
- Use parentheses for multiline expressions, never backslash continuations.
  - Put long collections and argument groups on multiple lines with one logical item per line.
  - Do not put several statements on one line or use deeply nested conditional expressions.
- Use `snake_case` for ordinary variables, functions, and new modules; `PascalCase` for classes; and `UPPER_SNAKE_CASE` for shared constant settings, schemas, and paths.

### 1.7.2. Paths, helpers, and comments

Paths:

- Use `pathlib.Path` for local filesystem paths.
- Declare paths and shared settings early.
- Resolve project-relative paths from the project root specified by `conda run --cwd`; do not change the working directory to make paths work.
- If a different launch location must be supported, receive or derive a verified root explicitly.
- Browser-hosted notebooks need their own verified asset-loading arrangement; do not copy a reference notebook's URLs or paths blindly.

Helper functions:

- Prefer clear, direct code and helpers with one responsibility.
- Use helpers for repeated work or substantial logic that becomes clearer when named.
- Keep a helper local to its cell if no other cell needs it; put truly shared helpers in a dedicated cell or an existing utility module.
- Do not build a class merely to group unrelated functions, or hide each figure's wording and assumptions inside a general report builder.
- Pass non-obvious arguments by keyword and prefer functions that return a transformed object without mutating their inputs.

Docstring of the helper functions:

- Give actual helper functions, methods, and classes multiline NumPy-style docstrings.
- A three-line summary is enough for a small helper.
- Add `Parameters`, `Returns`, `Raises`, `Attributes`, and numbered `Notes` only where applicable; describe units, shapes, observational levels, and mutation behavior when relevant.
- Use reasonable parameter and return type hints and avoid `Any` when a narrower type is practical.
- Omit a redundant `Returns` section for a function returning `None`.
- Do not apply these requirements to generated cell wrappers.

Comments:
- Use standalone triple-quoted `Notes:` strings for non-obvious implementation explanations, with points numbered `(1)`, `(2)`, and so on, and nested points lettered `(a)`, `(b)`, and so on.
- Explain why a correction or assumption is needed, not what an obvious line does.
- Use a raw string when literal backslashes must be preserved.
- Do not leave an implementation note as the final display expression; use Markdown and the appropriate callout when it is meant for report readers.
- Write `#` comments as complete sentences and move them to their own line when an end-of-line comment becomes crowded.

### 1.7.3. Data transformations

- Keep data transformations straightforward to inspect.
- Read inputs from named paths; set parsing options deliberately.
- Avoid `inplace=True`. Use `.copy()` for a filtered DataFrame that will be modified and `.loc[mask, column]` for conditional assignment.
- Name complex masks.
- Specify merge keys, merge type, and expected cardinality, using `validate` when pandas can check it; inspect unmatched keys after important merges.
- Sort before order-dependent operations.
- Prefer vectorized operations, with batching when memory requires it.
- A helper may mutate inputs only for a justified need, with that behavior explicit in its name and docstring; **do not use this exception to create hidden mutation between reactive cells**.


## 1.8. Worked example

- This small notebook demonstrates the main conventions using invented aggregate counts.
- It is not an empirical result. Replace the header placeholders before using it as a project notebook.
- The example uses marimo, pandas, and Altair from an existing environment; it intentionally contains no dependency metadata and writes no files.
- Its `__generated_with` value records the marimo version used to generate this example, not a required version for future notebooks.

```python
"""
Task:
    Demonstrate an editable interactive report using invented category counts.

Inputs:
    Not applicable. The notebook constructs a small illustrative dataset in memory.

Outputs:
    Not applicable. Results are displayed in the notebook; no files are written.

Descriptions of outputs:
    Not applicable.

Run:
    conda run --live-stream --name <conda-env> --cwd "<path/to/project_root>" marimo edit --watch --no-token --skip-update-check path/to/notebook.py

Notes:
(1) Counts are invented; each input row represents one country-category combination.
(2) Shares use all counts in the selected countries, before Top-N truncation.
(3) An empty country selection produces an explanatory message instead of a figure.


Wang Wenzhi
Time: YYYY-MM-DD
"""

import marimo

__generated_with = "0.23.16"
app = marimo.App(width="full", app_title="Descriptive results")


@app.cell
def import_libraries():
    import altair as alt
    import marimo as mo
    import pandas as pd

    return (
        alt,
        mo,
        pd,
    )


@app.cell
def prepare_example_counts(
    pd,
):
    category_counts = pd.DataFrame(
        {
            "country_code": ["GB", "GB", "GB", "US", "US", "US"],
            "category": ["A", "B", "C", "A", "B", "C"],
            "count": [40, 25, 15, 60, 35, 25],
        }
    )
    return (
        category_counts,
    )


@app.cell
def define_distribution_controls(
    mo,
):
    _section_title = """
    ## Category distribution: illustrative data
    """
    _top_n_label = """
    Number of categories in the figure
    """.strip()
    _metric_label = """
    Measure to display
    """.strip()
    _countries_label = """
    Countries to include
    """.strip()
    _count_label = """
    Count
    """.strip()
    _share_label = """
    Share
    """.strip()
    _uk_label = """
    United Kingdom
    """.strip()
    _us_label = """
    United States
    """.strip()
    _metric_options = {
        _count_label: "count",
        _share_label: "share",
    }
    _country_options = {
        _uk_label: "GB",
        _us_label: "US",
    }

    ui_distribution_top_n = mo.ui.number(
        start=1,
        stop=3,
        step=1,
        value=2,
        label=_top_n_label,
    )
    ui_distribution_metric = mo.ui.dropdown(
        options=_metric_options,
        value=_share_label,
        allow_select_none=False,
        label=_metric_label,
    )
    ui_distribution_countries = mo.ui.multiselect(
        options=_country_options,
        value=[_uk_label, _us_label],
        label=_countries_label,
    )

    mo.vstack(
        [
            mo.md(_section_title),
            mo.hstack(
                [
                    ui_distribution_top_n,
                    ui_distribution_metric,
                    ui_distribution_countries,
                ],
                wrap=True,
                gap=2,
            ),
        ]
    )
    return (
        ui_distribution_countries,
        ui_distribution_metric,
        ui_distribution_top_n,
    )


@app.cell
def calculate_category_distribution(
    category_counts,
    ui_distribution_countries,
):
    _selected_countries = ui_distribution_countries.value
    _flag_selected = category_counts["country_code"].isin(_selected_countries)
    _selected_counts = category_counts.loc[_flag_selected].copy()
    category_distribution = (
        _selected_counts.groupby("category", as_index=False)["count"]
        .sum()
        .sort_values(["count", "category"], ascending=[False, True])
        .reset_index(drop=True)
    )
    _n_total = category_distribution["count"].sum()
    category_distribution["share"] = (
        category_distribution["count"] / _n_total if _n_total > 0 else float("nan")
    )
    category_distribution["rank"] = range(1, len(category_distribution) + 1)
    return (
        category_distribution,
    )


@app.cell
def render_category_distribution(
    alt,
    category_distribution,
    mo,
    ui_distribution_metric,
    ui_distribution_top_n,
):
    _top_n = ui_distribution_top_n.value
    _metric = ui_distribution_metric.value
    _empty_contents = """
    Select at least one country with positive counts, choose a measure, and enter a whole number of categories from 1 to 3.
    """
    _empty_title = """
    Check the figure settings
    """.strip()
    _invalid_top_n = _top_n is None or _top_n not in (1, 2, 3)
    _invalid_metric = _metric not in ("count", "share")
    mo.stop(
        _invalid_top_n or _invalid_metric or category_distribution["count"].sum() <= 0,
        mo.md(_empty_contents).callout(kind="warn", title=_empty_title),
    )

    _n_top = int(_top_n)
    _plot_table = category_distribution.head(_n_top).copy()
    _figure_title = f"""
    Top {len(_plot_table)} categories in the selected countries
    """.strip()
    _category_axis_title = """
    Category
    """.strip()
    _count_axis_title = """
    Number of observations
    """.strip()
    _share_axis_title = """
    Share of selected observations
    """.strip()
    _method_title = """
    How is the figure constructed?
    """.strip()
    _method_contents = """
    1. Keep the countries selected above and sum their counts within each category.
    2. Divide each category count by the total across all categories in the selected countries.
    3. Rank categories by count, breaking ties by category code, and limit only the figure to Top-N.
    """
    _table_label = """
    Full category distribution for the selected countries
    """.strip()
    _accordion_label = f"""
    View all categories; the figure shows the first {len(_plot_table)} ranks
    """.strip()
    _interpretation_title = """
    What does this illustrative distribution show?
    """.strip()
    _displayed_share = _plot_table["share"].sum()
    _interpretation_contents = f"""
    - The displayed categories account for {_displayed_share:.1%} of observations in the selected countries.
    - These invented counts demonstrate the report layout; they provide no evidence about an actual population.
    """
    _axis_titles = {
        "count": _count_axis_title,
        "share": _share_axis_title,
    }
    _format_mapping = {
        "count": "{:,.0f}",
        "share": "{:.1%}",
        "rank": "{:,.0f}",
    }

    _chart = (
        alt.Chart(_plot_table)
        .mark_bar()
        .encode(
            x=alt.X(
                f"{_metric}:Q",
                title=_axis_titles[_metric],
                axis=alt.Axis(format=".0%" if _metric == "share" else ",.0f"),
            ),
            y=alt.Y("category:N", sort="-x", title=_category_axis_title),
        )
        .properties(title=_figure_title)
    )
    _table = mo.ui.table(
        data=category_distribution,
        label=_table_label,
        format_mapping=_format_mapping,
        show_column_summaries=False,
        pagination=True,
        page_size=10,
        selection=None,
    )

    mo.vstack(
        [
            mo.md(_method_contents).callout(kind="info", title=_method_title),
            _chart,
            mo.accordion({_accordion_label: _table}),
            mo.md(_interpretation_contents).callout(
                kind="danger",
                title=_interpretation_title,
            ),
        ],
        gap=1,
    )
    return


if __name__ == "__main__":
    app.run()
```

- The table is local because its row selection does not drive another cell.
- The controls and analytical summary are shared because later cells use them.
- Changing Top-N or the displayed measure reruns presentation without rebuilding the country summary.
- Changing countries updates that summary and then the presentation.
- In a larger report, move interpretation into its own cell when that makes substantive editing easier.

## 1.9. Checks before delivery

1. Confirm the header, actual environment, input paths, output descriptions, date, and single-line run command. Do not add inline dependency metadata without the explicit request.
2. Check unique concrete cell names, one definition per shared name, underscore-prefixed local text, and the absence of cycles or hidden mutation.
3. Check multiline parameters and returns, including single-item lists, and the correct zero-item exceptions. If marimo saving changes the layout, report it accurately.
4. Confirm every report string is easy to find and edit, with no automatic wrapping of triple-quoted text. Confirm `info` for construction and `danger` for interpretation.
5. Check the requested control types, explicit multiline labels, valid defaults, and separate cells for control creation and value consumption.
6. Check that every table explicitly supplies `label`, `format_mapping`, and `show_column_summaries=False`, and that accordion item labels come from local multiline strings.
7. Verify that each figure agrees with its table and that Top-N, missing values, weights, and denominators are explained. Check empty selections, cleared numeric inputs, and small samples where relevant.
8. Run a syntax check and the installed version's `marimo check` without automatic fixes first.
9. When the task permits execution and inputs are available, run the notebook in the intended conda environment, exercise representative controls, and inspect the rendered text, chart, and table. Do not start expensive analysis, remote calls, or file writes merely for formatting validation.
10. Report what changed, what was checked, and any untested behavior or compatibility limits. Do not claim a visual or interactive check from a syntax check alone.
