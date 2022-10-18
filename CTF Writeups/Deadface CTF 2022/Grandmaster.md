# Solution

- **Forewarning: my solution is a very hack-y solution and isn't the best one, but it works. It's messy and not the most efficient, fast, or nicest looking**
- So, immediately I looked for a python library that could possibly import this chart. I found python chess, which as it turns out, is probably the exact program that created this ascii chart and is running in the backend, but doesn't have the ability to import the raw text.
- Notes on my program:
    - I originally was going to use the calculate best move in the python chess library, but had some issues getting it to work properly. As such, I moved to using a library that was dedicated to using stockfish. The flip side of this is that the library didn't output the best move in standard algebraic notation, so I had to splice this up, and since the notation changes based on whether there can be ambiguity or taking pieces, I just ran the program multiple times until one worked
    - In the end, the python chess just allowed for a more "organized" way to generate the FEN which I just passed to the stockfish library. I could have cut it out, but at the time, I was assuming I'd use its solver functionality
    - Chess boards are almost always displayed from left to right a - h, and top to bottom 8 - 1, so I had to fiddle a bit to make sure the program properly set the right piece locations.
    - White pieces are uppercase, black pieces are lowercase
- Final python program:

```python
import ast
import socket
import chess
import chess.engine
from stockfish import Stockfish
with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.connect(("grandmaster.deadface.io", 5000))
    s.recv(1024)
    data = s.recv(1024).decode().split('\n')
    print(data)
    data = [i.split(' ') for i in data]
    data.pop()
    print(data)

    board = chess.Board()

    board.clear_board()

    board.set_piece_at(chess.parse_square('a1'), chess.Piece(chess.PAWN, chess.WHITE))

    rownum = 8
    for row in data:
        letter = 'a'
        for square in row:

            piece = ""
            if square == '.':
                board.remove_piece_at(chess.parse_square(letter + str(rownum)))
            else:
                color = chess.WHITE
                pieces = {
                    "p": chess.PAWN,
                    "n": chess.KNIGHT,
                    "b": chess.BISHOP,
                    "r": chess.ROOK,
                    "q": chess.QUEEN,
                    "k": chess.KING,
                }
                if square.islower():
                    color = chess.BLACK
                piece = pieces[square.lower()]

                board.set_piece_at(chess.parse_square(letter + str(rownum)), chess.Piece(piece, color))
            letter = chr(bytes(letter, 'utf-8')[0] + 1)
        rownum -= 1

    print(board)

    stockfish = Stockfish("/opt/homebrew/bin/stockfish")
    print(board.fen())
    stockfish.set_fen_position(board.fen())

    r = stockfish.get_best_move(2000)

    print(r)

    moved_piece = board.piece_at(chess.parse_square(r[0:2]))
    print(moved_piece)
    r = str(moved_piece) + str(stockfish.get_best_move(2000))[2:4]
    print(r)
    s.sendall(r.encode())
    data = s.recv(1024)
    print(data)
```

# Original CTF Challenge

DEADFACE has written another challenge to test the skills of new recruits. White to move. (Please use Short Algebraic Notation)

grandmaster.deadface.io:5000

The flag will be in the format. flag{.*}.

## Example socket connections:

```
Which move forces mate in 2?
. . . . N . . .
. . . p . . . .
B p p . P . b R
. r . k . p . .
n P . . R P . .
. . p . . . . .
K . P P N . . .
. . . . . . . Q
Too slow! Git Good.

Which move forces mate in 2?
. . . . . . . .
. . . . . . . N
. . K . . . k p
. . . . . N . .
. . . . . . . .
. . B . . . . .
. . . . Q . . .
. . . . . . . .
Too slow! Git Good.

Which move forces mate in 2?
. . . . . . K .
b . B Q . . . .
. . . . . . . .
p . . . . . . .
. . . . k . . .
r . . . . p . .
. . P . . . . .
. . . N . n . .
Too slow! Git Good.
```