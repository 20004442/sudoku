# Sudoku

## Aim:
To develop a code to solve a sudoku puzzle using contraint propagation

## Theory:
Every 9x9 sudoku board is divided into nine 3x3 sections.

The rows will be labeled by the letters A, B, C, D, E, F, G, H, I. The columns will be labeled by the numbers 1, 2, 3, 4, 5, 6, 7, 8, 9. Note that the labeling convention is just for our understanding and doesn’t actually exists on the board.

The individual squares at the intersection of rows and columns will be called boxes. These boxes will have labels 'A1', 'A2', …, 'I9'. We will use these labels to address each box on the board uniquely while coding

Each row, column, and 3x3 square will be called a unit. Thus, each unit is a set of 9 boxes, and there are 27 units in total.

For a particular box (such as ‘E7’), its peers will be all other boxes that belong to a common unit (namely, those that belong to the same row, column, or 3x3 square).

Fill all of the empty boxes on the board with a digit such that none of the boxes in the same row, same column, or same 3x3 square has that same digit.



## Solving Puzzle:
![sudoku](https://user-images.githubusercontent.com/75235813/173862958-5d19ee4e-5b1a-42f2-affb-775151bc3eb1.png)



## Design Steps:
### step 1: 
The starting values of the unsolved board will be represented by a string of digits with a dot(.) to represent if the box is empty.
### step 2:
The board itself will be represented in the form of a dictionary with the keys to be the labels of each the box and the values to be the digit in the box.
### step 3:
create a function that takes the unsolved sudoku board and returns the same board but after applying elimination ‘once’ to all of the boxes.
### Step 4:
Check whether any box which allows only a certain digit in the unit after elimination.
### Step 5:
Repeat 3 and 4 until we get the solved puzzle.
## Program:
```
%matplotlib inline
import matplotlib.pyplot as plt
import random
import math
import sys
rows = 'ABCDEFGHI'
cols = '123456789'

def cross(a,b):
    return [s+t for s in a for t in b]
boxes= cross(rows, cols)
row_units = [cross(r,cols) for r in rows]
column_units = [cross(rows,c) for c in cols]
square_units = [cross(rs,cs) for rs in ('ABC','DEF','GHI') for cs in ('123','456','789')]

unitlist = row_units + column_units + square_units
units = dict((s,[u for u in unitlist if s in u]) for s in boxes)
peers = dict((s,set(sum(units[s],[]))-set([s])) for s in boxes)
puzzle = '53..7....6..195....98....6.8...6...34..8.3..17...2...6.6....28....419..5....8..79'
def display(values):
    width=1+max(len(values[s])for s in boxes)
    line = '+'.join(['-'*(width*3)]*3)
    for r in rows:
        print(''.join(values[r+c].center(width)+('|'if c in '36' else '')for c in cols))
        if r in 'CF':print(line)
    return
def elimination(values):
    solved_values = [box for box in values.keys() if len(values[box])==1]
    for box in solved_values:
        digit = values[box]
        for peer in peers[box]:
            values[peer] = values[peer].replace(digit,'')
    return values
def only_choice(values):
    for unit in unitlist:
        for digit in '123456789':
            dplaces = [box for box in unit if digit in values[box]]
            if len(dplaces) == 1:
                values[dplaces[0]] = digit
    return values
def grid_values(grid):
    assert len(grid) == 81, "Input grid must be a string length of 81 (9x9)"
    boxes = cross(rows,cols)
    return dict(zip(boxes,grid))

def grid_values_improved(grid):
    values = []
    all_digits = '123456789'
    for c in grid:
        if c == '.':
            values.append(all_digits)
        elif c in all_digits:
                values.append(c)
    assert len(values) == 81
    boxes = cross(rows,cols)
    return dict(zip(boxes,values))
def reduce_puzzle(values):
    stalled =False
    while not stalled:
        solved_values_before = len([box for box in values.keys() if len(values[box])==1])
        elimination(values)
        only_choice(values)
        solved_values_after = len([box for box in values.keys() if len(values[box])==1])
        stalled = solved_values_after == solved_values_before
        if len([box for box in values.keys() if len(values[box])==1])==0:
            return False
    return values
def search(values):
    values_reduced = reduce_puzzle(values)
    if not values_reduced:
        return False
    else:
        values=values_reduced
    if len([box for box in values.keys() if len(values[box])==1])==81:
        return values
    
    possibility_count_list = [(len(values[box]),box) for box in values.keys() if len(values[box])>1]
    
    possibility_count_list.sort()
    for(_,t_box_min) in possibility_count_list:
        for i_digit in values[t_box_min]:
            new_values = values.copy()
            new_values[t_box_min]=i_digit
            new_values = search(new_values)
            if new_values:
                return new_values
            
    return values
sudoku=grid_values_improved(puzzle)
result = search(sudoku)
display(result)
print(result)
```

## Output:
![image](https://user-images.githubusercontent.com/75235813/173863521-9c3096af-0e49-46db-94a6-8cab2af70a21.png)

![image](https://user-images.githubusercontent.com/75235813/173863585-e5baa802-f88e-4ed1-8e6a-387121b51854.png)



## Result:
Thus, a program to solve sudoku puzzle using constraint propagation is implemented successfully.
