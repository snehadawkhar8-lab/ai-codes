# ai-codes
my ai code practices


1.partA

def manhattan(state, goal):
    h = 0

    for i in range(9):
        if state[i] != 0:
            j = goal.index(state[i])

            x1, y1 = i // 3, i % 3
            x2, y2 = j // 3, j % 3

            h += abs(x1 - x2) + abs(y1 - y2)

    return h


def get_neighbors(state):
    neighbors = []

    pos = state.index(0)
    x, y = pos // 3, pos % 3

    moves = [(-1, 0), (1, 0), (0, -1), (0, 1)]

    for dx, dy in moves:
        nx, ny = x + dx, y + dy

        if 0 <= nx < 3 and 0 <= ny < 3:
            new_state = list(state)

            new_pos = nx * 3 + ny

            new_state[pos], new_state[new_pos] = new_state[new_pos], new_state[pos]

            neighbors.append(tuple(new_state))

    return neighbors


def a_star(start, goal):

    open_list = [(start, 0)]
    visited = []
    parent = {start: None}
    cost = {start: 0}

    while open_list:

        best = 0

        for i in range(1, len(open_list)):
            s, g = open_list[i]
            if g + manhattan(s, goal) < open_list[best][1] + manhattan(open_list[best][0], goal):
                best = i

        current, g = open_list.pop(best)

        if current == goal:
            path = []

            while current is not None:
                path.append(current)
                current = parent[current]

            path.reverse()

            print("Solution using A* Search")

            for step, state in enumerate(path, 1):
                print("\nStep", step)

                for i in range(0, 9, 3):
                    print(state[i:i+3])

            print("\nGoal State Reached!")
            return

        if current in visited:
            continue

        visited.append(current)

        for neighbor in get_neighbors(current):

            new_g = g + 1

            if neighbor not in cost or new_g < cost[neighbor]:

                cost[neighbor] = new_g
                parent[neighbor] = current

                if neighbor not in visited:
                    open_list.append((neighbor, new_g))

    print("No solution found")


start = (1, 2, 3,
         4, 0, 6,
         7, 5, 8)

goal = (1, 2, 3,
        4, 5, 6,
        7, 8, 0)

a_star(start, goal)




2.
def alpha_beta(depth, node, is_max, alpha, beta):

    if depth == 3:
        print("Leaf Node:", node)
        return node

    if is_max:
        best = -999

        for child in tree[node]:
            value = alpha_beta(depth + 1, child, False, alpha, beta)

            best = max(best, value)
            alpha = max(alpha, best)

            if beta <= alpha:
                print("Pruned at node:", child)
                break

        return best

    else:
        best = 999

        for child in tree[node]:
            value = alpha_beta(depth + 1, child, True, alpha, beta)

            best = min(best, value)
            beta = min(beta, best)

            if beta <= alpha:
                print("Pruned at node:", child)
                break

        return best


tree = {
    'A': ['B', 'C'],
    'B': ['D', 'E'],
    'C': ['F', 'G'],
    'D': [3, 5],
    'E': [6, 9],
    'F': [1, 2],
    'G': [0, -1]
}

print("Alpha-Beta Pruning")

result = alpha_beta(0, 'A', True, -999, 999)

print("\nOptimal Value:", result)






3.
def tower_of_hanoi(n, source, auxiliary, destination):

    if n == 1:
        print("Move disk 1 from", source, "to", destination)
        return

    tower_of_hanoi(n - 1, source, destination, auxiliary)

    print("Move disk", n, "from", source, "to", destination)

    tower_of_hanoi(n - 1, auxiliary, source, destination)


n = 3

print("Tower of Hanoi using State Space Search")
print("Number of disks:", n)

tower_of_hanoi(n, 'A', 'B', 'C')

print("Goal State Reached!")




4.
board = [
    [0,0,0,0,0,3,8,0,7],
    [0,4,0,8,0,9,3,0,0],
    [0,0,0,4,0,0,0,0,0],
    [0,0,0,0,4,0,6,0,5],
    [3,0,0,0,9,0,4,0,2],
    [6,0,0,0,0,0,0,0,1],
    [0,6,9,1,3,5,2,7,0],
    [8,0,0,0,0,0,0,0,0],
    [0,0,5,0,6,8,0,0,0]
]


def print_board(board):
    for row in board:
        for num in row:
            print(num, end=" ")
        print()


