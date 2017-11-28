---
title: So You Hosed Your Mac OS Python Install
date: '2017-10-16 01:57:19'
---

Mac OS ships with Python 2.7, but before long most users will want to install Python 3. The easiest way to do this is via [Homebrew](https://brew.sh/).

Everything is fine and dandy until one day you try to run create a Python 3 virtual environment and get an error message:

```
dyld: Library not loaded: @executable_path/../.Python
  Referenced from: /Library/Frameworks/Python.framework/Versions/3.6/bin/python3.6
  Reason: image not found
Abort trap: 6
```

You do some Googling and figure you just need to uninstall and reinstall Python 3:
```
brew remove python3
brew cleanup && brew prune
brew install python3
```

Homebrew installs Python3 successfully, so you update pip3 as the installer recommends...
```
pip3 install --upgrade pip setuptools wheel
```
...and get the same error you had before:
```
dyld: Library not loaded: @executable_path/../.Python
  Referenced from: /Library/Frameworks/Python.framework/Versions/3.6/bin/python3.6
  Reason: image not found
Abort trap: 6
```
At this point, you become concerned. Further Googling turns up some people suggesting you'll need to take drastic measures; e.g. re-install Mac OS.

Take a deep breath, and try something else. Run
```
which python3
```
and figure out where your Python 3 is installed. Assuming it is something like `/Library/Frameworks/Python.framework/Versions/3.6/bin/python`, delete everything under and including `/Library/Frameworks/Python.framework`.

Just to be sure, run
```
brew doctor
```
and if it reports any broken symlinks, run
```
brew prune
```
again.

Finally, reinstall Python3 using Homebrew.
```
brew install python3
```
With any luck, Python 3 should be back to normal!
