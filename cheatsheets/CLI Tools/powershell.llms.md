# Powershell 

CLI

tool

Powershell is the major command line tool for windows

## Basics

### Files

List contents of directory  
\`\`\`Powershell dir \# or ls

&nbsp;

    Get current working directory
    : ```powershell
    pwd

Change directory  
\`\`\`powershell cd \# or chdir

&nbsp;

    Delete file / folder
    : ```powershell
    del
    # or
    rm
    # or
    rmdir

Copy file  
\`\`\`powershell cp \# or copy

&nbsp;

    Move file 
    : ```powershell
    mv
    # or
    move

Create variables  
\`\`\`powershell \$myVariable = “C:”

&nbsp;

    ### Processes

    List all running processes, ordered by cpu-usage
    ```powershell
    Get-Process |Sort-Object cpu -Descending

## Python

execute python program  
\`\`\`powershell py path.py

&nbsp;

    install package with pip
    : ```powershell
    py -m pip install pandas
