Compares NVIM v0.5.0-dev+1056 vs vim 8.2.2434, current as of 2021-01-31

--------------------------------------------------------------------------------
Things changed in neovim:

General:
. Neovim is licensed under the terms of the Apache 2.0 license, except for parts
  of Neovim that were contributed under the Vim license.
. Nvim is always "nocompatible"!
. Nvim always builds with all features, in contrast to Vim which may have
  certain features removed/added at compile-time.
. Defaults (option values, etc.) are quite different.
. The "base" (root) directories conform to the XDG Base Directory Specification.
. External plugins run in separate processes. This improves stability and allows
  those plugins to work without blocking the editor. Even "legacy" Python and
  Ruby plugins which use the old Vim interfaces run out-of-process.
. Viminfo text files were replaced with binary (messagepack) ShaDa files. ShaDa
  file format was designed with forward and backward compatibility in mind. More
  error-resilient, keeps search direction (viminfo does not).
. Lua interface (see documentation for details):
    . Lua has direct access to Nvim API via vim.api.
    . Lua package.path and package.cpath are automatically updated according to
      'runtimepath'.
    . :lua print() behavior changed.
    . :lua error() behavior changed.
. If a Python interpreter is available on your $PATH, :python and :python3 are
  always available and may be used simultaneously.
. Replay of a macro recorded during :lmap produces the same actions as when it
  was recorded. In Vim if a macro is recorded while using :lmap'ped keys then
  the behaviour during record and replay differs.
. 'keymap' is implemented via :lmap instead of :lnoremap so that you can use
  macros and 'keymap' at the same time. This also means you can use :imap on the
  results of keys from 'keymap'.
. The jumplist avoids useless/phantom jumps.
. Shell output (:!, :make, etc.) is always routed through the UI, so it cannot
  "mess up" the screen.
. Nvim throttles (skips) messages from shell commands (:!, :grep, :make) if
  there is too much output. No data is lost, this only affects display and
  improves performance. :terminal output is never throttled.
. Visual selection highlights the character at cursor.
. Nvim does not have special t_XX options nor <t_XX> keycodes to configure
  terminal capabilities. Instead Nvim treats the terminal as any other UI, e.g.
  'guicursor' sets the terminal cursor style if possible.
. Nvim will use 256-colour capability on Linux virtual terminals. Vim uses only
  8 colours plus bright foreground on Linux VTs.
. Nvim has no direct connection to the system clipboard. Instead it depends on a
  provider which transparently uses shell commands to communicate with the
  system clipboard or any other clipboard "backend".
. Platform and I/O facilities are built upon libuv.

Functions:
. system() can run {cmd} directly (without 'shell').
. systemlist() can run {cmd} directly (without 'shell').
. mkdir() behaviour changed (in short, permissions and error reporting, but
  see documentation).
. string() behaves differently with nested containers, see documentation.
. json_decode() may output msgpack-special-dict, also in case of duplicate
  keys (no error), accepts only valid JSON.
. json_encode() --- msgpack-special-dict values are accepted, but v:none is not.
. printf() returns something meaningful when used with '%p' argument.
. input() and inputdialog() support for each other’s features (return on
  cancel and completion respectively).
. input() and inputdialog() support user-defined cmdline highlighting.

Commands:
. :Man --- view manpages (highlighting, completion, locales, and navigation).
  Replaced with Lua implementation in neovim.
. :redir nested in execute() works.
. :echo behaves differently with nested containers, see documentation.
. :doautocmd does not warn about "No matching autocommands".

Highlight groups:
. hl-ColorColumn, hl-CursorColumn are lower priority than most other groups.
. hl-CursorLine is low-priority unless foreground color is set.

Normal mode commands:
. Q is the same as gQ.

Options:
. 'ttimeout', 'ttimeoutlen' behavior was simplified.
. 'term' reflects the terminal type derived from $TERM and other environment
  checks (for debugging only).
. 'maxcombine' (6 is always used).
. 'fsync' behavior was changed.

Startup:
. -e and -es invoke the same "improved Ex mode" as -E and -Es.
. -E and -Es read stdin as text (into buffer 1).
. -es and -Es have improved behavior (auto-quit, skips swap-file dialog).
. -s reads Normal commands from stdin if the script name is "-".
. Reading text (instead of commands) from stdin "--" works by default, "-" file
  is optional, works in more cases: -Es, file args.
