# Ex.No: 1  Implementation of HotPotato game using Queue 
### DATE: 09/08/2024                                                                           
### REGISTER NUMBER : 212222240090
## AIM: 
To write a python program to simulate the process of passing an item among players and eliminating players based on the given rules until a single winner is determined.
### Algorithm:
1. Initialize the Queue: Create a queue and enqueue all the participants.
2. Pass the Potato: Dequeue the first person in the queue and enqueue them at the end. This simulates passing the potato.
3. Count the Passes: Repeat the passing for a given number of times.
4. Eliminate the Holder: After the set number of passes, remove the person who holds the potato (dequeue the front of the queue).
5. Repeat: Continue the process until only one person remains in the queue.
### Program:

```
NAME: M Sanjay
REG NO: 212222240090
```
```python
import queue
import random
import time

def hot_potato(names, num):
    sim_queue = queue.Queue()

    for name in names:
        sim_queue.put(name)

    while sim_queue.qsize() > 1:
        for _ in range(num):
            sim_queue.put(sim_queue.get())
        eliminated = sim_queue.get()
        print(f"{eliminated} is eliminated!")

    return sim_queue.get()

def main():
    players = ["Alice", "Bob", "Charlie", "David", "Eve"]
    #num_passes = random.randint(1, 10)
    num_passes=1
    print("Hot Potato Game Start!")
    time.sleep(1)
    winner = hot_potato(players, num_passes)
    print(f"\nThe winner is: {winner}")

if __name__ == "__main__":
    main()
```

### Output:
<img src="https://github.com/user-attachments/assets/d6eff734-e0fe-4a7c-964b-1f724c78627c" width=50%>



### Result:
Thus the simple HotPotato game was implemented using Queue.

