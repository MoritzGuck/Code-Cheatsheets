# Quarto

tool

web development

Quarto is a publishing tool that allows you to render markdown documents into websites, documents and presentations.

## Blocks

Collapsable callout block  

&nbsp;

    ::: {.callout-warning collapse=true appearance="simple"}  
    ### Title 
    Content
    :::

> **WARNING:**
>
> Content

Panels  

&nbsp;

    ::: {.panel-tabset}
    ### Panel heading 1
    Content 1

    ### Panel heading 2
    Content 2
    :::

## Panel heading 1

Content 1

## Panel heading 2

Content 2

## Tables

Markdown table  
    Header col 1  | header col 2
    --------------|-----
    content col 1 | content col 2

    :  {tbl-colwidths="[50,50]"}

| Header col 1  | header col 2  |
|---------------|---------------|
| content col 1 | content col 2 |

## Formatting

Font-color  

``` html
<font color="grey">
</font>
```

## Keyboard shortcuts

Format keyboard keys for different operating systems  

&nbsp;

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
