Sudoku Solver
This is a Sudoku solver implemented in Python. It provides a Sudoku class that allows you to create Sudoku objects and solve Sudoku puzzles.

Usage
To use the Sudoku solver, follow these steps:

1. Import the Sudoku class from the module:

   from sudoku import Sudoku

2. Create a Sudoku object by passing the initial puzzle as a list of strings to the constructor. Each string represents a row of the puzzle, where digits 1-9 represent known values and blanks or underscores (\_) represent empty cells:

sd = Sudoku([
'53__7____',
'6__195___',
'_98____6_',
'8___6___3',
'4__8_3__1',
'7___2___6',
'_6____28_',
'___419__5',
'____8__79'
])

3. Display the Sudoku puzzle using the show() method. By default, it displays the digits only for cells with a single possible value. Pass details=True to display the sets of possible values for each cell:

   sd.show() # Display puzzle with single-digit values
   sd.show(details=True) # Display puzzle with sets of possible values

4. Solve the Sudoku puzzle using the provided methods. The solver uses a rule-based approach to eliminate possibilities and propagate constraints:
   ruleout(i, j, x): Takes as input a cell (i, j) and a value x. It removes x from the set of possibilities for that cell and updates the puzzle accordingly. If the resulting set is empty, it raises an Unsolvable exception. If the cell used to be a non-singleton cell and is now a singleton cell, it returns the set {(i, j)}. Otherwise, it returns the empty set.

   propagate_cell(ij): Takes a two-element tuple ij of cell indices. It propagates the singleton value at cell (i, j) to other cells in the same row, column, and 3x3 block. It returns the set of newly singleton cells.

Here's an example of how to use the solver:

# Create Sudoku object

sd = Sudoku([
'53__7____',
'6__195___',
'_98____6_',
'8___6___3',
'4__8_3__1',
'7___2___6',
'_6____28_',
'___419__5',
'____8__79'
])

# Solve the Sudoku puzzle

while True: # Keep propagating until no more changes occur
prev_puzzle = sd.to_string()
newly_singleton = set()
for i in range(9):
for j in range(9):
newly_singleton.update(sd.propagate_cell((i, j)))
if sd.to_string() == prev_puzzle:
break

# Display the solved puzzle

sd.show(details=True)

Limitations
Please note that this Sudoku solver uses a simple rule-based approach and may not be able to solve all Sudoku puzzles. It relies on propagating constraints and eliminating possibilities based on the given rules. In some cases, it may require additional advanced techniques or brute-force search to find a solution. If a puzzle is unsolvable or requires advanced techniques, an Unsolvable exception will be raised.

Feel free to use and modify this Sudoku solver according to your needs!

Contributors
Robert Fleming
