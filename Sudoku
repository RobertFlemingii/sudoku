NAME = ""
COLLABORATORS = ""

# try:
#     from nose.tools import assert_equal, assert_almost_equal
#     from nose.tools import assert_true, assert_false
#     from nose.tools import assert_not_equal, assert_greater_equal
# except:
#     from nose.tools import assert_equal, assert_almost_equal
#     from nose.tools import assert_true, assert_false
#     from nose.tools import assert_not_equal, assert_greater_equal

def getel(s):
    """Returns the unique element in a singleton set (or list)."""
    assert len(s) == 1
    return list(s)[0]

import json

class Sudoku:
    
    def __init__(self, elements):
        """The `elements` argument can be one of:
        Case 1: an instance of Sudoku.  In that case, we initialize an 
        object to be equal to (a copy of) the provided instance.
        Case 2: a list of 9 strings of length 9 each.
        Each string represents a row of the initial Sudoku puzzle,
        with either a digit 1..9 in it, or with a blank or _ to signify
        a blank cell.
        Case 3: a list of lists of sets, used to initialize the problem."""

        # Case 1
        if isinstance(elements, Sudoku):
            # We let self.m consist of copies of each set in elements.m
            self.m = [[x.copy() for x in row] for row in elements.m]

        # Cases 2 and 3
        else:
            assert len(elements) == 9
            for s in elements:
                assert len(s) == 9
            # We let self.m be our Sudoku problem, a 9x9 matrix of sets. 
            self.m = []
            for s in elements:
                row = []
                for c in s:
                    # Case 2
                    if isinstance(c, str):
                        if c.isdigit():
                            row.append({int(c)})
                        else:
                            row.append({1, 2, 3, 4, 5, 6, 7, 8, 9})
                    # Case 3
                    else:
                        assert isinstance(c, set)
                        row.append(c)
                self.m.append(row)               
            
    def show(self, details=False):
        """Prints out the Sudoku matrix.  If details=False, we print out
        the digits only for cells that have singleton sets (where only
        one digit can fit).  If details=True, for each cell, we display the 
        sets associated with the cell."""
        if details:
            print("+-----------------------------+-----------------------------+-----------------------------+")
            for i in range(9):
                r = '|'
                for j in range(9):
                    # We represent the set {2, 3, 5} via _23_5____
                    s = ''
                    for k in range(1, 10):
                        s += str(k) if k in self.m[i][j] else '_'
                    r += s
                    r += '|' if (j + 1) % 3 == 0 else ' '                        
                print(r)
                if (i + 1) % 3 == 0:
                    print("+-----------------------------+-----------------------------+-----------------------------+")
        else:
            print("+---+---+---+")
            for i in range(9):
                r = '|'
                for j in range(9):
                    if len(self.m[i][j]) == 1:
                        r += str(getel(self.m[i][j]))
                    else:
                        r += "."
                    if (j + 1) % 3 == 0:
                        r += "|"
                print(r)
                if (i + 1) % 3 == 0:
                    print("+---+---+---+")
                    
                    
    def to_string(self):
        """This method is useful for producing a representation that 
        can be used in testing."""
        as_lists = [[list(self.m[i][j]) for j in range(9)] for i in range(9)]
        return json.dumps(as_lists)
    
    @staticmethod
    def from_string(s):
        """Inverse of above."""
        as_lists = json.loads(s)
        as_sets = [[set(el) for el in row] for row in as_lists]
        return Sudoku(as_sets)  
    
    def __eq__(self, other):
        """Useful for testing."""
        return self.m == other.m

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
sd.show()
sd.show(details=True)

s = sd.to_string()
sdd = Sudoku.from_string(s)
sdd.show(details=True)
# assert_equal(sd, sdd)

class Unsolvable(Exception):
    pass

def sudoku_ruleout(self, i, j, x):
    """Takes as input a cell (i, j), and a value x.
    Removes x from the set of possibilities for that cell, if present, and:
    - if the resulting set is empty, raises Unsolvable;
    - if the cell used to be a non-singleton cell and is now a singleton 
      cell, then returns the set {(i, j)};
    - otherwise, returns the empty set."""
    c = self.m[i][j]
    n = len(c)
    c.discard(x)
    self.m[i][j] = c
    if len(c) == 0:
        raise Unsolvable()
    return {(i, j)} if 1 == len(c) < n else set()    

Sudoku.ruleout = sudoku_ruleout

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
sd.show(details=True)

