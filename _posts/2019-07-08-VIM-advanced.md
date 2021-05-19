1. 80i*<escape> Insert 80 * characters. //********************************************************************************
2. dT} Delete backwards from cursor until previous }.
3. gx Go to url under your cursor in a browser.http://www.baidu.com
4. CTRL-o Go backwards in the jumplist (list of where your cursor has been). Trust me this is like movement steroids.
5. gp Pastes just like p but leave the cursor after the pasted text.
6. `:%g/pattern/norm @q` Run macro q on all lines in a file that match a pattern.
7. `:set paste` Set this if you are pasting in content from the system clipboard. Trust me.
8. `q` records things - so it doesn’t do much on its own. You need to tell it what register to store the recorded sequence in.
9. `"%p` Pastes in the name of the current file.// 2019-07-08-VIM-advanced.md
10. `:e .` open file explorer in current directory.
11. `da"` Delete everything with quotes wrapped around (including the quotes).  // (fdfdsafaf)
12. `2df"` Delete from cursor to find the 2nd quote mark. This is inclusive so it will delete the second quote. This is a handy command for deleting attributes in html if your cursor is on the first letter of the attribute. // class="test" style="fda" 
13. `:12,54=` Format lines 12 through 54.
14. `:h i_CTRL-R` Opens vim help to documentation on pressing control and r while in insert mode.
15. `:r !tree` Pastes in the output from running tree on a directory.
16. `gq` Format selected text.
17. `gn` Grab the next match from last search and visually select it.
18. `"*p` Pastes in text from your system clipboard.
19. `gv` Reselect last visual selection.
20. `/^}` Forward search for closing bracket of a css class, if the css class is closed at the beginning of a new line i.e.
21. `dl` Delete character under cursor. Same as x.
22. `4i<tab><escape>` Insert 4 tabs (leaves you in command mode, not insert mode).
23. `:r !ls` Pastes in the output of ls. ! calls an external process in vim. So this can be pretty userful.
24. `:ea 14h 30m` jump to 14 hours and 30 minutes ago. Ok you get the point.
25. `gi` Go into insert mode at the end of the last insert you did.
26. `12>>` Indent 12 lines including the line you are on.
27. `:12,42wq` saves lines 12 to 42 and quits file.
28. `"2p` This will paste in text from the second register. You will use this all of the time. Most useful when you delete something you want to paste, then delete something else. Move to the place where you want to paste text, hit p and go “doh”. Just remember "2p.
29. `:%g/^\d/norm yyGp` This searches for all lines of a file that start with a digit as the first character. It then copies the line and pastes it at the bottom of the file.
30. `gv` Selects the previous visual.
31. `v_o` Goes to the other end of the visual block. Useful if you started one line too low or something. In block mode, it goes to the opposite diagonal corner: use v_O to go to the opposite horizontal corner..
32. `g ctrl-A / ctrl-X` In visual mode, ctrl-A just increments the first number on every line. g ctrl-A, on the other hand, will bump the increment by one for each matching line. 
33. `:vert[ical] ` 后面可接各种命令，`:vertical` works with any command that splits a window, `:vert term`，
34. `g/pattern/d` 删除包含特定字符的行 (全局删除匹配行) `1,5g/pattern/d` (删除第1-5行里的匹配行)
35. `v/pattern/d` 删除不包含指定字符的行
36. `g!/pattern/d` （全部删除）
37. `:%g/pattern/norm @q` Run macro q on all lines in a file that match a pattern.
38. `ge` jumps to the end of the previous word.
39. `gP` Pastes just like P but leave the cursor after the pasted text.
40. `"xyy` Copies current line into register x.
41. `:56,99>` Indent lines 56 through 99.
