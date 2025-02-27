# DOCX2DAISY

DOCX2DAISY is a Python library that converts Microsoft Word (.docx) documents into DAISY 3 compliant digital books. The output is a ZIP package containing all necessary files for a DAISY 3 book, including text and images. In addition, DOCX2DAISY provides document preparation checks to help ensure that your Word document is properly structured for conversion.

## Features

- **Comprehensive .docx Conversion**  
  Converts all text content (headings, paragraphs, lists, etc.) from .docx files into a structured DTBook XML format.

- **Image Extraction and Embedding**  
  Extracts images from the .docx (if present) and includes them in the DAISY package. Images are referenced in the DTBook XML via `mediaobject` elements with appropriate alternative text.

- **Document Preparation Checks**  
  Provides functions to verify that your document is properly prepared for conversion, checking for:
  - Essential metadata (Title, Author, Language)
  - Proper use of heading styles (e.g., Heading 1, Heading 2)
  - Alternative text for images
  - Presence of tables (and a note to provide descriptions, if needed)

- **Standards Compliance**  
  Generates the necessary DAISY 3 files:
  - **DTBook XML** (`book.xml`): Contains formatted text and images.
  - **NCX** (`book.ncx`): Provides navigation (table of contents) with proper DOCTYPE declaration.
  - **SMIL** (`book.smil`): Includes synchronization information with an empty `<audio>` tag for each navigation point and a DOCTYPE declaration.
  - **OPF** (`book.opf`): The package manifest linking all resources.

- **ZIP Packaging**  
  All generated files, including extracted images (if any), are bundled into a single ZIP package for easy distribution and use with DAISY readers.

## Requirements

- Python 3.x  
- [python-docx](https://python-docx.readthedocs.io/en/latest/)

Install the dependency via pip:

```bash
pip install python-docx
```

## Installation

To install the library locally, clone the repository and use pip:

```bash
git clone https://github.com/yourusername/DOCX2DAISY.git
cd DOCX2DAISY
pip install .
```

## Usage

### Document Preparation Checks

Before conversion, you can run a check on your .docx file to ensure proper preparation:

```python
from daisy_converter import check_docx_preparation

check_docx_preparation("sample.docx")
```

This will output warnings if essential metadata, headings, alternative text for images, or table descriptions are missing.

### Conversion Example

Below is an example script (`tests/test_convert.py`) demonstrating how to use DOCX2DAISY:

```python
from daisy_converter import docx_to_daisy, check_docx_preparation
import zipfile
import os

input_docx = "sample.docx"       # Path to your .docx file (ensure it exists)
output_zip = "sample_daisy.zip"  # Desired output ZIP package name

# 1. Run document preparation checks
print("Running document preparation checks:")
check_docx_preparation(input_docx)
print("-" * 40)

# 2. Convert the DOCX to a DAISY 3 package
docx_to_daisy(input_docx, output_zip)
print(f"Converted '{input_docx}' to DAISY 3 book: '{output_zip}'.")

# 3. Verify the contents of the generated ZIP file
if os.path.exists(output_zip):
    with zipfile.ZipFile(output_zip, 'r') as z:
        files = z.namelist()
        print("\nFiles in the ZIP package:")
        for f in files:
            print(" -", f)
        
        required_files = {"book.xml", "book.ncx", "book.smil", "book.opf"}
        missing = required_files - set(files)
        if missing:
            print("\nMissing required files:", missing)
        else:
            print("\nAll required files are present.")

        image_files = [f for f in files if f.startswith("images/") and not f.endswith("/")]
        if image_files:
            print("\nImage files found:", image_files)
        else:
            print("\nNo images found in the package.")
else:
    print("Conversion failed: Output ZIP file not found.")
```

## Project Structure

```
DOCX2DAISY/
├── docx2daisy/
│   ├── __init__.py
│   └── daisy_converter.py
├── tests/
│   └── test_convert.py
├── README.md
├── LICENSE
└── setup.py
```

## Contributing

Contributions, bug reports, and feature requests are welcome. Please open an issue or submit a pull request on GitHub.

## License

This project is licensed under the MIT License. See the LICENSE file for details.

## Acknowledgments

This library leverages the [python-docx](https://python-docx.readthedocs.io/en/latest/) library for processing Word documents and adheres to DAISY 3 specifications for digital talking books.
