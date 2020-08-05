VSCode PyLint Issues
---

I was having problems with pylint even being installed after I changed the name
of the directory because I changed the name of the repo.  In trying to fix it I
ended up breaking pylint everywhere.  I eventually hardcoded the pylint executable
in preferences for the pylint path.  This made pylint work but it wasn't finding
a dependency I install with pip.  I wasn't able to fix this till I changed the
interpreter to the local one for the repo which I found at [1].  `.env/bin/python3`
because I pip install the requirements.txt into .env.

```
I was facing same issue (VS Code).Resolved by below method

1) Select Interpreter command from the Command Palette (Ctrl+Shift+P)

2) Search for "Select Interpreter"

3) Select the installed python directory

Ref:- https://code.visualstudio.com/docs/python/environments#_select-an-environment
```

[1] - https://stackoverflow.com/questions/43574995/visual-studio-code-pylint-unable-to-import-protorpc
