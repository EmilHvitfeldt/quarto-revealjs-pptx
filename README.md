# Quarto RevealJS to PPTX Conversion

This project demonstrates how to convert a Quarto RevealJS presentation to PowerPoint (PPTX) format.

## Files

- `slides.qmd` - The Quarto source file
- `slides.html` - The rendered RevealJS presentation
- `slides.pdf` - PDF export of the presentation
- `slides.pptx` - Final PowerPoint file

## Process

### 1. Create the Quarto RevealJS presentation

```bash
quarto render slides.qmd
```

### 2. Export to PDF using PDF Export Mode

Open the rendered `slides.html` in a browser and add `?print-pdf` to the URL:

```
file:///path/to/slides.html?print-pdf
```

Then use the browser's Print function (Cmd+P / Ctrl+P) and save as PDF.

### 3. Convert PDF to PPTX using LibreOffice

```bash
soffice --headless --infilter="impress_pdf_import" --convert-to pptx --outdir . "slides.pdf"
```

### 4. Fix font references

The PDF uses embedded fonts (e.g., SourceSansPro) that may not map correctly to system fonts. To fix this, extract the PPTX (which is a ZIP archive), replace font names in the XML files, and repackage:

```bash
# Extract PPTX
mkdir pptx_edit && cd pptx_edit
unzip "../slides.pptx"

# Replace font references
find . -name "*.xml" -exec sed -i '' \
  's/SourceSansPro-Semibold/Source Sans 3 Semibold/g;
   s/SourceSansPro-Regular/Source Sans 3/g;
   s/SourceSansPro/Source Sans 3/g' {} \;

# Repackage
zip -r "../slides.pptx" .
```

## Requirements

- [Quarto](https://quarto.org/)
- [LibreOffice](https://www.libreoffice.org/) - Install via `brew install --cask libreoffice`
- Source Sans 3 font (or adjust font substitution as needed)

## Limitations

The PDF Export Mode flattens the presentation to static slides. The following RevealJS features are **not supported**:

### Animations & Transitions
- Slide transitions (fade, slide, convex, etc.)
- Fragment animations (incremental bullet points, `.fragment` class)
- Auto-animate between slides
- CSS animations and transforms

### Interactive Elements
- Embedded videos (only shows a static frame or placeholder)
- Embedded iframes (web content, Shiny apps, Observable notebooks)
- Interactive widgets (htmlwidgets, Plotly hover/zoom)
- Hyperlinks (converted to plain text)

### Speaker Features
- Speaker notes are not included in the PDF
- Speaker timer and preview

### Layout & Styling
- Vertical slides (nested slides) are flattened
- Background videos
- Some custom CSS may not render correctly
- Scrollable content (tall code blocks, tables) may be cut off

### Other
- Chalkboard/drawing annotations
- Multiplex (remote control) functionality
- Keyboard shortcuts and navigation
