import heapq
from itertools import count
from typing import Callable, Dict, Iterable, List, Optional, Tuple
from functools import lru_cache

State = Tuple[int, ...]  # 9-length tuple; 0 represents the blank
GOAL: State = (1, 2, 3, 4, 5, 6, 7, 8, 0)

# Directions for blank movement: (dr, dc, move_char)
DIRS = [(-1, 0, 'U'), (1, 0, 'D'), (0, -1, 'L'), (0, 1, 'R')]


def index_to_rc(i: int) -> Tuple[int, int]:
    return divmod(i, 3)


def rc_to_index(r: int, c: int) -> int:
    return r * 3 + c


def is_solvable(state: State) -> bool:
    """An 8-puzzle is solvable iff the inversion count (ignoring 0) is even."""
    arr = [x for x in state if x != 0]
    inv = 0
    for i in range(len(arr)):
        for j in range(i + 1, len(arr)):
            if arr[i] > arr[j]:
                inv += 1
    return inv % 2 == 0


def neighbors(state: State) -> Iterable[Tuple[State, str]]:
    """Yield (neighbor_state, move_char) for each legal slide."""
    i0 = state.index(0)
    r0, c0 = index_to_rc(i0)
    for dr, dc, mv in DIRS:
        nr, nc = r0 + dr, c0 + dc
        if 0 <= nr < 3 and 0 <= nc < 3:
            ni = rc_to_index(nr, nc)
            new_state = list(state)
            new_state[i0], new_state[ni] = new_state[ni], new_state[i0]
            yield tuple(new_state), mv


def reconstruct_path(parents: Dict[State, State], end: State) -> List[State]:
    path = [end]
    while end in parents:
        end = parents[end]
        path.append(end)
    path.reverse()
    return path


def path_to_moves(path: List[State]) -> str:
    """Convert a path of states into a string of moves 'UDLR'."""
    moves = []
    for a, b in zip(path, path[1:]):
        ia, ib = a.index(0), b.index(0)
        ra, ca = index_to_rc(ia)
        rb, cb = index_to_rc(ib)
        if rb == ra - 1 and cb == ca:
            moves.append('U')
        elif rb == ra + 1 and cb == ca:
            moves.append('D')
        elif rb == ra and cb == ca - 1:
            moves.append('L')
        elif rb == ra and cb == ca + 1:
            moves.append('R')
        else:
            moves.append('?')
    return ''.join(moves)


def misplaced_tiles(state: State, goal: State = GOAL) -> int:
    """Heuristic: number of tiles not in their goal position (ignore blank)."""
    return sum(1 for i, v in enumerate(state) if v != 0 and v != goal[i])


@lru_cache(maxsize=None)
def _goal_positions(goal: State) -> Tuple[Tuple[int, int], ...]:
    """Cache goal positions as a tuple indexed by tile value (0..8)."""
    pos = [(0, 0)] * 9
    for i, v in enumerate(goal):
        r, c = index_to_rc(i)
        pos[v] = (r, c)
    return tuple(pos)


def manhattan_distance(state: State, goal: State = GOAL) -> int:
    """Heuristic: sum of Manhattan distances of each tile to its goal position."""
    pos = _goal_positions(goal)
    total = 0
    for i, v in enumerate(state):
        if v == 0:
            continue
        r, c = index_to_rc(i)
        gr, gc = pos[v]
        total += abs(r - gr) + abs(c - gc)
    return total


def a_star(
    start: State,
    heuristic: Callable[[State, State], int] = manhattan_distance,
    goal: State = GOAL,
) -> Optional[Dict[str, object]]:
    """
    A* search for the 8-puzzle.
    Returns a dict with keys: path, moves, expanded_nodes, or None if unsolvable.
    """
    if start == goal:
        return {"path": [start], "moves": 0, "expanded_nodes": 0}

    if not is_solvable(start):
        return None

    # Each heap item: (f, g, tie, state)
    heap: List[Tuple[int, int, int, State]] = []
    tie = count()
    g0 = 0
    h0 = heuristic(start, goal)
    heapq.heappush(heap, (g0 + h0, g0, next(tie), start))

    best_g: Dict[State, int] = {start: 0}
    parents: Dict[State, State] = {}
    expanded = 0

    while heap:
        f, g, _, state = heapq.heappop(heap)
        expanded += 1

        if state == goal:
            path = reconstruct_path(parents, state)
            return {
                "path": path,
                "moves": len(path) - 1,
                "expanded_nodes": expanded,
                "move_sequence": path_to_moves(path),
            }

        for nb, _mv in neighbors(state):
            new_g = g + 1
            if nb not in best_g or new_g < best_g[nb]:
                best_g[nb] = new_g
                parents[nb] = state
                h = heuristic(nb, goal)
                heapq.heappush(heap, (new_g + h, new_g, next(tie), nb))

    return None  # Shouldn't happen for solvable instances


def print_board(state: State) -> None:
    """Pretty-print a 3x3 board."""
    for r in range(3):
        row = state[3 * r : 3 * r + 3]
        print(" ".join(str(x) if x != 0 else "·" for x in row))
    print()


def parse_state(s: str) -> State:
    """
    Parse a state from a string like "1,2,3,4,0,6,7,5,8" or "123406758".
    """
    s = s.strip().replace(" ", "")
    if "," in s:
        nums = [int(x) for x in s.split(",")]
    else:
        if len(s) != 9 or not s.isdigit():
            raise ValueError("Provide 9 digits including 0 for the blank, or 9 comma-separated numbers.")
        nums = [int(ch) for ch in s]
    if len(nums) != 9 or set(nums) != set(range(9)):
        raise ValueError("State must contain exactly the numbers 0..8 once each.")
    return tuple(nums)  # type_ignore[return-value]


def main(start_state_str: str = "1,2,3,4,0,6,7,5,8", goal_state_str: str = "1,2,3,4,5,6,7,8,0", heuristic_name: str = "manhattan", show_path: bool = True):
    start = parse_state(start_state_str)
    goal = parse_state(goal_state_str)

    # Reset goal positions cache if using a different goal
    _goal_positions.cache_clear()

    heur: Callable[[State, State], int] = manhattan_distance if heuristic_name == "manhattan" \
        else misplaced_tiles

    result = a_star(start, heuristic=heur, goal=goal)
    if result is None:
        print("This puzzle is unsolvable.")
        return

    path: List[State] = result["path"]  # type_ignore[assignment]
    moves: int = result["moves"]        # type_ignore[assignment]
    expanded: int = result["expanded_nodes"]  # type_ignore[assignment]
    seq: str = result.get("move_sequence", "")  # type_ignore[assignment]

    print(f"Solved in {moves} moves using {heuristic_name} (expanded {expanded} states).")
    print(f"Move sequence: {seq}")
    if show_path:
        print("\nPath:")
        for i, st in enumerate(path):
            print(f"Step {i}:")
            print_board(st)

main()
