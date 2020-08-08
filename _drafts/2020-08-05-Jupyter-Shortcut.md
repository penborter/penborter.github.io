---

layout: post
title: Creating a shortcut for Jupyter Notebook
description: Creating a shortcut for Jupyter Notebook
summary: Creating a shortcut for Jupyter Notebook
tags: [python, data, productivity]

---

A quick post to keep the ball rolling. I wanted to streamline the process of launching Jupyter, so I created a simple bash script to launch it anytime, anywhere.

Note: this is for MacOS 

Put the following in a plaintext file, and save the file as ```jupyter.command``` (or whatever name you want, as long as the file extension is the same).

```bash
#!/bin/bash

jupyter notebook
```

The first line is the [Shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)), used by Unix operating systems to specify the interpreter to be used for the program. The next line is our bash command to be run, launching Jupyter. 

Next step is to authorize Terminal to execute the script. 

