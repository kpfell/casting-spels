## Walking Around In Our World

Ok, now that we can see our world, let's write some code that lets us walk around in it. Let's start with a helper function that lists the valid directions in which our player can move:

```lisp
(defun get-valid-moves (exits)
  (lists:map
    (lambda (x)
      (list_to_atom (exit-direction x)))
    exits))
```

Since we're in the living room right now, out two valid moves should be those that take us to the garden or to the attic:

```lisp
> (get-valid-moves state)
(garden attic)
```

[add explanation]

```lisp
(defun get-new-location (player-dir exits)
  (exit-destination
    (car
      (lists:filter
        (match-lambda
          (((match-exit direction dir destination dest))
            (if (== dir (atom_to_list player-dir))
                'true
                'false)))
        exits))))
```

[add demonstration]

Excellent!

Let's create some more helper functions: whever the player's move is good or bad, two things need to happen, and in this order:

1. A message needs to be displayed, and
1. The game state needs to be returned.

Let's make those functions now:

```lisp
(defun good-move (game-state)
  (look game-state)
  game-state)

(defun bad-move (game-state)
  (io:format "You can't go that way.~n")
  game-state)
```

With these in place, we're ready to create or first action:

```lisp
(defun walk-direction (direction game-state)
  (let ((exits (place-exits (get-here game-state))))
    (case (lists:member direction (get-valid-moves exits))
          ('true (good-move
                   (set-state-player
                     game-state
                     (get-new-location direction exits))))
          ('false (bad-move game-state)))))
```

What are you waiting for?! Let's try it!

```lisp
> (set state (walk-direction 'west state))
```
```lisp
You are in a beautiful garden. There is a well in front of you.
There is a door going east from here.
You see a frog on the floor. You see a chain on the floor.
```

You will also see the new state displayed in the REPL. We'll talk more about that later.


Now, we were able to simplify our description functions by creating a look command that is easy for our player to type. Similarly, it would be nice to adjust the walk-direction command so that it doesn't have an annoying quote mark in the command that the player has to type in. But, as we have learned, when the compiler reads a form in Code Mode, it will read all its parameters in Code Mode, unless a quote tells it not to. Is there anything we can do to tell the compiler that west is just a piece of data without the quote?
