Intro
=====

Vlime is a Common Lisp dev environment for Vim, similar to SLIME for Emacs and
SLIMV for Vim (and yes, I dug around SLIMV's source code when writing Vlime).

Vlime provides REPL integration inside Vim, as well as omni-completions and
basic SLDB support. And, optionally, it makes you a lot sexier.

Why?
====

* There were barely no choice besides SLIMV.
* SLIMV was written in Python on the Vim side, but I think a Lisp-and-Vimscript
  implementation would be awesome.
* Vim 8 has these nice `channel` APIs, why not try them out?

Current State
=============

Vlime is currently in a pre-alpha state. One should expect changes in APIs and
key bindings etc.

Dependencies
============

Must-have:

* Vim 8.0+ and +channel
* ASDF
* Quicklisp
* An Internet connection to install other dependencies from Quicklisp

Note that there was a bug in the channel implementation of Vim, which may
cause large messages to be dropped incorrectly. This was fixed in 8.0.0312,
so using 8.0.0312 or a more recent version is strongly recommended.
[Details](https://groups.google.com/d/topic/vim_dev/Rl0X_R5pjxk/discussion).

Currently Vlime can only detect s-expressions inside parentheses. To make your
life easier, use [paredit](https://github.com/kovisoft/paredit).

Supported CL Implementations
============================

Some major CL implementations are listed below. The ones marked with `O` work
fine with Vlime. If you tried out Vlime with an implementation not listed
here, please let me know.

```
Implementation  Version  Working  Notes
--------------------------------------------------------------
ABCL            1.4.0       X     Networking code malfunction
Allegro CL      10.0        O     Tested with the Express Edition
CLISP           2.49+       X     No multithreading support
ECL             16.1.3      X     Partial multithreading support
CCL             1.11        O
SBCL            1.3.13      O
LispWorks       6.1         O     Tested with the Personal Edition
```

Usage
=====

1. Set up Quicklisp properly.
2. Clone this repo.
3. Install the Vim plugin from Vlime's `vim` directory using your usual way
   (Vundle or something).
4. Run the server: `sbcl --load <vlime repo>/lisp/start-vlime.lisp`

If it's your first time running the server, Vlime will try to install it's
dependencies via Quicklisp.

When the server is up and running, use Vim to start editing a CL source file.
These keys are mapped (in normal mode unless specified otherwise):

Connection management:

* `<LocalLeader>cc`: Connect to Vlime server.
* `<LocalLeader>cs`: Switch Vlime connections.
* `<LocalLeader>cd`: Disconnect.
* `<LocalLeader>cr`: Rename the current connection.

Sending stuff to the REPL:

* `<LocalLeader>ss`: Send the "thing" (an s-expression or an atom) under the cursor to the REPL.
* `<LocalLeader>se`: Send the s-expression under the cursor to the REPL.
* `<LocalLeader>sa`: Send the atom under the cursor to the REPL.
* `<LocalLeader>s`: (In visual mode) Send the current selection to the REPL.

Expanding macros:

* `<LocalLeader>m1`: Expand the macro under the cursor.
* `<LocalLeader>ma`: Expand the macro under the cursor and all nested macros.

Compiling:

* `<LocalLeader>oe`: Compile the form under the cursor.
* `<LocalLeader>of`: Compile the current file.
* `<LocalLeader>o`: (In visual mode) Compile the current selection.

Cross references (xref):

* `<LocalLeader>xc`: Show callers of the function under the cursor.
* `<LocalLeader>xC`: Show callees of the function under the cursor.
* `<LocalLeader>xr`: Show references of the variable under the cursor.
* `<LocalLeader>xb`: Show bindings of the variable under the cursor.
* `<LocalLeader>xs`: Show who sets the value of the variable under the cursor.
* `<LocalLeader>xe`: Show who expands the macro under the cursor.
* `<LocalLeader>xm`: Show specialized methods for the class under the cursor.

Describing things:

* `<LocalLeader>do`: Describe the "operator" of the s-expression under the cursor.
* `<LocalLeader>da`: Describe the atom under the cursor.
* `<LocalLeader>da`: Apropos search.

Inspection:

* `<LocalLeader>II`: Evaluate and inspect the "thing" (an s-expression or an atom) under the cursor.
* `<LocalLeader>Ii`: Same as `<LocalLeader>II`
* `<LocalLeader>IE`: Evaluate and inspect the s-expression under the cursor.
* `<LocalLeader>Ie`: Same as `<LocalLeader>IE`
* `<LocalLeader>IA`: Evaluate and inspect the atom under the cursor.
* `<LocalLeader>Ia`: Same as `<LocalLeader>IA`
* `<LocalLeader>I` : (In visual mode) Evaluate and inspect the current selection.

Other stuff:

* `<LocalLeader>i`: Interaction Mode.
* `<LocalLeader>l`: Load the current file.
* `<LocalLeader>a`: Disassemble the form under the cursor.
* `<LocalLeader>p`: Specify the package for the current buffer.
* `<LocalLeader>b`: Set a breakpoint at entry to a function.

`<LocalLeader>` defaults to backslash `\`. In addition, you can use `<tab>`
instead of `<c-x><c-o>` to invoke omni-completion.

Most of Vlime's functionalities need an active connection to the server. Press
`<LocalLeader>cc` to create one. And Vlime supports multiple connections, use
`<LocalLeader>cs` to select the connection to use for the current buffer.

In `Interaction Mode` (entered via `<LocalLeader>i`), you can place the cursor
on an s-expression and then press `<cr>` to send it to the REPL.

In the REPL output buffer, `<c-c>` will interrupt the REPL thread.

When there's an unhandled condition or a thread is interrupted, the SLDB
buffer will appear, with posible restarts and stack frames as it's content.
Pressing `<cr>` on one of the restart options will invoke that restart. These
keys are available in the SLDB buffer:

* `<cr>`: Choose a restart.
* `d`: Show the details (local variables and source location etc.) of the
       frame under the cursor (or the most recent frame).
* `S`: Jump to the source code for the frame under the cursor (or the most recent frame).
* `r`: Restart the frame under the cursor.
* `s`: Start stepping in the frame under the cursor (or the most recent frame).
* `x`: Step over the current function call.
* `o`: Step out of the current function.
* `c`: Invoke the restart labeled CONTINUE.
* `a`: Invoke the restart labeled ABORT.
* `C`: Inspect the current condition object.
* `i`: Evaluate and inspect an expression in the frame under the cursor (or
       the most recent frame).
* `e`: Evaluate an expression in the frame under the cursor (or the most
       recent frame).
* `D`: Disassemble the frame under the cursor (or the most recent frame).
* `R`: Return a manually specified result from the frame under the cursor.

In the inspector buffer, one can use `<cr>` and `<space>` to interact with
buttons and values etc.

In the xref buffer, use `<cr>` to jump to the selected reference spot.

License
=======

MIT. See `LICENSE.txt`.
