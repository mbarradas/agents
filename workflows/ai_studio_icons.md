---
description: How to select and configure icons for Altair AI Studio python operators.
---

# Configuring Icons for AI Studio Extensions

When building custom Pyhon operators for Altair AI Studio, assigning the correct icon is crucial for UI consistency. By default, the `altair_aitools` SDK might assign a generic python logo. 

To use native AI Studio icons, you must configure them in the `extension.toml` file.

## 1. Defining Icons in `extension.toml`

The `altair_aitools` builder allows you to override default operator attributes directly via the configuration file using the `[operators.<function_name>]` block.

```toml
[operators.my_custom_operator]
icon = "chart_pie.png"
```

## 2. Supported Icon Names

AI Studio ships with hundreds of internal icons that you can reference simply by their string name (suffixed with `.png`). They are loaded natively from the Java GUI engine.

Here are some commonly used examples based on category:

**Data Transformation & Manipulation:**
- `table_selection_column.png`
- `table_selection_row.png`
- `table_replace.png`
- `data_into_features.png`

**Exploratory Data Analysis (EDA) & Charts:**
- `chart_pie.png` (Good for standard summaries)
- `chart_line.png` (Good for lightweight or trending data)
- `clock.png` (Good for time-series operations)
- `chart_bar.png`
- `scatter.png`

**Validation & Comparison:**
- `id_cards.png` (Good for comparing datasets/subsets)
- `check.png`
- `compare.png`

**Machine Learning & Models:**
- `brain.png` (Deep Learning/AI)
- `lightbulb.png`
- `target.png`

## 3. Best Practices

1. **Avoid Custom Image Files:** Do not try to bundle your own `.png` images into the Python extension package unless strictly necessary. It bloats the package and often breaks during the Java Resource loading phase. Use the built-in AI Studio icon strings instead.
2. **Namespace:** Make sure the operator name in the TOML matches your python function name EXACTLY (e.g., `[operators.generate_standard_eda]`).
3. **Draft Builds:** If your icon doesn't update, remember to do a clean build of your extension (refer to the `build_ai_studio_extension` workflow).
