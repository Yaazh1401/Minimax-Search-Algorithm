<h1>ExpNo 5 : Implement Minimax Search Algorithm for a Simple TIC-TAC-TOE game</h1> 
<h3>Name: YAAZHINI S      </h3>
<h3>Register Number/Staff Id: 212224230308      </h3>
<H3>Aim:</H3>
<p>
    Implement Minimax Search Algorithm for a Simple TIC-TAC-TOE game
</p>

<H3>Theory and Procedure:</H3>
To begin, let's start by defining what it means to play a perfect game of tic tac toe:

If I play perfectly, every time I play I will either win the game, or I will draw the game. Furthermore if I play against another perfect player, I will always draw the game.

How might we describe these situations quantitatively? Let's assign a score to the "end game conditions:"

I win, hurray! I get 10 points!
I lose, shit. I lose 10 points (because the other player gets 10 points)
I draw, whatever. I get zero points, nobody gets any points.
So now we have a situation where we can determine a possible score for any game end state.

Looking at a Brief Example
To apply this, let's take an example from near the end of a game, where it is my turn. I am X. My goal here, obviously, is to maximize my end game score.

![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/498656fc-79ce-4234-a623-06568bad8dda)


If the top of this image represents the state of the game I see when it is my turn, then I have some choices to make, there are three places I can play, one of which clearly results in me wining and earning the 10 points. If I don't make that move, O could very easily win. And I don't want O to win, so my goal here, as the first player, should be to pick the maximum scoring move.

But What About O?
What do we know about O? Well we should assume that O is also playing to win this game, but relative to us, the first player, O wants obviously wants to chose the move that results in the worst score for us, it wants to pick a move that would minimize our ultimate score. Let's look at things from O's perspective, starting with the two other game states from above in which we don't immediately win:

![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/029b1a70-e92e-46c0-9a32-d6aea98ecd9d)

The choice is clear, O would pick any of the moves that result in a score of -10.

Describing Minimax
The key to the Minimax algorithm is a back and forth between the two players, where the player whose "turn it is" desires to pick the move with the maximum score. In turn, the scores for each of the available moves are determined by the opposing player deciding which of its available moves has the minimum score. And the scores for the opposing players moves are again determined by the turn-taking player trying to maximize its score and so on all the way down the move tree to an end state.

A description for the algorithm, assuming X is the "turn taking player," would look something like:

If the game is over, return the score from X's perspective.
Otherwise get a list of new game states for every possible move
Create a scores list
For each of these states add the minimax result of that state to the scores list
If it's X's turn, return the maximum score from the scores list
If it's O's turn, return the minimum score from the scores list
You'll notice that this algorithm is recursive, it flips back and forth between the players until a final score is found.

Let's walk through the algorithm's execution with the full move tree, and show why, algorithmically, the instant winning move will be picked:

![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/12b82542-54fb-47e7-8f76-b75fddc40f92)
<ul>
<li>It's X's turn in state 1. X generates the states 2, 3, and 4 and calls minimax on those states.</li>
<li>State 2 pushes the score of +10 to state 1's score list, because the game is in an end state.</li>
<li>State 3 and 4 are not in end states, so 3 generates states 5 and 6 and calls minimax on them, while state 4 generates states 7 and 8 and calls minimax on them.</li>
<li>State 5 pushes a score of -10 onto state 3's score list, while the same happens for state 7 which pushes a score of -10 onto state 4's score list.</li>
<li>State 6 and 8 generate the only available moves, which are end states, and so both of them add the score of +10 to the move lists of states 3 and 4.</li>
<li>Because it is O's turn in both state 3 and 4, O will seek to find the minimum score, and given the choice between -10 and +10, both states 3 and 4 will yield -10.</li>
<li>>Finally the score list for states 2, 3, and 4 are populated with +10, -10 and -10 respectively, and state 1 seeking to maximize the score will chose the winning move with score +10, state 2.</li
</ul>
##A Coded Version of Minimax Hopefully by now you have a rough sense of how th e minimax algorithm determines the best move to play. Let's examine my implementation of the algorithm to solidify the understanding:

Here is the function for scoring the game:

# @player is the turn taking player
def score(game)
    if game.win?(@player)
        return 10
    elsif game.win?(@opponent)
        return -10
    else
        return 0
    end
end
Simple enough, return +10 if the current player wins the game, -10 if the other player wins and 0 for a draw. You will note that who the player is doesn't matter. X or O is irrelevant, only who's turn it happens to be.

And now the actual minimax algorithm; note that in this implementation a choice or move is simply a row / column address on the board, for example [0,2] is the top right square on a 3x3 board.

