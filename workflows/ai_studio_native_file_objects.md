---
description: How to pass native HTML, JSON, or Image Blob data out of a python Operator to a FileObject port.
---

# Native AI Studio FileObjects from Python

By default, an AI Studio Operator expects an `ExampleSet` (a Pandas DataFrame) going in and coming out. If an Operator generates a physical file (like an HTML report, image, or JSON), you MUST map it to a Blob `FileObject` so it can be handled by a "Write File" operator on the canvas.

## 1. Extension.toml Wiring
In your `extension.toml`, you must manually override the output ports to match AI Studio shorthand conventions so the UI handles it cleanly.
* `exa` = ExampleSet (Dataframe)
* `fil` = FileObject (Blob)

```toml
[operators.my_custom_operator]
icon = "document.png"
outputs = ["exa", "fil"]
```

## 2. Python Return Types
AI Studio's internal `altair_aitools` annotations parser expects a very specific Python type to render the Blob correctly. You must use `io.BytesIO`. 
*Do NOT use `bytes` directly, as AI Studio will classify it as an internal Python object that cannot cross standard canvas boundaries.*

```python
import pandas as pd
import io

def my_custom_operator(
    exa: pd.DataFrame
) -> tuple[pd.DataFrame, io.BytesIO]:
    """
    :param exa: The input dataset.
    :return: 
        - port 1 (result): The unaltered original ExampleSet dataset.
        - port 2 (result2): The generated document FileObject, connect this to a 'Write File' operator.
    """
    html_content = "<h1>Hello FileObject!</h1>"
    
    # Encode string data to bytes, then wrap it in io.BytesIO
    file_payload = io.BytesIO(html_content.encode("utf-8"))
    
    return exa, file_payload
```
