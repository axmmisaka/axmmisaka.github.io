# Another way to approach Dynamic Programming
Well, all the programs are provided in Python3. However, C version may also be made available some time later.

## Introduction
What is Dynamic Programming? To be fair I cannot give you the precise definition of it since... Well, I still cannot master it. However, I hope that this little thing can help you to achieve something - at least know how it works and how to write a usable code.    
And for your information, DP is very useful in solving DAG(i.e. Directed Acyclic Graph) problems. Please try to link them together and you may know when to use DP and when not.   
All contents are written by me and is released under CC-BY-NC-ND 4.0 Intl'. However, special thanks to ZHW in Peking University, a teacher of mine.

## Classic Pyramid Problem
This problem can be find on many OJ websites, such as USACO training.  
Well, here's the question: you got a Pyramid made by number like this:  
```
    1
   4 5
  7 8 9
 2 8 2 3
4 1 6 2 4
```
From the top number, each time you can go down and go left or right. What you need to do is to find a route that gives you the biggest sum of all numbers along your route.'  

### Simplifying the problem
For a pyramid like this:
```
  1
 2 3
4 5 6
```
We can rewrite it like this and assign them some number:  
```
  0 1 2 3 4
0 0 0 0 0 0
1 0 1 0 0 0
2 0 2 3 0 0
3 0 4 5 6 0
4 0 0 0 0 0
```
(You may want to know why do we have so many 0. That's for convinence and prevention of memory overflow.)  
Good. Read the pyramid into an array, and we're good to go.  
Also, you may notify now you can go down or go downright now.  

### Brute-force solution
The brute-force solution is very easy to consider and implement - using recursion.  
```
MAXN = 5
def brute_force(x, y):
	if x==MAXN:
		return list[x][y]
	else:
		return list[x][y]+max(brute_force(x+1,y),brute_force(x+1,y+1))

print(brute_force(1,1))	
```

### Memorization
You will notify, if you tried, that brute-force solution is very slow - because this is O(2^n) complexity.  
You will also notice that a lot stuff is calculated repeatedly, and we have to keep in mind that the same x and y value gives the same return value.   
Let us define a list, `arr[x][y]`, that stores the max-sum route starting from (x,y), and it can be easily inferred that what we want in this question is `arr[1][1]`.  
When calling memorization(), we will check if `arr[x][y]` is calculated. If so, we use it, otherwise we calculate it.  
We initially set all arr to -1.  
Here's the code:
```
MAXN = 5
def memorization(x, y):
	if x==MAXN:
		arr[x][y] = list[x][y] if arr[x][y]==-1 else arr[x][y]
		return arr[x][y]
	else:
		return list[x][y]+max(memorization(x+1,y) if arr[x+1][y]==-1 else arr[x+1][y],memorization(x+1,y+1) if arr[x+1][y+1]==-1 else arr[x+1][y+1])

print(memorization(1,1))
```

### Dynamic Programming
Some of you may easily come out with the DP transition equation (as we call it in Chinese), shown below.  
``
dp[x][y] = list[x][y]+max(dp[x+1][y],dp[x+1][y+1])
``
The "dp" here is the same thing as arr in memorization. It means the biggest route sum starting from (x,y) is the current number add the biggest route sum of such thing of two "next step".  
And from the recurrsion (or memorization if you want), you'll find out that the only nodes that does not require the dp data from other node is at the last row, and their dp value are themself.   
Then you'll find no recursion is required, and this is probably(I use this  word because I am not sure also) what we call "Dynamic Programming".  
Here's the code:
```
for foo in range(1,MAXN+1):
	for bar in range(1,MAXN+1):
		dp[foo][bar] = list[foo][bar] + max(dp[foo + 1][bar], dp[foo + 1][bar + 1])
#Do keep in mind that the default value of all dp is 0, and thus we don't need to specially calculate the last row.
print(dp[1][1])
```

## Summary
I have no idea if I made everything clear. However, let's see what do we need to implement dp. This is not a definite list that you must have to implement dp, and some of them are redundant. I personally cannot master dp, as I said before, so I'll just stack everything here just to provide some information.  
1. The question can be considered as a DAG, i.e. directed acylic graph.
2. If you can write a brute-force search, perferably depth-first search(DFS), you'll need to make sure that for same input argument, the search will output the same value, disregarding how the search was done previously. 
3. Still, in the search, you have to make sure that there's some "boundry", or so-called "base case" in recursion, which does not rely on any search result to calculate. Some DFS or BFS solvable problems does not have such thing(they mostly does not apply 2 at first), and such "boundry" must be definite all the time(means you can find it with computer, not your brain).
Then, follow these steps:  
1. Write the normal search solution for the question - and be sure that no additional factors that is not included in the argument is used - for example, the given example in "Brute-force solution" could use a list which stores current position, and updates every time the function is called again. Very hard does this make the program to be re-written as search with memorization. With these limitations, same outputs it will give you as you call the function with the same input.
2. Re-write the search procedure into memorization search. As the same it will be for the same argument given, a list that stores the return value can prevent redundant calculation. A quick tip that confirms the correctness of such list is that its dimension will be the same as the number of arguments.
3. See which data will be calculated first, i.e. which elements in memorization list will be filled first, i.e. which of them are dependent. Calculate them first, and then use them to calculate other elements that are dependent on them, then do the same thing. Graphically, this will give you the optimal answer starting from a certain node, and then all its ancestors.

## Something else to say
This is the simpliest dp example. There are many other varianced dp forms, e.g. probability dp. I don't know all of them for sure, but hope this gives you an introduction to dp, and mainly how to write one.