def sudoku_propagate_cell(self, ij):
    """Takes a two-element tuple `ij` of cell indices.
    Propagates the singleton value at cell (i, j), returning the set 
    of newly-singleton cells."""
    i, j = ij
    if len(self.m[i][j]) > 1:
        # Nothing to propagate from cell (i,j).
        return set()
    # We keep track of the newly-singleton cells.
    newly_singleton = set()
    x = getel(self.m[i][j]) # Value at (i, j). 

    # Same row.
    for jj in range(9):
        if jj != j: # Do not propagate to the element itself.
            newly_singleton.update(self.ruleout(i, jj, x))
            
    # Same column.
    for ii in range(9):
        if ii != i:
            newly_singleton.update(self.ruleout(ii, j, x))
    
    # Same 3x3 block of cells.
    if i <= 2 and j <= 2:
        for ii in range(3):
            for jj in range(3):
                if ii != i or jj != j:
                    newly_singleton.update(self.ruleout(ii, jj, x))
    elif i <= 5 and j <= 2:
        for ii in range(3, 6):
            for jj in range(3):
                if ii != i or jj != j:
                    newly_singleton.update(self.ruleout(ii, jj, x))
    elif i <= 8 and j <= 2:
        for ii in range(6, 9):
            for jj in range(3):
                if ii != i or jj != j:
                    newly_singleton.update(self.ruleout(ii, jj, x))
    elif i <= 2 and j <= 5:
        for ii in range(3):
            for jj in range(3, 6):
                if ii != i or jj != j:
                    newly_singleton.update(self.ruleout(ii, jj, x))
    elif i <= 5 and j <= 5:
        for ii in range(3, 6):
            for jj in range(3, 6):
                if ii != i or jj != j:
                    newly_singleton.update(self.ruleout(ii, jj, x))
    elif i <= 8 and j <= 5:
        for ii in range(6, 9):
            for jj in range(3, 6):
                if ii != i or jj != j:
                    newly_singleton.update(self.ruleout(ii, jj, x))
    elif i <= 2 and j <= 8:
        for ii in range(3):
            for jj in range(6, 9):
                if ii != i or jj != j:
                    newly_singleton.update(self.ruleout(ii, jj, x))
    elif i <= 5 and j <= 8:
        for ii in range(3, 6):
            for jj in range(6, 9):
                if ii != i or jj != j:
                    newly_singleton.update(self.ruleout(ii, jj, x))
    elif i <= 8 and j <= 8:
        for ii in range(6, 9):
            for jj in range(6, 9):
                if ii != i or jj != j:
                    newly_singleton.update(self.ruleout(ii, jj, x))
    else:
        print("Yeah, I dunno what happened.")


    # Returns the list of newly-singleton cells.
    return newly_singleton

Sudoku.propagate_cell = sudoku_propagate_cell

### Propagating to the same column

