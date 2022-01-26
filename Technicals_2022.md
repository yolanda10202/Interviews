# Technical interviews 2022
## Google 
### Random Bomb Board
You have a given `random()` function that takes in 2 integers, `low` and `high`. `random()` will output a random number within the range {`low`, `high`}.
Your task is to randomly generate bombs on a `nxn` game board. Given `k` bombs, design an algorithm that can place the `k` bombs on the board as randomized 
as possible.

**Brute Force:**  
```
int random(int low, int high);


vector<vector<int>> generate_bomb(int k) {
  // initialize the board to all 0s
  // 0: empty
  // 1: bomb
  vector<vector<int>> board(n, vector<int> (n, 0));
  int n = board.size();
  
  while (k > 0) {
    int x = random(0, n);
    int y = random(0, n);
    // make sure the position does not have a bomb already
    while (board[x][y] == 1) {
      x = random(0, n-1);
      y = random(0, n-1);
    }
    board[x][y] = 1;
    k--;
  }
  
  return board;
}
```
There is one problem with this brute force. We might keep hitting `(x,y)` that is taken already if `k` is large. How can we eliminate the redunancy?

**Optimized Version**  
Assume `k` is a big number (I think this solution is only really optimized if `k` is large). The idea is to randomly choose `(x,y)`s that are not taken already. 
If the pile of `(x,y)`s we are picking from does not contain any `(x,y)`s that have bombs, we don't need to worry bumping into one!

To do so, we need to create an array `xy_arr`. It will store the x and y pairs that are not taken yet. We initialize it so it has all the pairs. For
example, if `n = 3`, in the beginning, we have:
```
xy_arr = [[0,0],[0,1],[0,2],[1,0],[1,1],[1,2],[2,0],[2,1],[2,2]]
n_xy = 8; // the last index of xy_arr is 8
```
We will use the `random()` function to pick the index of `xy_arr`. The number generated from `random()` is like the key to find an avaliable pair of x and y. 

In our first iteration, we run `pair = random(0, n_xy)` (note `n_xy = 8`). Let's say `pair = 5`. This means we will use the pair store in `xy_arr[5]`, which is 
`[1,2]` in this case. So we assign `board[1][2] = 1`.  
After this, we need to swap `[1,2]` with the last item in `xy_arr` and decrement `n_xy` by 1. After the operation, we will have:
```
xy_arr = [[0,0],[0,1],[0,2],[1,0],[1,1],[2,2],[2,0],[2,1],[1,2]]
n_xy = 7;
```
Now, you can probably see, in the next iteration, we will run `pair = random(0, n_xy)` again. But this time, since `n_xy = 7`, we will never be able to pick the 
pair `[1,2]` anymore! This solves the issue of picking the same pair over and over again if `k` is large

```
int random(int low, int high);


vector<vector<int>> generate_bomb(int k) {
  // initialize the board to all 0s
  // 0: empty
  // 1: bomb
  vector<vector<int>> board(n, vector<int> (n, 0));
  int n = board.size();
  
  // xy_arr will keep track of avaliable (x,y) pairs
  vector<pair<int,int>> xy_arr;
  for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
      xy_arr.push_back(make_pair(i,j));
    }
  }
  // n_xy = pointer to the last item in xy_arr
  int n_xy = xy_arr.size()-1;
  
  while (k > 0) {
    int pair = random(0, n_xy);
    int x = xy_arr[pair].first;
    int y = xy_arr[pair].second;
    board[x][y] = 1;
    swap(xy_arr[pair], x_arr[n_xy]);
    n_xy--;
    k--;
  }
  
  return board;
}
```