. Start Nvim with 'verbose' level 3 to show terminal capabilities: nvim -V3

VimL compatibility:
. Some variables without v: are not aliased to 'v:' variables: count, errmsg,
  shell_error and this_session.
. Does not support :scriptversion!

Other:
. c_CTRL-R pasting a non-special register into cmdline omits the last <CR>.
. Some obsolete features are removed: :fixdel; :helpfind; :mode no longer
  accepts an argument; :open; :Print; :shell.
. Signs are removed if the associated line is deleted.
. v:progpath is always absolute ("full").
. v:windowid is always available (for use by external UIs).

--------------------------------------------------------------------------------
neovim-only features:

"Major" features (unfinished / unreleased are marked [WIP]):
. Nvim exposes an API that can be used by plugins and external processes
  via RPC, Lua and VimL.
. The Lua language is builtin and always available. Nvim includes a "standard
  library" for Lua.
. Builtin support for the Language Server Protocol (LSP). [WIP]
. The vim.lsp Lua module --- a framework for building LSP plugins. [WIP]
. Nvim integrates the tree-sitter library for incremental parsing of buffers.
  It does not provide the tree-sitter parsers, these must be built separately.
  Provides tree-sitter syntax tree queries and syntax highlighting. [WIP]

Events:
. BufModifiedSet --- After the 'modified' value of a buffer has been changed.
. ChanInfo --- State of channel changed.
. ChanOpen --- Just after a channel was opened.
. TabNewEntered --- After entering a new tab page.
. TermChanged --- !UNDOCUMENTED!
. TermClose --- When a terminal job ends.
. TermEnter --- After entering Terminal-mode.
. TermLeave --- After leaving Terminal-mode.
. WinClosed --- After closing a window.
. WinScrolled --- After scrolling the viewport of the current window.

Functions ("major" differences-related (jobs, floatwin, etc.) are omitted):
. dictwatcheradd() --- add a callback whenever a Dict is modified.
. dictwatcherdel() --- remove a watcher added with dictwatcheradd().
. menu_get() --- return a List of Dicts describing menus (:menu, etc.).
. msgpackdump() --- convert a list of VimL objects to msgpack.
. msgpackparse() --- convert a readfile()-style list to a list of VimL objects.
. nvim_parse_expression() --- parse a VimL expression.
. stdpath() --- return locations of various default files and directories.
. wait() --- wait until {condition} evaluates to TRUE, where {condition} is a
  Funcref or string containing an expression.
. Context-related functions: ctx*

Nvim API functions. Only ones providing new (or really improved) features:
. nvim_get_all_options_info() --- get the option information for all options.
. nvim_get_color_by_name() --- get 24-bit RGB value of a color name or #rrggbb.
. nvim_get_keymap() --- get a list of global mapping definitions.
. nvim_get_proc() --- get info describing process {pid}.
. nvim_get_proc_children() -- get the immediate children of process {pid}.

Commands:
. :sign-define accepts a 'numhl' argument, to highlight the line number.
. :highlight option "blend=" --- controls blend level for a highlight group
  within the popupmenu or floating window.

Highlight groups:
. Expressions entered in i_CTRL-R_=, c_CTRL-\_e, "= are highlighted by the
  builtin expressions parser, several highlight groups are defined for that
  (see :help expr-highlight).
. hl-NormalFloat --- floating window.
. hl-NormalNC --- non-current windows.
. hl-MsgArea --- messages/cmdline area.
. hl-MsgSeparator --- separator for scrolled messages.
. hl-Substitute --- :substitute replacement text.
. hl-TermCursor --- cursor in a focused terminal.
. hl-TermCursorNC --- cursor in an unfocused terminal.
. hl-Whitespace --- 'listchars' whitespace.

Input/Mappings:
. ALT (META) chords always work, with any key (<M-BS>, <M-Space>, <M-Enter>,
  etc.). Case-sensitive: <M-a> and <M-A> are two different keycodes.
  ALT behaves like <Esc> if not mapped.

Normal mode commands:
gO --- shows a filetype-defined "outline" of the current buffer.

