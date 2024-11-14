# Ex.No: 10 Implementation of Negamax algorithm 
**DATE:10	18.10.2024**<br>
**REGISTER NUMBER : 212222240090**
### Aim:
Write a negamax algorithm to find the optimal value of Player from the graph.

### Steps:
1.Start the program
2.Define the minimax function
3.If maximum depth is reached then return the score value of leaf node. [depth taken as 3]
4.In Max player turn, assign the  maximum value by calling the negamax function recursively.
5.In Min player turn, finding the maximum value by taking the negation of its children.
6.Specify the score value of leaf nodes and Call the negamax function.
7.Print the best value of Max player.
8.Stop the program.
## Program:
```py
import math
def negamax(curDepth, nodeIndex, scores, targetDepth):
    if curDepth == targetDepth:
        return scores[nodeIndex]
    value1 = negamax(curDepth + 1, nodeIndex * 2, scores, targetDepth)
    value2 = negamax(curDepth + 1, nodeIndex * 2 + 1, scores, targetDepth)
    return max(-value1, -value2)  # Flip the sign for the other player's turn
scores = [3, 5, 2, 9, 12, 5, 23, 20]
treeDepth = math.log(len(scores), 2)  # Calculate depth of node, log(8, base 2) = 3
print("The optimal value is: ", end="")
print(negamax(0, 0, scores, int(treeDepth)))
```
## Output:
![image](https://github.com/user-attachments/assets/7513a519-9c1c-4276-b24f-c6cdc69c190f)

## Result:
Thus the best score of max player was found using negamax algorithm.
