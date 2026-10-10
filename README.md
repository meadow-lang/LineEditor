# LineEditor

Reading an entry from someone typing it, for
[Meadow](https://github.com/meadow-lang/meadow): the prompt of a REPL. The
cursor moves about in what has been typed, an entry can be several lines,
the entries before it come back, and the program reading it says how the
text is coloured, what completes it, whether it is finished and how far in
its next line starts.

```meadow
use LineEditor (editor, readEntry, loadHistory, saveEntry, Outcome)

fun repl (history : [String]) =
  let e = { editor | prompt = "> ", continued = "  ", finished = balanced, indent = indentOf } in
  match readEntry e history with
  | Outcome.Entered text ->
      let _ = saveEntry ".history" text in
      let _ = println (eval text) in
      repl (V.pushBack history text)
  | Outcome.Interrupted -> repl history
  | Outcome.Ended -> ()

fun main () = repl (loadHistory ".history")
```

An `Editor` is a record of what the program has to say about the text:

| field | |
| --- | --- |
| `prompt`, `continued` | what the first line starts with, and each line after it |
| `highlight` | the text as it is to be shown: the same characters, with styles among them |
| `complete` | the text and the cursor -> where what is being completed starts, and what it could be |
| `finished` | whether Enter enters the text, or starts a new line of it |
| `indent` | what a new line after the text so far opens with |
| `bindings` | keys of the program's own: a function of the text and the cursor that may do anything, and answers `Kept`, `Edited text cursor` or `Entering` |

`editor` is one that asks nothing of the text; start from it.

Any of these functions may do more than answer -- ask a compiler's database
what completes a name, say: an `Editor e`'s functions perform `e`, and reading
an entry with it performs that too.

**Keys.** Left, Right, Home, End, Ctrl-A, Ctrl-E, Ctrl-B; Ctrl-Left and
Ctrl-Right, or Alt-B and Alt-F, by words. Backspace, Delete; Ctrl-W the word
before, Ctrl-K the rest of the line, Ctrl-U its start. Up and Down move
between the lines of an entry, and past its first or last through the
history, as do Ctrl-P and Ctrl-N. Tab completes as far as every candidate
agrees. Enter enters a finished text and otherwise starts a new line,
indented; Alt-Enter and Ctrl-J start one whatever the text. Ctrl-C gives the
entry up; Ctrl-D on an empty one ends. Pasted text goes in as it is.

Without a terminal -- input from a file or a pipe -- lines are read as they
come, as many as `finished` asks for, and nothing is drawn.

The editor is a state and a function of a key, `press`, so it is tested by
giving it keys; `readEntry` is the loop that gives it a terminal
(`Std.Terminal`). `drawn` is what it writes for a state.

## AI disclosure

LineEditor is written with AI coding agents: Anthropic's Claude, through Claude
Code. Most of the code, the tests, the documentation and the commit messages in
this repository were written by an agent, under the direction of the project's
author, who decides the design and what goes in. Read it, and rely on it, with
that in mind.

## Install

```sh
meadow add meadow-lang/LineEditor
```

## Licence

BSD 3-Clause: see [LICENSE](LICENSE).
