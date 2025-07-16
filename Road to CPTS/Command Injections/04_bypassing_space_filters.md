# Bypassing Space Filters

New-line character  `%0a` <br>
Tab character `%09`

## $IFS
`$IFS` (Internal Field Separator) defines the characters the shell uses to split input text into separate worsd or fields. By default, the value is: <br>
`$IFS = <space><tab><newline>`

Example of when IFS is used:
```
list="one two three"
for word in $list; do
  echo "$word"
done
```
IFS determines how `$list` is split.

## Using Brace Expansion
Bash Brace Expansion feature automatically adds space between arguments wrapped between braces
```
farelmusyaffa@htb[/htb]$ {ls,-la}

total 0
drwxr-xr-x 1 21y4d 21y4d   0 Jul 13 07:37 .
drwxr-xr-x 1 21y4d 21y4d   0 Jul 13 13:01 ..
```
