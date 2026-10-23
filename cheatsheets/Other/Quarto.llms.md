# Quarto

tool

web development

Quarto is a publishing tool that allows you to render markdown documents into websites, documents and presentations.

## Blocks

Collapsable callout block  
\`\`\`md ::: {.callout-warning collapse=true appearance=“simple”}\
\### Title :::

&nbsp;

    Panels
    : ```md
    ::: {.panel-tabset}
    ### Panel heading 1
    ### Panel heading 2
    :::

## Tables

Markdown table  
    Header col 1  | header col 2
    --------------|-----
    content col 1 | content col 2

    :  {tbl-colwidths="[50,50]"}

## Formatting

Font-color  
\`\`\`html

&nbsp;

    ## Keyboard shortcuts

    Format keyboard keys for different operating systems
    : 

    ```md
    {{< kbd mac=Shift-Command-O win=Shift-Control-O linux=Shift-Ctrl-L >}}

simple  

&nbsp;

    {{< kbd Shift-Ctrl-P >}}

Format arrow keys  

&nbsp;

    {{<kbd &uarr; >}}
    {{<kbd &darr; >}}
    {{<kbd &larr; >}}
    {{<kbd &rarr; >}}

↑↑ ↓↓ ←← →→