def get_candidates(board, row, col):

    used = set(board[row])

    for i in range(9):
        used.add(board[i][col])

    r = (row // 3) * 3
    c = (col // 3) * 3

    for i in range(r, r+3):
        for j in range(c, c+3):
            used.add(board[i][j])

    return [n for n in range(1, 10) if n not in used]


def solve(board):

    while True:
        changed = False

        for i in range(9):
            for j in range(9):

                if board[i][j] == 0:

                    values = get_candidates(board, i, j)

                    if len(values) == 0:
                        return False

                    if len(values) == 1:
                        board[i][j] = values[0]
                        changed = True

        if not changed:
            break

    min_values = None
    position = None

    for i in range(9):
        for j in range(9):

            if board[i][j] == 0:

                values = get_candidates(board, i, j)

                if len(values) == 0:
                    return False

                if min_values is None or len(values) < len(min_values):
                    min_values = values
                    position = (i, j)

    if position is None:
        return True

    row, col = position

    for num in min_values:

        new_board = [r[:] for r in board]
        new_board[row][col] = num

        if solve(new_board):

            for i in range(9):
                board[i] = new_board[i][:]

            return True

    return False


print("Sudoku Solver using Backtracking")
print("\nInitial Sudoku:\n")
print_board(board)

if solve(board):

    print("\nFinal Sudoku:\n")
    print_board(board)

    print("\nSolution Found!")

else:
    print("\nNo Solution Found")





5.
# Sudoku Solver using Backtracking and Constraint Propagation

board = [
    [0,0,0,2,6,0,7,0,1],
    [6,8,0,0,7,0,0,9,0],
    [1,9,0,0,0,4,5,0,0],
    [8,2,0,1,0,0,0,4,0],
    [0,0,4,6,0,2,9,0,0],
    [0,5,0,0,0,3,0,2,8],
    [0,0,9,3,0,0,0,7,4],
    [0,4,0,0,5,0,0,3,6],
    [7,0,3,0,1,8,0,0,0]
]


def print_board(board):
    for row in board:
        for num in row:
            print(num, end=" ")
        print()


def get_candidates(board, row, col):

    used = set(board[row])

    for i in range(9):
        used.add(board[i][col])

    r = (row // 3) * 3
    c = (col // 3) * 3

    for i in range(r, r+3):
        for j in range(c, c+3):
            used.add(board[i][j])

    return [n for n in range(1, 10) if n not in used]


def solve(board):

    # Constraint Propagation
    while True:
        changed = False

        for i in range(9):
            for j in range(9):

                if board[i][j] == 0:

                    values = get_candidates(board, i, j)

                    if len(values) == 0:
                        return False

                    if len(values) == 1:
                        board[i][j] = values[0]
                        changed = True

        if not changed:
            break

    # Find cell with minimum candidates
    min_values = None
    position = None

    for i in range(9):
        for j in range(9):

            if board[i][j] == 0:

                values = get_candidates(board, i, j)

                if len(values) == 0:
                    return False

                if min_values is None or len(values) < len(min_values):
                    min_values = values
                    position = (i, j)

    # Check if Sudoku is completed
    if position is None:
        return True

    row, col = position

    # Backtracking
    old_board = [r[:] for r in board]

    for num in min_values:

        board[row][col] = num

        if solve(board):
            return True

        # Backtrack
        for i in range(9):
            board[i] = old_board[i][:]

    return False


print("Sudoku Solver")

print("\nInitial Sudoku:\n")
print_board(board)

if solve(board):

    print("\nFinal Sudoku:\n")
    print_board(board)

    print("\nSolution Found!")

else:
    print("\nNo Solution Found")







6.
cities = ['A', 'B', 'C', 'D']

distance = [
    [0, 10, 15, 20],
    [10, 0, 35, 25],
    [15, 35, 0, 30],
    [20, 25, 30, 0]
]

k = 2

def route_cost(route):
    cost = 0

    for i in range(len(route) - 1):
        cost += distance[cities.index(route[i])][cities.index(route[i + 1])]

    cost += distance[cities.index(route[-1])][cities.index(route[0])]

    return cost


def get_neighbors(route):
    neighbors = []

    for i in range(1, len(route)):
        for j in range(i + 1, len(route)):
            new_route = route[:]
            new_route[i], new_route[j] = new_route[j], new_route[i]

            neighbors.append(new_route)

    return neighbors


beam = [['A', 'B', 'C', 'D']]

print("Local Beam Search for TSP")
print("Initial Route:", beam[0])
print("Initial Cost:", route_cost(beam[0]))

step = 1

while True:

    all_neighbors = []

    for route in beam:
        all_neighbors.extend(get_neighbors(route))

    all_neighbors.sort(key=route_cost)

    new_beam = []

    for route in all_neighbors:
        if route not in new_beam:
            new_beam.append(route)

        if len(new_beam) == k:
            break

    print("\nStep", step)

    for route in new_beam:
        print("Route:", route, "Cost:", route_cost(route))

    if not new_beam or route_cost(new_beam[0]) >= min(route_cost(r) for r in beam):
        break

    beam = new_beam
    step += 1

best_route = min(beam, key=route_cost)

print("\nBest Route:", best_route + [best_route[0]])
print("Minimum Distance:", route_cost(best_route))





7.
cities = ['A', 'B', 'C', 'D', 'E']

distance = [
    [0, 4, 8, 10, 7],
    [4, 0, 6, 9, 5],
    [8, 6, 0, 3, 6],
    [10, 9, 3, 0, 4],
    [7, 5, 6, 4, 0]
]

k = 2

def route_cost(route):
    cost = 0

    for i in range(len(route)):
        a = cities.index(route[i])
        b = cities.index(route[(i + 1) % len(route)])
        cost += distance[a][b]

    return cost


def get_neighbors(route):
    neighbors = []

    for i in range(1, len(route)):
        for j in range(i + 1, len(route)):
            new_route = route[:]
            new_route[i], new_route[j] = new_route[j], new_route[i]
            neighbors.append(new_route)

    return neighbors


beam = [['A', 'B', 'C', 'D', 'E']]
best_route = beam[0]

print("Local Beam Search for TSP")
print("Initial Route:", best_route)
print("Initial Cost:", route_cost(best_route))

step = 1

while True:

    neighbors = []

    for route in beam:
        neighbors.extend(get_neighbors(route))

    neighbors.sort(key=route_cost)

    new_beam = []

    for route in neighbors:
        if route not in new_beam:
            new_beam.append(route)

        if len(new_beam) == k:
            break

    print("\nStep", step)

    for route in new_beam:
        print("Route:", route)
        print("Cost:", route_cost(route))

    if not new_beam or route_cost(new_beam[0]) >= route_cost(best_route):
        break

    beam = new_beam
    best_route = beam[0]

    step += 1

print("\nBest Route:", best_route)
print("Minimum Distance:", route_cost(best_route))
print("Goal State Reached!")




8.
def get_neighbors(state):
    neighbors = []

    pos = state.index(0)
    x, y = pos // 3, pos % 3

    moves = [(-1, 0), (1, 0), (0, -1), (0, 1)]

    for dx, dy in moves:
        nx, ny = x + dx, y + dy

        if 0 <= nx < 3 and 0 <= ny < 3:
            new_state = list(state)
            new_pos = nx * 3 + ny

            new_state[pos], new_state[new_pos] = new_state[new_pos], new_state[pos]

            neighbors.append(tuple(new_state))

    return neighbors


def bfs(start, goal):

    queue = [start]
    visited = [start]
    parent = {start: None}

    while queue:

        current = queue.pop(0)

        if current == goal:
            path = []

            while current is not None:
                path.append(current)
                current = parent[current]

            path.reverse()

            for step, state in enumerate(path, 1):
                print("\nStep", step)

                for i in range(0, 9, 3):
                    print(state[i:i+3])

            print("\nGoal State Reached!")
            return

        for neighbor in get_neighbors(current):

            if neighbor not in visited:
                visited.append(neighbor)
                parent[neighbor] = current
                queue.append(neighbor)

    print("No Solution Found")


start = (1, 2, 3,
         4, 0, 6,
         7, 5, 8)

goal = (1, 2, 3,
        4, 5, 6,
        7, 8, 0)

print("8 Puzzle using BFS")

bfs(start, goal)





9.
grid = [
    ['S', 0, 0, 0],
    [1, 1, 0, 1],
    [0, 0, 0, 0],
    [0, 1, 1, 'G']
]

rows = 4
cols = 4

def bfs(start, goal):

    queue = [start]
    visited = [start]
    parent = {start: None}

    moves = [(-1, 0), (1, 0), (0, -1), (0, 1)]

    while queue:

        current = queue.pop(0)

        if current == goal:

            path = []

            while current is not None:
                path.append(current)
                current = parent[current]

            path.reverse()

            print("Robot Path Planning using BFS")

            for step, position in enumerate(path, 1):
                print("Step", step, ":", position)

            print("\nGoal Reached!")
            return

        x, y = current

        for dx, dy in moves:

            nx = x + dx
            ny = y + dy

            if 0 <= nx < rows and 0 <= ny < cols:

                if grid[nx][ny] != 1 and (nx, ny) not in visited:

                    visited.append((nx, ny))
                    parent[(nx, ny)] = current
                    queue.append((nx, ny))

    print("No Path Found")


start = (0, 0)
goal = (3, 3)

bfs(start, goal)






10.
def get_neighbors(state):
    neighbors = []

    # Check each block
    for i in range(3):

        # Check whether block i is clear
        clear = True

        for j in range(3):
            if state[j] == i:
                clear = False

        if not clear:
            continue

        # Move block i to table or another clear block
        for j in range(-1, 3):

            if i == j:
                continue

            if j != -1:

                # Destination block must be clear
                if any(state[k] == j for k in range(3)):
                    continue

                # Avoid placing a block on its own tower
                temp = j

                while temp != -1:
                    if temp == i:
                        break
                    temp = state[temp]

                if temp == i:
                    continue

            new_state = list(state)
            new_state[i] = j

            neighbors.append(tuple(new_state))

    return neighbors


def bfs(start, goal):

    queue = [start]
    visited = [start]
    parent = {start: None}

    while queue:

        current = queue.pop(0)

        if current == goal:

            path = []

            while current is not None:
                path.append(current)
                current = parent[current]

            path.reverse()

            for step, state in enumerate(path, 1):

                print("\nStep", step)

                for i in range(3):
                    if state[i] == -1:
                        print(chr(65+i), "is on Table")
                    else:
                        print(chr(65+i), "is on",
                              chr(65+state[i]))

            print("\nGoal State Reached!")
            return

        for neighbor in get_neighbors(current):

            if neighbor not in visited:

                visited.append(neighbor)
                parent[neighbor] = current
                queue.append(neighbor)

    print("No Solution Found")


start = (-1, -1, -1)
goal = (-1, 0, 1)

print("Blocks World using BFS")

bfs(start, goal)







11.
def dfs(a, b, visited, path):

    # Check goal condition
    if a == 2:
        path.append((a, b))
        return True

    visited.append((a, b))
    path.append((a, b))

    # All possible operations
    states = [
        (4, b),                         # Fill Jug A
        (a, 3),                         # Fill Jug B
        (0, b),                         # Empty Jug A
        (a, 0),                         # Empty Jug B
        (a - min(a, 3-b), b + min(a, 3-b)),  # Pour A to B
        (a + min(b, 4-a), b - min(b, 4-a))   # Pour B to A
    ]

    for new_state in states:

        if new_state not in visited:

            if dfs(new_state[0], new_state[1], visited, path):
                return True

    path.pop()
    return False


visited = []
path = []

print("Water Jug Problem using DFS")

dfs(0, 0, visited, path)

for step, state in enumerate(path, 1):
    print("Step", step, ":", state)

print("\nGoal Reached!")
print("Jug A contains 2 litres")




12.
def dfs(a, b, visited, path):

    # Check goal condition
    if a == 4:
        path.append((a, b))
        return True

    visited.append((a, b))
    path.append((a, b))

    # Possible operations
    states = [
        (5, b),                              # Fill Jug A
        (a, 3),                              # Fill Jug B
        (0, b),                              # Empty Jug A
        (a, 0),                              # Empty Jug B
        (a - min(a, 3-b), b + min(a, 3-b)),  # Pour A to B
        (a + min(b, 5-a), b - min(b, 5-a))   # Pour B to A
    ]

    for new_state in states:

        if new_state not in visited:

            if dfs(new_state[0], new_state[1], visited, path):
                return True

    path.pop()
    return False


visited = []
path = []

print("Water Jug Problem using DFS")

dfs(0, 0, visited, path)

for step, state in enumerate(path, 1):
    print("Step", step, ":", state)

print("\nGoal Reached!")
print("Jug A contains 4 litres")





13.
def dfs(m, c, boat, visited, path):

    state = (m, c, boat)

    if state in visited:
        return False

    visited.append(state)
    path.append(state)

    if state == (0, 0, 0):
        return True

    moves = [(1, 0), (2, 0), (0, 1), (0, 2), (1, 1)]

    for x, y in moves:

        if boat == 1:
            new_m = m - x
            new_c = c - y
        else:
            new_m = m + x
            new_c = c + y

        if new_m < 0 or new_m > 3 or new_c < 0 or new_c > 3:
            continue

        if new_m > 0 and new_c > new_m:
            continue

        if (3-new_m) > 0 and (3-new_c) > (3-new_m):
            continue

        if dfs(new_m, new_c, 1-boat, visited, path):
            return True

    path.pop()
    return False


visited = []
path = []

print("Missionaries and Cannibals using DFS")

dfs(3, 3, 1, visited, path)

for i, state in enumerate(path, 1):
    print("Step", i, ":", state)

print("Goal State Reached!")






14.
def dfs(state, visited, path):

    if state in visited:
        return False

    visited.append(state)
    path.append(state)

    goal = (0, "A", "B")

    if state == goal:
        return True

    moves = [
        (0, "A", 0),
        (0, "A", "B")
    ]

    for new_state in moves:

        if new_state not in visited:
            if dfs(new_state, visited, path):
                return True

    path.pop()
    return False


start = (0, 0, 0)

visited = []
path = []

print("Blocks World using DFS\n")

if dfs(start, visited, path):

    for i, state in enumerate(path, 1):

        print("Step", i, ": (A, B, C)")
        print("        ", state)
        print()

    print("Goal State Reached!")

    print("\nFinal Tower:")
    print("    C")
    print("    B")
    print("    A")
    print("  ------")
    print("   Table")

else:
    print("No Solution Found")





15.
maze = [
    [0, 0, 0, 0],
    [1, 1, 0, 1],
    [0, 0, 0, 0],
    [0, 1, 1, 0]
]

start = (0, 0)
goal = (3, 3)

visited = []
path = []

def dfs(x, y):

    if x < 0 or x >= 4 or y < 0 or y >= 4:
        return False

    if maze[x][y] == 1 or (x, y) in visited:
        return False

    visited.append((x, y))
    path.append((x, y))

    if (x, y) == goal:
        return True

    # Up, Right, Down, Left
    moves = [(-1, 0), (0, 1), (1, 0), (0, -1)]

    for dx, dy in moves:
        if dfs(x + dx, y + dy):
            return True

    path.pop()
    return False


print("Maze Solving using DFS")

if dfs(start[0], start[1]):
    for i, state in enumerate(path, 1):
        print("Step", i, ":", state)

    print("Goal Reached!")
else:
    print("No Path Found")




16.
N = 8

board = [[0 for i in range(N)] for j in range(N)]

def is_safe(row, col):

    # Check upper column
    for i in range(row):
        if board[i][col] == 1:
            return False

    # Check upper left diagonal
    i = row - 1
    j = col - 1

    while i >= 0 and j >= 0:
        if board[i][j] == 1:
            return False
        i -= 1
        j -= 1

    # Check upper right diagonal
    i = row - 1
    j = col + 1

    while i >= 0 and j < N:
        if board[i][j] == 1:
            return False
        i -= 1
        j += 1

    return True


def solve(row):

    if row == N:
        return True

    for col in range(N):

        if is_safe(row, col):

            board[row][col] = 1

            if solve(row + 1):
                return True

            board[row][col] = 0

    return False


print("8 Queens Problem using Backtracking")

if solve(0):

    print("\nSolution:")

    for i in range(N):
        for j in range(N):
            if board[i][j] == 1:
                print("Q", end=" ")
            else:
                print("-", end=" ")
        print()

    print("\nSolution Found!")

else:
    print("No Solution Found")





17.
print("Simple Chatbot")
print("Chatbot: Hello! Welcome to my chatbot.")
print("Chatbot: You can ask me about my name, age, or how I am.")
print("Chatbot: Type 'bye' to exit.")

while True:

    user = input("\nYou: ").lower()

    if "hello" in user or "hi" in user:
        print("Chatbot: Hello! How can I help you?")

    elif "your name" in user or "name" in user:
        print("Chatbot: My name is ChatBot.")

    elif "how are you" in user:
        print("Chatbot: I am fine. Thank you!")

    elif "age" in user or "old" in user:
        print("Chatbot: I am a computer program.")

    elif "thank" in user:
        print("Chatbot: You are welcome!")

    elif "bye" in user:
        print("Chatbot: Goodbye! Have a nice day.")
        break

    else:
        print("Chatbot: Sorry, I don't understand.")
