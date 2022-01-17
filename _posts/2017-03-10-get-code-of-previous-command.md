---
layout: post
title: How to get result code AND output of command in Bash
author: Martin Rotter
date: 2017-03-10 08:00:00 +0200
category: it-programming
language: en
---

Let's say you have some command and you are interested in its *result code* and *output* in the SAME TIME. Problem is that when you use `$()` construct to get output of some command, then you do not have any way to get its result code.

Well, in that case, why not use pipes and `read` command? See:

```bash
some-command | read command_output

echo "Result code of command is: $?"
echo "Output of command is: $command_output"
```

`read` reads input from the pipe and stores it in `command_output` variable. Simple, works.