Options:
. 'cpoptions' --- flags: cpo-_ .
. 'display' --- flags: "msgsep" minimizes scrolling when showing messages.
. 'guicursor' --- works in the terminal.
. 'fillchars' --- flags: "msgsep", "eob" for "end of a buffer" character,
  "foldopen", "foldsep", "foldclose". Local to window.
. 'foldcolumn' --- supports up to 9 dynamic/fixed columns.
. 'inccommand' --- shows interactive results for :substitute-like commands.
. 'jumpoptions' --- make the jumplist behave like the tagstack / web browser.
. 'listchars' --- local to window.
. 'pumblend' --- pseudo-transparent popupmenu.
. 'redrawdebug'--- change the way redrawing works, for debugging purposes.
. 'scrollback' --- maximum number of lines kept beyond the visible screen.
. 'signcolumn' --- supports up to 9 dynamic/fixed columns.
. 'statusline' --- supports unlimited alignment sections.
. 'tabline' --- %@Func@foo%X can call any function on mouse-click.
. 'wildoptions'--- "pum" flag to use popupmenu for wildmode completion.
. 'winblend' --- pseudo-transparency in floating windows.
. 'winhighlight' --- window-local highlights.

--------------------------------------------------------------------------------
vim-only features:

"Major" features (unfinished / unreleased are marked [WIP]):
. Vim9 script --- alternative to legacy vim script. Main goal is to drastically
  improve performance, secondary goal is to avoid Vim-specific constructs and
  get closer to commonly used programming languages. [WIP]
. The OLE interface --- vim acts as an OLE automation server.
. NetBeans protocol --- a socket interface for vim integration into an IDE.
. Builtin LogiPat plugin --- takes Boolean logic arguments and produces a
  regular expression which implements the choices.

General:
. Versions with builtin GUI (:help GUI).
. X11 integration.
. python-bindeval (object providing access to vim Dictionary type) and
  python-Function (function-like object, acting like vim Funcref) are supported.
. The Tcl interface to vim (:help tcl).
. The MzScheme interface to vim (:help mzscheme).
. Compile-time options: EBCDIC, Emacs tags support.
. Aliases for running vim (ex, exim, gex, gview, gvim, gvimdiff, rgview, rgvim,
  rview, rvim, view, vimdiff).
. --literal startup option.
. "exrc" support (might be security risk).
. Sounds-playing support functions: sound_clear(), sound_playevent(),
  sound_playfile(), sound_stop().
. When setting 'background' to the default value with ":set background&" vim
  will guess the value.
. Builtin Vimball support plugin.

Functions ("major" differences-related (jobs, popups, etc.) are omitted):
. js_encode() --- valid encoding for JavaScript.
. js_decode() --- opposite of js_encode().
. charclass() --- character class of {string}.
. charcol() --- column number of cursor or mark.
. charidx() --- char index of byte {idx} in {string}.
. chdir() --- change current working directory.
. echoraw() --- output {expr} as-is.
. extendnew() --- like extend() but creates a new List or Dictionary.
. getcharpos() --- get position of cursor, mark, etc.
. getcursorcharpos() --- character position of the cursor.
. getreginfo() --- get information about a register.
. gettext() --- lookup translation of message {text}.
. mapnew() --- like but creates a new List or Dictionary.
. mapset() --- restore mapping from maparg() result.
. matchfuzzy() --- fuzzy match {str} in {list}.
. matchfuzzypos() --- fuzzy match {str} in {list}.
. rand() --- get pseudo-random number.
. readblob() --- read a Blob from {fname}.
. readdirex() --- file info in {dir} selected by {expr}.
. reduce() --- reduce {object} using {func}.
. screenchars() --- list of characters at screen position.
. screenstring() --- characters at screen position.
. searchcount() --- get or update search stats.
. setcellwidths() --- set character cell width overrides.
. setcharpos() --- set the {expr} position to {list}.
. setcursorcharpos() --- move cursor to position in {list}.
. sign_placelist() --- place a list of signs.
. sign_unplacelist() --- unplace a list of signs.
. strptime() --- convert {timestring} to unix timestamp.
. typename() --- representation of the type of {expr}.
. win_execute() --- execute {command} in window {id}.
. win_splitmove() --- move window {nr} to split of {target}.
. system() --- supports writing/reading "backgrounded" commands.
. Assert functions: assert_*
. Test functions: test_*

