# The Algorithm X Matrix is Binary

Let's revisit the challenge of constructing a word search. Given a list of words and grid of a certain size, what are the requirements?

1. Each word must be placed on the grid.

2. Each location may or may not be used, but if it is used more than once, it must always be colored with the same letter.

The first set of requirements are easy, and they fit perfectly into the paradigms discussed earlier in this playground. The second set of requirements are troublesome. There is no way to know how many words will intersect at a single location on the grid and some sort of checking must be done to ensure grid locations are only colored a single way. Using the perspective of words being placed on the grid, it makes sense to say:

* Each grid location may be covered by 0 to 4 words.

* Any location covered by 2 to 4 words must be covered (“colored”) with the same letter by each word.

These non-binary stipulations do not fit into an Algorithm X matrix where everything is binary.  The solution is to customize `AlgorithmXSolver`to monitor these non-binary requirements outside the Algorithm X matrix.

# Requirements

First, put the binary requirements into the Algorithm X matrix. For our word search construction exercise:

```python
requirements = [('word placed', word) for word in word_list]
```

For the non-binary requirements that can be colored, add an attribute to your solver subclass to keep track of the color assignments. There are many ways you could do this. For this example, I will use a `dictionary` where the grid locations are the `key`s and each `value` is a `list`. Every time a location is covered by a word, the letter that colors the location is added to the list. A cell that has an empty list may be covered by any color (letter). A cell that has a non-empty list can only be properly covered again if the new color matches what is already in the list.

```python
self.location_colors = {(r, c):[] for r in range(height) for c in range(width)}
```

Finally, logic must be added to make sure coloring requirements are satisfied.

# Adding Coloring Logic To Your Solver

Each time a row is selected, logic must be added to check the coloring of each grid cell covered by the word being placed on the grid. This is accomplished by overriding the `AlgorithmXSolver` `_process_row_selection()` method to do the following:

1. Update the coloring of any covered grid location.

1. Redirect Algorithm X if any color violations have occurred.

```
    def _process_row_selection(self, row):
        word and location = unpack the row tuple
        for each letter in word:
            if self.location_colors[grid location] not empty and letter is inappropriate:
                self.solution_is_valid = False

            self.location_colors[grid_location].append(letter)
```

The last line above might be a little confusing. Why is the letter getting added to the list even if the coloring was inappropriate and `self.solution_is_valid` was set to `False`? This must be done this way because backtracking can happen naturally or it can be forced because the current path is not valid. Either way, the backtracking is going to "undo" the most recent row processing which means popping the most recent addition out of the `location_colors` `list`. This cleanup is accomplished by overriding the `AlgorithmXSolver` `_process_row_deselection()` method as follows:

```
    def _process_row_deselection(self, row):
        word and location = unpack the row tuple
        for each letter in word:
            remove the most recent addition to self.location_colors[grid location]
```

# Tremendous Power From Minimal Effort

It is much more difficult to find research done on Algorithm C or the coloring of requirements than it is to find material on Algorithm X. One way or another, modifications must be made to Algorithm X if you wish to handle requirements that can be colored. The technique laid out above requires a minimal amount of customization to your solver subclass and provides tremendous power. In the next section, I will identify a couple of puzzles where you might benefit from this newfound power!