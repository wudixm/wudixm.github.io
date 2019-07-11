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
