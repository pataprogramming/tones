_Twinkle, Twinkle, Little Star_ - Making Sounds with Overtone
=================================================

In the first part of the ClojureBridge curriculum, you've been
drawing pictures. Now, let's make some music!

[Overtone](http://overtone.github.io) provides a way to generate
sounds, create synthetic instruments, and compose music using
Clojure. It's pretty easy to get started, and there are lots of
sophisticated features to explore.

**Linux Users** If you are on Linux, you need to have the JACK daemon
running. Take a look at [Installing and starting
JACK](http://github.com/overtone/overtone/wiki/Installing-and-starting-jack),
and install the packages.


1. Prelude - getting started
----------------------------

### Download the project

ClojureBridge has a template project for getting started with
Overtone. The first thing to do is to *clone* that project
using `git` command.

Open the terminal and type the command:

```bash
git clone https://github.com/ClojureBridge/tones.git
```

> (Option) It's a good exercise to start from creating a Clojure
> project. [Starting from scratch](00-starting-from-scratch.md)
> explains how to do that. Try it later.

### Evaluate the file

After downloading the project with the `git` command, launch Light
Table and open the file `tones/src/tones/play.clj` Next, you can
evaluate `play.clj` by hitting <kbd>Cmd</kbd> + <kbd>Shift</kbd> +
<kbd>Enter</kbd> (or <kbd>Ctrl</kbd> + <kbd>Shift</kbd> +
<kbd>Enter</kbd>), which may take while to finish.

When it completes, you should hear some music!

> If you didn't hear anything, check the volume.
> You did turn up the volume, right?

Move the cursor right next to `(twinkle)` and evaluate this line by
hitting <kbd>Cmd</kbd> + <kbd>Enter</kbd> (or <kbd>Ctrl</kbd> +
<kbd>Enter</kbd>).  Again, you should hear some music: the first part
of _Twinkle, Twinkle, Little Star_, played by a piano.

> To do this in a repl, `cd` to the tones project root directory and
> run `lein repl`. Once the repl starts up, `(require 'tones.play)`,
> which loads and evaluates the file.  You should hear a melody once
> the file evaluation finishes.  To listen the melody again, change
> the namespace by typing `(ns tones.play)`, then `(twinkle)`.

### What to look at

The GitHub repository for Overtone, at
https://github.com/overtone/overtone, is a great source of examples
for how to make music with this library.  Here are two helpful
starting points for figuring out how to make it sound like a piano:

* https://github.com/overtone/overtone/blob/master/src/overtone/examples/getting_started/pragpub-article.clj
* https://github.com/overtone/overtone/blob/master/src/overtone/examples/workshops/resonate2013/ex01_phrasestudy.clj


2. Etude - playing piano notes
------------------------------

#### Basic `piano` function usage

The first Overtone function to try is `piano` with no arguments.
Add the function below to the file `play.clj` in Light Table:

```clojure
(piano)
```

When you evaluate this line you should hear a piano note.

### The `piano` function argument

The `piano` function takes a note number as an argument. For example:

```clojure
(piano 48)
```

The number `48` corresponds to a specific note, but it doesn't look
much like traditional music notation.  Fortunately, there's an easy
way to start with a more familiar note name and convert it the numbers
that this function understands: Overtone's `note` function.

`note` can be called like this:

```clojure
(note :c3)
```

Try various note names to see how Overtone handles them.  A chart
showing the mapping between numbers and notes can be found this chart:

![midi note](img/midi-int-midi-note-no-chart.jpg)

This picture from [Understanding Musical
Theory](http://wiki.spheredev.org/Understanding_musical_theory) shows
the the notes of a C scale in standard musical notation, along with
their names:

![notes](img/Theory-staff-cmajortreble.png)

The `:c3` represents a `C` note in octave number three. (Looking at a
piano keyboard, starting from the lowest note, it will be the third
`C` that you find.). You can combine the two functions to play a piano
note:

```clojure
(piano (note :c3))
```

> When you don't give the `piano` function a number argument, it uses
> `60` by default, which corresponds to `:c4`, otherwise known as
> "middle C".


### The `chord` function

A *chord* is the sound of two or more notes played at the same time, like this:

![chord](img/chords_c3.png)

Overtone's has a handy chord generator: the `chord` function. It
returns a set of numbers the corresond to all the notes that make up
the chord.  You can see how to use the function by moving the cursor
next to the function name (`chord`), right-clicking and selecting "show
docs".

> In the REPL, you can get the same information using `(doc chord)`


```clojure
overtone.live
([root chord-name] [root chord-name inversion])
Returns a set of notes for the specified chord. The root must be in
  midi note format i.e. :c4.

  (chord :c4 :major)  ; c major           -> #{60 64 67}
  (chord :a4 :minor)  ; a minor           -> #{57 60 64}
  (chord :Bb4 :dim)   ; b flat diminished -> #{70 73 76}
```

So if you try the function call

```clojure
(chord :c3 :major)
```

it should return
```clojure
(48 55 52)
```

You might expect that, since`(chord :c3 :major)` corresponds to `(48
55 52)`, Overtone will play a chord if you type `(piano 48) (piano 55)
(piano 52)` on a single line and evaluate it. In fact the sound will
just be a single note, as Light Table will think that you're just
asking it to evaluate the last of these: the `(piano 52)`

> At the REPL, writing `(piano 48) (piano 55) (piano 52)` in a single
> line plays all of the notes of the chord at once.

Knowing the number of the chord's notes, they can be combined together
using a function, as discussed earlier in the ClojureBridge curriculum:
For example, here's a `c3-major-chord` that can be added to `play.clj`:

```clojure
;; function definition
(defn c3-major-chord
  []
  (piano 48)
  (piano 55)
  (piano 52))
```

Place the cursor on the last line of the function and evaluate it. This
defines the function for later use, but doesn't yet play any of the notes.
To use the function, add another line to `play.clj` and evaluate it:

```clojure
;; usage
(c3-major-chord)
```


### A sequence of notes

This function is repetitive (every time you want to add a note, you
have to add a new call to `piano`) and not very flexible (it always
plays the same chord).

Since the chord function returns a sequence of notes, one way to make
things easier is to use Clojure's _sequence functions_ that were
discussed in the main ClojureBridge curriculum.  In this case, the
`doseq` function is a good fit for what we want to do: take the same
action for a sequence of things.

Since the `chord` function returns a sequence of numbers, there's no
need to retype them.  So, this ccommand has the same effect as the
`c3-major-chord` function:

```clojure
(doseq [note (chord :c3 :major)]  ;; Sequence
  (piano note))                   ;; Behavior
```

The `doseq` binds the value of the first note to the `note` symbol,
and then calls `(piano note)` to play it. Then it binds the value of
the second note to `note` and calls `(piano note)` again.  And then,
one more time, with the third note.

### Writing a function that takes arguments

The `c3-major-code` function can be rewritten by replacing
the repetition with the `doseq` command above:

```clojure
;; function definition
(defn c3-major-chord
  []
  (doseq [note (chord :c3 :major)]
    (piano note)))
```

This eliminates the repetition of `piano`, but it's still not very
general.  To address this, make a new variation of this function take
uses some _arguments_.

As the `chord` function takes two arguments, `root` and `chord-name`,
this new function should also take two arguments. To reflect what it
does, it also needs a better name, such as `piano-chord`.

```clojure
;; function definition
(defn piano-chord [root chord-name]
  (doseq [note (chord root chord-name)]
    (piano note)))
```

Now, you can give a chord description to `piano-chord`, and it will
play it:

```clojure
;; usage
(piano-chord :c3 :minor)
(piano-chord :f3 :minor)
(piano-chord :g2 :major)
(piano-chord :c3 :minor)
```


### [BONUS] change the function to take multiple sets of arguments

If you look at the documentation for the `chord` function, you may
notice that it can also take an optional third argument that
represents the _inversion_ of the chord. It would be nice if
`piano-chord` had the same flexibility.

> An inversion is a re-ordering of the notes in a chord. For example,
> the :c3 :major chord has the notes :c3, :e3, and :g3, which
> correspond to the numbers 48, 52, and 55.  To make the "first
> inversion" of the chord, you move the :c3 up an octave to :c4, so
> the notes are now :e3, :g3, and :c4, which correspond to the numbers
> 52, 55, and 60.

Clojure has a way to define a single function that can take different
numbers of arguments. (Such a function is said to have multiple
_arities_.)  Using this feature, expand `piano-chord`:

```clojure
(defn piano-chord
  ([root chord-name]
    (doseq [note (chord root chord-name)]
      (piano note)))
  ([root chord-name inv]
    (doseq [note (chord root chord-name inv)]
      (piano note))))
```

The definition of `piano-chord` has two function bodies (each wrapped
in a set of parentheses). The first element of each is a vector of the
parameters. Based on the number of arguments (2 or 3) either the first
or second body will be executed.

So, when evaluating

```clojure
(piano-chord :c3 :minor)
```

the first body entry will be used. When there are three arguments, as
with

```clojure
(piano-chord :c2 :dim 1)
```

the second body is called, resulting in a moody, first-inversion,
diminished C chord. Overtone supports many types of chords...try
evaluating these examples:

```clojure
(piano-chord :g3 :7sus4)
(piano-chord :g3 :dom7)
(piano-chord :c4 :sus4)
(piano-chord :c4 :minor)
```

### Make a melody

So far, you've been playing single notes and single chords.  How about
a melody?

The `twinkle` function in `play.clj` file is an example: it plays the
familiar tune of _Twinkle Twinkle Little Star_. The Overtone examples
give instructions for how to create a *progression* using the `at`
function.

The idea of a progression is that, by adding delays between successive
notes or chords, the music will unfold over time. (For example,
`note1` starting right `now`, `note2` playing one second later,
`note3` playing two seconds later, and so on. The `at`
function takes a time for its first argument, and we can get the
current time using `now`.

```clojure
(let [time (now)]
  (at         time  (piano-chord :d4 :minor7))
  (at (+ 2000 time) (piano-chord :g3 :major7))
  (at (+ 4000 time) (piano-chord :c3 :major7))
  (at (+ 6000 time) (piano-chord :e3 :minor7)))
```

The `let` binding was discussed in the Functions module of the
ClojureBridge main curriculum. In the above code, inside of the `let`,
`time` is used to hold the current time (at the moment when you
evaluate it).  The `now` function returns the value of the current
time in *milliseconds* (1 second = 1000 milliseconds).

The code above works like this:

1. play piano chord of :d4 :minor7 now
2. play piano chord of :g3 :major7 2 seconds from now
3. play piano chord of :c3 :major7 4 seconds from now
4. play piano chord of :e3 :minor7 6 seconds from now

Try changing the parameters to see what happens:

```clojure
(let [time (now)]
  (at time (piano-chord :c4 :minor))
  (at (+ 1500 time) (piano-chord :f3 :minor 2))
  (at (+ 3000 time) (piano-chord :g3 :major 1))
  (at (+ 4100 time) (piano (note :f4)))
  (at (+ 4500 time) (piano-chord :c3 :minor 2)))
```

It's starting to sound more like music!

### Complete Twinkle Twinkle Little Star

The `twinkle` function only plays the first part of _Twinkle Twinkle
Little Star_.  Here's the first part of this well-known lullaby.

![Twinkle Twinkle Little Star](img/TwinkleTwinkle_C_Image.jpg)

Since the function already has the melody up too "what you are, you
can complete the song by adding the notes for "Up above the world so
high", repeating it, and then repeating the first part agin..

Using the score, you can determine that the notes of this phrase are
`:g3 :g3 :f3 :f3 :e3 :e3 :d3`.  Now, combine the functions that
have been used previously to construct the melody and play it. Here's
a version of an `up-above` function:

```clojure
;; function definition
(defn up-above
  [start]
  (let [step 650
        notes [:g3 :g3 :f3 :f3 :e3 :e3 :d3]]
    (dotimes [i (count notes)]
      (at (+ start (* i step)) (piano (note (nth notes i)))))))
```

By giving the `up-above` function a `start` parameter, you can delay
the start until after the first part of the song.  The
[`dotimes`](http://clojuredocs.org/clojure.core/dotimes) function is
used in the version above to execute the `at` function multiple times.
The `nth` function was discussed in the Data Structures module in the
ClojureBridge main curriculum. Try varying the value of the `step`
binding to find a time interval between the notes that you like.

To test this function, give it a time to start playing:

```clojure
(up-above (now))
```

It sounded good. The last piece was to play all, `twinkle`,
`up-above`, `up-above`, then `twinkle` in order.

```clojure
(let [start (now)]
  (twinkle start)
  (up-above (+ start 10400))
  (up-above (+ start 15600))
  (twinkle (+ start 20800)))
```

When you evaluate this `let` form, you'll hear the entirety of
_Twinkle, Twinkle, Little Star_ played on a piano. It's a simple,
start but it can be heavily embellished (as Mozart did, when he wrote
his [variations on the
melody](https://www.youtube.com/watch?v=bOXdAa-G4bo).


4. Finale - epilogue
---------------------

This lesson covered a lot of ground including a bit about Overtone's
interface to SuperCollider music synthesis engine and how to use it.

It's really hard to imagine creating a song like this way.  Just as
with the repetition in `c3-major-chord`, the final version of the song
above required extra bookkeeping.  At its best, Clojure can be
satisfyingly *succinct*, and discovering how to keep improving code
you have written is a big part of the fun.

From this point, you're on your own.  Here are some links to launch
you on your next steps:

If you are interested in playing realistic sounds, take a look at
[this essay and
code](http://blog.josephwilk.net/clojure/creating-instruments-with-overtone.html)
for building up an ethereal-sounding flute solo.  If you want to know
making music without so much typing, check out the
[Leipzig Library](https://github.com/ctford/leipzig), which provides
lots of tools for building musical compositions..

Have fun, and make some noise!