def minimax(game)
    return score(game) if game.over?
    scores = [] # an array of scores
    moves = []  # an array of moves

    # Populate the scores array, recursing as needed
    game.get_available_moves.each do |move|
        possible_game = game.get_new_state(move)
        scores.push minimax(possible_game)
        moves.push move
    end

    # Do the min or the max calculation
    if game.active_turn == @player
        # This is the max calculation
        max_score_index = scores.each_with_index.max[1]
        @choice = moves[max_score_index]
        return scores[max_score_index]
    else
        # This is the min calculation
        min_score_index = scores.each_with_index.min[1]
        @choice = moves[min_score_index]
        return scores[min_score_index]
    end
end

<hr>
<h2>Sample Input and Output</h2>

![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/6b668685-8bcc-43c5-b5c2-ddd43f3da84a)
![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/8ca1b08a-8312-4ef5-89df-e69b7b2c3fa2)
![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/dc06427a-d4ce-43a1-95bd-9acfaefac323)
![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/a8a27e2a-6fd4-46a2-afb5-6d27b8556702)
![image](https://github.com/natsaravanan/19AI405FUNDAMENTALSOFARTIFICIALINTELLIGENCE/assets/87870499/a2acb6a1-ed8e-42e5-8968-fe805e4b0255)

<hr>

<h2>Program:</h2>

```python
def initialize_game():
    current_state = [['.','.','.'],['.','.','.'],['.','.','.']]
    player_turn = 'X'
def drawboard():
    for i in range(3):
        for j in range(3):
            print("{}|".format(current_state[i][j]),end="")
        print()
def isvalid(px,py):
    if px<0 or px>2 or py<0 or py>2:
        return False
    elif current_state[px][py]!='.':
        return False
    else:
        return True
def is_end():
    #vertical win
    for i in range(3):
        if (current_state[0][i]!='.' and current_state[0][i]==current_state[1][i] and
            current_state[1][i]==current_state[2][i]):
            return current_state[0][i]
    #horizontal win
    for i in range(3):
        if current_state[i]==['X','X','X']:
            return 'X'
        elif current_state[i]==['O','O','O']:
            return 'O'
    #inverse diagonal win
    for i in range(3):
        if (current_state[0][2]==current_state[1][1] and current_state[1][1]==current_state[2][0]
            and current_state[0][2]!='.'):
            return current_state[0][2]
    #diagonal win
    for i in range(3):
        if (current_state[0][0]==current_state[1][1] and current_state[1][1]==current_state[2][2]
            and current_state[0][0]!='.'):
            return current_state[0][0]
    #check for board
    for i in range(3):
        for j in range(3):
            if current_state[i][j]=='.':
                return None
    return '.' #it is a tie
def minimum():
    minv=2
    qx=None
    qy=None
    result=is_end()
    if result=='X':
        return(-1,0,0)
    elif result=='O':
        return(+1,0,0)
    elif result=='.':
        return(0,0,0)
    for i in range(3):
        for j in range(3):
            if current_state[i][j]=='.':
                current_state[i][j]='X'
                (m,min_i,min_j)=maximum()
                if m<minv:
                    minv=m
                    qx=i
                    qy=j
                current_state[i][j]='.'
    return (minv,qx,qy)
def maximum():
    maxv=-2
    px=None
    py=None
    result=is_end()
    if result=='X':
        return(-1,0,0)
    elif result=='O':
        return(+1,0,0)
    elif result=='.':
        return(0,0,0)
    for i in range(3):
        for j in range(3):
            if current_state[i][j]=='.':
                current_state[i][j]='O'
                (m,max_i,max_j)=minimum()
                if m>maxv:
                    maxv=m
                    px=i
                    py=j
                current_state[i][j]='.'
    return (maxv,px,py)
def play():
    #initialize_game()
    #drawboard()
    player_turn = 'X'
    while True:
        result = is_end()
        drawboard()
        if result != None:
            if result == 'X':
                print("The winner is X")
            elif result == 'O':
                print("The winner is O")
            else:
                print("It is a tie!")
            initialize_game()
            return
        if player_turn == 'X':
            (m,qx,qy)=minimum()
            while True:
                print("x:{},y:{}".format(qx,qy))
                px = int(input())
                py = int(input())
                (qx, qy) = (px, py)
                if isvalid(px,py):
                    current_state[px][py]='X'
                    player_turn = 'O'
                    break
                else:
                    print("Enter correct values for x and y")
        else:
            (m,px,py)=maximum()
            current_state[px][py]='O'
            player_turn='X'
current_state = [['.','.','.'],['.','.','.'],['.','.','.']]
play()
```

<hr>
<h2>Output:</h2>
<img width="1792" height="986" alt="image" src="https://github.com/user-attachments/assets/01ff7c2e-eb72-4162-a29a-d45e2b0b2d8c" />

<img width="1920" height="754" alt="image" src="https://github.com/user-attachments/assets/1d647531-e2ce-4870-b11f-bf03d1692f76" />


<h2>Result:</h2>
<p>Thus,Implementation of  Minimax Search Algorithm for a Simple TIC-TAC-TOE game wasa done successfully.</p>
