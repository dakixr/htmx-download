# HTMX-Download Extension

This is a custom HTMX extension that allows for seamless file downloads (Excel, PDF, etc.) in single-page applications without disrupting the current page or requiring page reloads.

## Features
- Handles binary file downloads (Excel, PDF, etc.) with minimal JavaScript.
- Extracts the filename from the `Content-Disposition` header.
- Supports downloading files without swapping DOM elements.
- Gracefully handles server responses, including proper error status logging.

## Installation

Include the `htmx-download.js` extension file in your project after loading HTMX:

```html
<script src="https://unpkg.com/htmx.org"></script>
<script src="path/to/htmx-download.js"></script>
```

## Usage

To use the `htmx-download` extension, simply add the `hx-ext="htmx-download"` attribute to any HTMX-enabled element that triggers a file download.

### Example:

```html
<button hx-get="/download_file/123" hx-ext="htmx-download">
	Download File
  <img class="htmx-indicator" src="/spinner.gif">
</button>
```

### Backend Example (Django):

```python
import io
from django.http import FileResponse
import pandas as pd

def download_excel(request):
    data = {
      "calories": [420, 380, 390],
      "duration": [50, 40, 45]
    }
    df = pd.DataFrame(data)
    buffer = io.BytesIO()
    df.to_excel(buffer, index=False)
    buffer.seek(0)
    
    filename = "report.xlsx"
    return FileResponse(buffer, as_attachment=True, filename=filename)
```

## How It Works
1. **Intercepts the HTMX event**: The extension listens for the `htmx:beforeSwap` event.
2. **Handles binary response**: The server returns a binary file, and the extension extracts the `Content-Disposition` header to determine the filename.
3. **Triggers download**: A temporary `<a>` tag is created, triggering the file download without affecting the SPA page.

## Notes
- Ensure your server response includes appropriate headers such as `Content-Disposition` and `Content-Type`.
- The extension works best with binary file responses, where the `responseType` is set to `arraybuffer`.
