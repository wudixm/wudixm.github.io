### vim surround

Surround.vim is all about "surroundings": parentheses, brackets, quotes, XML tags, and more.  The plugin provides mappings to easily delete, change and add such surroundings in pairs.  While it works under Vim 6, much of the functionality requires Vim 7. 

Examples follow.  It is difficult to provide good examples in the variable width font of this site; check the documentation for more. 

Press cs"' (that's c, s, double quote, single quote) inside 

"Hello world!" 

to change it to 

'Hello world!' 

Now press cs'<q> to change it to 

<q>Hello world!</q> 

To go full circle, press cst" to get 

"Hello world!" 

To remove the delimiters entirely, press ds" . 

Hello world! 

Now with the cursor on "Hello", press ysiw] (iw is a text object). 

[Hello] world! 

Let's make that braces and add some space (use "}" instead of "{" for no space): cs]{ 

{ Hello } world! 

Now wrap the entire line in parentheses with yssb or yss) . 

({ Hello } world!) 

Revert to the original text: ds{ds) 

Hello world! 

Emphasize hello: ysiw<em> 

<em>Hello</em> world! 

Finally, let's try out visual mode. Press a capital V (for linewise visual mode) 
followed by S<p>. 

<p> 
  Hello world! 
</p> 

This plugin is very powerful for HTML and XML editing, a niche which currently seems underfilled in Vim land.  (As opposed to HTML/XML *inserting*, for which many plugins are available).  Adding, changing, and removing pairs of tags simultaneously is a breeze. 

The "." command will work with ds, cs, and yss if you install repeat.vim, [vimscript #2136](https://vim8.org/scripts/script.php?script_id=2136). 

All feedback appreciated. 

<http://github.com/tpope/vim-surround>



### color scheme

First of all let's start with basics. Pick your favorite plugin manager: [Pathogen](https://github.com/tpope/vim-pathogen), [Vundle](https://github.com/gmarik/vundle), [NeoBundle](https://github.com/Shougo/neobundle.vim)or [vim-plug](https://github.com/junegunn/vim-plug), just to name a few. Pathogen is the oldest, simplest, and most featureless. Vundle is very popular and I've used it for a long time before. If you're using some of [Shougo's plugins](https://github.com/Shougo), you should probably pick NeoBundle. Last but not least is vim-plug, which I've recently switched to.

- Pathogen
  - `git clone https://github.com/morhetz/gruvbox.git ~/.vim/bundle/gruvbox`
- Vundle
  - Add `Plugin 'morhetz/gruvbox'` to your .vimrc and run `:PluginInstall`
- NeoBundle
  - Add `NeoBundle 'morhetz/gruvbox'` to your .vimrc and run `:NeoBundleInstall`
- vim-plug
  - Add `Plug 'morhetz/gruvbox'` to your .vimrc and run `:PlugInstall`
- Vim-8
  - `git clone https://github.com/morhetz/gruvbox.git ~/.vim/pack/default/start/gruvbox`

Then add the line `colorscheme gruvbox` to your .vimrc file, and restart vim.

If you're using GUI version of vim that should be enough. Otherwise let me guide you through the hell of [terminal specific issues](https://github.com/morhetz/gruvbox/wiki/Terminal-specific).