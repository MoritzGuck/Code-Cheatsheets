# Code-Cheatsheets

A collection of cheatsheets for programming written in qmd (Quarto markdown).
They are rendered into a static website using [Quarto](quarto.org).

You can find the website [here](https://moritzguck.github.io/Code-Cheatsheets/).

If you find mistakes or want to contribute, please create an issue. 

## How to use

The markdown-files must be rendered into html-pages.

- **In Github actions**: Use the github workflows in `.githb`
- **On your local device**: 
  - By installing Quarto `python3 -m pip install jupyter matplotlib plotly`
  - ... and then previewing the change: `quarto preview [hello.qmd]`
  - ... or rendering it: `quarto render [hello.qmd] --to html`