tsd = Sudoku([
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

# Ensure that the value 5 in cell (0, 0) has been ruled out for other cells in the same column
tsd.propagate_cell((0,0))
# assert_equal(
#     [tsd.m[i][0] for i in range(9)],
#     [{5},
#      {6},
#      {1, 2, 3, 4, 6, 7, 8, 9},
#      {8},
#      {4},
#      {7},
#      {1, 2, 3, 4, 6, 7, 8, 9},
#      {1, 2, 3, 4, 6, 7, 8, 9},
#      {1, 2, 3, 4, 6, 7, 8, 9}])

tsd = Sudoku([
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

# Ensure that the value 3 in cell (4, 5) has been ruled out for other cells in the same column
tsd.propagate_cell((4,5))
# assert_equal(
#     [tsd.m[i][5] for i in range(9)],
#     [{1, 2, 4, 5, 6, 7, 8, 9},
#      {5},
#      {1, 2, 4, 5, 6, 7, 8, 9},
#      {1, 2, 4, 5, 6, 7, 8, 9},
#      {3},
#      {1, 2, 4, 5, 6, 7, 8, 9},
#      {1, 2, 4, 5, 6, 7, 8, 9},
#      {9},
#      {1, 2, 4, 5, 6, 7, 8, 9}])

tsd = Sudoku([
    '6____894_',
    '9____61__',
    '_7__4____',
    '2__61____',
    '______2__',
    '_89__2___',
    '____6___5',
    '_______3_',
    '8____16__'
])

# Ensure that the value 3 in cell (7, 7) has been ruled out for other cells in the same column
tsd.propagate_cell((7, 7))
# assert_equal(
#     [tsd.m[i][7] for i in range(9)],
#     [{4},
#      {1, 2, 4, 5, 6, 7, 8, 9},
#      {1, 2, 4, 5, 6, 7, 8, 9},
#      {1, 2, 4, 5, 6, 7, 8, 9},
#      {1, 2, 4, 5, 6, 7, 8, 9},
#      {1, 2, 4, 5, 6, 7, 8, 9},
#      {1, 2, 4, 5, 6, 7, 8, 9},
#      {3},
#      {1, 2, 4, 5, 6, 7, 8, 9}])

### Propagating to the same 3x3 block

tsd = Sudoku([
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

# Ensure that the value 5 in cell (0, 0) has been ruled out for other cells in the same 3x3 block
tsd.propagate_cell((0,0))
# assert_equal(
#     [tsd.m[i][j] for i in range(3) for j in range(3)],
#     [{5},                          {3},                      {1, 2, 3, 4, 6, 7, 8, 9},
#      {6},                          {1, 2, 3, 4, 6, 7, 8, 9}, {1, 2, 3, 4, 6, 7, 8, 9},
#      {1, 2, 3, 4, 6, 7, 8, 9},     {9},                      {8}])

tsd = Sudoku([
    '6____894_',
    '9____61__',
    '_7__4____',
    '2__61____',
    '______2__',
    '_89__2___',
    '____6___5',
    '_______3_',
    '8____16__'
])

# Ensure that the value 9 in cell (0, 6) has been ruled out for other cells in the same 3x3 block
tsd.propagate_cell((0, 6))
# assert_equal(
#     [tsd.m[i][j] for i in range(3) for j in range(6, 9)],
#     [{9},                          {4},                          {1, 2, 3, 4, 5, 6, 7, 8},
#      {1},                          {1, 2, 3, 4, 5, 6, 7, 8},     {1, 2, 3, 4, 5, 6, 7, 8},
#      {1, 2, 3, 4, 5, 6, 7, 8},     {1, 2, 3, 4, 5, 6, 7, 8},     {1, 2, 3, 4, 5, 6, 7, 8}])

tsd = Sudoku([
    '6____894_',
    '9____61__',
    '_7__4____',
    '2__61____',
    '______2__',
    '_89__2___',
    '____6___5',
    '_______3_',
    '8____16__'
])

# Ensure that the value 3 in cell (7, 7) has been ruled out for other cells in the same 3x3 block
tsd.propagate_cell((7, 7))
# assert_equal(
#     [tsd.m[i][j] for i in range(6, 9) for j in range(6, 9)],
#     [{1, 2, 4, 5, 6, 7, 8, 9},     {1, 2, 4, 5, 6, 7, 8, 9},     {5},
#      {1, 2, 4, 5, 6, 7, 8, 9},     {3},                          {1, 2, 4, 5, 6, 7, 8, 9},
#      {6},                          {1, 2, 4, 5, 6, 7, 8, 9},     {1, 2, 4, 5, 6, 7, 8, 9}])

### More tests for cell propagation

tsd = Sudoku.from_string('[[[5], [3], [2], [6], [7], [8], [9], [1, 2, 4], [2]], [[6], [7], [1, 2, 4, 7], [1, 2, 3], [9], [5], [3], [1, 2, 4], [8]], [[1, 2], [9], [8], [3], [4], [1, 2], [5], [6], [7]], [[8], [5], [9], [1, 9, 7], [6], [1, 4, 9, 7], [4], [2], [3]], [[4], [2], [6], [8], [5], [3], [7], [9], [1]], [[7], [1], [3], [9], [2], [4], [8], [5], [6]], [[1, 9], [6], [1, 5, 9, 7], [9, 5, 7], [3], [9, 7], [2], [8], [4]], [[9, 2], [8], [9, 2, 7], [4], [1], [9, 2, 7], [6], [3], [5]], [[3], [4], [2, 3, 4, 5], [2, 5, 6], [8], [6], [1], [7], [9]]]')
tsd.show(details=True)
try:
    tsd.propagate_cell((0, 2))
except Unsolvable:
    print("Good! It was unsolvable.")
else:
    raise Exception("Hey, it was unsolvable")
    
tsd = Sudoku.from_string('[[[5], [3], [2], [6], [7], [8], [9], [1, 2, 4], [2, 3]], [[6], [7], [1, 2, 4, 7], [1, 2, 3], [9], [5], [3], [1, 2, 4], [8]], [[1, 2], [9], [8], [3], [4], [1, 2], [5], [6], [7]], [[8], [5], [9], [1, 9, 7], [6], [1, 4, 9, 7], [4], [2], [3]], [[4], [2], [6], [8], [5], [3], [7], [9], [1]], [[7], [1], [3], [9], [2], [4], [8], [5], [6]], [[1, 9], [6], [1, 5, 9, 7], [9, 5, 7], [3], [9, 7], [2], [8], [4]], [[9, 2], [8], [9, 2, 7], [4], [1], [9, 2, 7], [6], [3], [5]], [[3], [4], [2, 3, 4, 5], [2, 5, 6], [8], [6], [1], [7], [9]]]')
tsd.show(details=True)
# assert_equal(tsd.propagate_cell((0, 2)), {(0, 8), (2, 0)})

def sudoku_propagate_all_cells_once(self):
    """This function propagates the constraints from all singletons."""
    for i in range(9):
        for j in range(9):
            self.propagate_cell((i, j))
            
Sudoku.propagate_all_cells_once = sudoku_propagate_all_cells_once

tsd = Sudoku([
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
sd.propagate_all_cells_once()
sd.show(details=True)

def sudoku_full_propagation(self, to_propagate=None):
    """Iteratively propagates from all singleton cells, and from all 
    newly discovered singleton cells, until no more propagation is possible."""
    while True:
        if to_propagate is None:
            to_propagate = {(i, j) for i in range(9) for j in range(9)}
        
        if len(to_propagate) == 0:
            break
        else:
            self.full_propagation(to_propagate)

Sudoku.full_propagation = sudoku_full_propagation

### Tests for `full_propagation`

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
## sd.full_propagation()
## sd.show()

# Spot checks for a few cells
# assert_equal(sd.m[0][2], {4})
# assert_equal(sd.m[1][1], {7})
# assert_equal(sd.m[8][1], {4})
# assert_equal(sd.m[4][7], {9})

### More tests for `full_propagation`

# Full propagation is enough to solve this puzzle...
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
## sd.full_propagation()
## sd.show()
sdd = Sudoku.from_string('[[[5], [3], [4], [6], [7], [8], [9], [1], [2]], [[6], [7], [2], [1], [9], [5], [3], [4], [8]], [[1], [9], [8], [3], [4], [2], [5], [6], [7]], [[8], [5], [9], [7], [6], [1], [4], [2], [3]], [[4], [2], [6], [8], [5], [3], [7], [9], [1]], [[7], [1], [3], [9], [2], [4], [8], [5], [6]], [[9], [6], [1], [5], [3], [7], [2], [8], [4]], [[2], [8], [7], [4], [1], [9], [6], [3], [5]], [[3], [4], [5], [2], [8], [6], [1], [7], [9]]]')
# assert_equal(sd, sdd)

# ...but not this one 
sd = Sudoku([
    '8________',
    '__36_____',
    '_7__9_2__',
    '_5___7___',
    '____457__',
    '___1___3_',
    '__1____68',
    '__85___1_',
    '_9____4__'
])
## sd.full_propagation()
## sd.show()
sdd = Sudoku.from_string('[[[8], [1, 2, 4, 6], [2, 4, 5, 6, 9], [2, 3, 4, 7], [1, 2, 3, 5, 7], [1, 2, 3, 4], [1, 3, 5, 6, 9], [4, 5, 7, 9], [1, 3, 4, 5, 6, 7, 9]], [[1, 2, 4, 5, 9], [1, 2, 4], [3], [6], [1, 2, 5, 7, 8], [1, 2, 4, 8], [1, 5, 8, 9], [4, 5, 7, 8, 9], [1, 4, 5, 7, 9]], [[1, 4, 5, 6], [7], [4, 5, 6], [3, 4, 8], [9], [1, 3, 4, 8], [2], [4, 5, 8], [1, 3, 4, 5, 6]], [[1, 2, 3, 4, 6, 9], [5], [2, 4, 6, 9], [2, 3, 8, 9], [2, 3, 6, 8], [7], [1, 6, 8, 9], [2, 4, 8, 9], [1, 2, 4, 6, 9]], [[1, 2, 3, 6, 9], [1, 2, 3, 6, 8], [2, 6, 9], [2, 3, 8, 9], [4], [5], [7], [2, 8, 9], [1, 2, 6, 9]], [[2, 4, 6, 7, 9], [2, 4, 6, 8], [2, 4, 6, 7, 9], [1], [2, 6, 8], [2, 6, 8, 9], [5, 6, 8, 9], [3], [2, 4, 5, 6, 9]], [[2, 3, 4, 5, 7], [2, 3, 4], [1], [2, 3, 4, 7, 9], [2, 3, 7], [2, 3, 4, 9], [3, 5, 9], [6], [8]], [[2, 3, 4, 6, 7], [2, 3, 4, 6], [8], [5], [2, 3, 6, 7], [2, 3, 4, 6, 9], [3, 9], [1], [2, 3, 7, 9]], [[2, 3, 5, 6, 7], [9], [2, 5, 6, 7], [2, 3, 7, 8], [1, 2, 3, 6, 7, 8], [1, 2, 3, 6, 8], [4], [2, 5, 7], [2, 3, 5, 7]]]')
# assert_equal(sd, sdd)

