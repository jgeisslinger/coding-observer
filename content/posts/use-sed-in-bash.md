---
title: "Use sed in bash script"
date: 2020-11-05T13:46:38+01:00
draft: false
comment: true
categories: "Shell"
tags: [bash, shell, sed, script, tutorial, sh, console]
Javascript: false
NodeJS: false
PowerApp: false
Tech: false
Misc: true
discussionId: simple-sed-2020
---

Good morning, 

I just coded on my new setup shell script for my Raspberry Pis check out on [GitHub](https://github.com/jgeisslinger/Pi-Setup) and observed that it's not quite self-explaining how to use 

```bash
sed
```

command to enter a specific path into a file already exiting. So let me recap the details for you. 

Remember the following while using ***sed***:
- If you want to make perma changes you must use "-i" after the sed command
- You should always use single quotes **'**. Otherwise sed may interprete the input variables for himself instead insert them into the file presented


## Case 1 - Replace line with matching pattern

To replace an existing line with a search pattern you can just use:

```bash
sed 's/searchvalue/replacevalue/' filename
```
where you replace the "searchvalue" with your pattern (like a variable in a config file) and the "replacevalue" with the new one. Please remember that the "s" prefix indicates that you want to replace the matching line. "filename" could be a path or a variable you defined and pointing to a specific file.

## Case 2 - Insert line after pattern
If you want to insert a line instead of replacing you can easy modify the code:

```bash
sed '/searchvalue/a replacevalue/' filename
```
So you can see that we don't use "s" but "a" after the second delimiter. This indicates to insert **after** the pattern.

## Case 3 - Insert line before pattern
In case you want to enter a line before the pattern you can modify the code as:
```bash
sed '/searchvalue/i replacevalue/' filename
```
So we just use "i" instead of "a" from the code before.

## And whats about the path?
After I solved the issue how to enter a line before the matching line I was wondering how to enter a path value that contains also slashes as the delimiter. If you just enter it as "replacevalue" you would observe that **sed** will interprete them as own delimiter instead of pasting them into the file. To get rid of this behaviour you have basically two options: Change the delimiter that sed uses or prefix the slashes. 

I would strongly recommend to use prefixes as you might not know which symbols are used in the expression that you parse to sed and you might face an issue if they match your new delimiters.

So to insert a path into a file you can just use:
```bash
sed '/searchvalue/i replace\/path\/value/' filename
```
So you observe that we just use the **backslash** within the path to tell sed that this is not a delimiter but part of the value we would like to insert (or replace).

## Recap

There are a lot of manipulations you can do with **sed** (official documentation could be found [here](https://www.gnu.org/software/sed/manual/sed.html)) Its much more powerful as just **echo** the lines into a file. However it could get really confusing from time to time and if you searching for complex pattern. I hope this short excurse saved you some time in research for that specific case.

Have a nice day,\
Jo