Options (obsolete options omitted):
. 'completeopt' --- "popup" flag, shows extra information about the currently
  selected completion in a popup window.
. 'cryptmethod' and 'key' --- vim encryption implementation.
. 'cursorlineopt' --- list of settings for how 'cursorline' is displayed.
. 'encoding' --- allows "internal" non-utf-8 encodings.
. 'guioptions' --- 't' (tearoff menus), '!' (external commands are executed in a
  terminal window) flags.
. 'highlight' --- allows to set highlight groups for various occasions.
. 'nrformats' --- unsigned flag (numbers are recognized as unsigned).
. 'maxmem' --- maximum amount of memory to use for one buffer.
. 'maxmemtot' --- maximum amount of memory to use for all buffers together.
. 'previewpopup' ---- if not empty a popup window is used for commands that
  would open a preview window.
. 'quickfixtextfunc' --- function to be used to get the text to display in the
  quickfix and location list windows.
. 'restorescreen' --- whether to restore the screen contents when exiting Vim.
. 'scrollfocus' --- scroll focus behavior, only for MS-Windows GUI.
. 'swapsync' --- if a swap file is synced to disk after writing to it.
. 'varsofttabstop' --- list of the number of spaces that a <Tab> counts for
  while editing, such as inserting a <Tab> or using <BS>.
. 'vartabstop' --- list of the number of spaces that a <Tab> counts for.
. Input Method support options: imactivatefunc, imactivatekey, imstatusfunc.
. Terminal-related options: term*

Events:
. SafeState --- nothing is pending, going to wait for a typed character.
. SafeStateAgain --- like SafeState but after processing any messages and
  invoking callbacks.
. TerminalWinOpen --- after a terminal buffer was created.

Commands:
. :! supports "interactive" commands.
. :!start is special-cased on Windows.
. :balt --- like :badd and also set the alternate file for the current window.
. :breakadd expr {expression} --- breakpoint that will break whenever the
  {expression} evaluates to a different value.
. :filter --- works with :registers.
. :scriptversion --- specifies the version of VimL for the lines that follow
  in the same file.
. :smile
. :sort --- with 'l' uses the current collation locale.
. :spellrare --- add word as a rare word to 'spellfile'.

Highlights:
. LineNrAbove --- Line number for when the 'relativenumber' option is set, above
  the cursor line.
. LineNrBelow --  Line number for when the 'relativenumber' option is set, below
  the cursor line.

Variables:
. v:none (the same as v:null).
. v:numbermax --- maximum value of a number.
. v:numbermin --- minimum value of a number (negative).
. v:numbersize --- number of bits in a number.
. v:versionlong --- like v:version, but also including the patchlevel.
. Variables for OptionSet autocommand: v:option_oldlocal, v:option_oldglobal
  and v:option_command.

--------------------------------------------------------------------------------
Different implementations of similar things:
. Job control APIs are different.
. Nvim's "floating windows" and "virtual texts" APIs are different from "popups"
  and "text properties" APIs in vim.
. Builtin terminal emulators are different.
. Nvim's buffer-local highlighting (api-highlights) plus Extended marks
  (api-extended-marks) APIs are different from vim's text properties.
. Vim has listener functions (callbacks for changes in buffers, see listener_*);
  nvim has buffer update events (nvim_buf_* functions).

--------------------------------------------------------------------------------
Irrelevant to vim or neovim:

Events:
 . UIEnter --- After a UI connects via nvim_ui_attach().
 . UILeave --- After a UI disconnects from Nvim.

Highlight groups:
. hl-StatusLineTerm and hl-StatusLineTermNC are unnecessary in Nvim
  because it supports window-local highlights.

--------------------------------------------------------------------------------
--------------------------------------------------------------------------------
These are incorrectly documented in neovim as differences from vim:

. :autocmd accepts the '++once' flag

Events:
 . Signal --- After Nvim receives a signal (*only* SIGUSR1). Vim: SigUSR1
   -- Documented twice, later as:
      "*SigUSR1* Use |Signal| to detect `SIGUSR1` signal instead."
 . TermOpen --- TerminalOpen in vim.
 . VimResume
 . VimSuspend
