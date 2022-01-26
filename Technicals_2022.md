# Technical interviews 2022
## Google 
### Random Bomb Board
You have a given `random()` function that takes in 2 integers, `low` and `high`. `random()` will output a random number within the range {`low`, `high`}.
Your task is to randomly generate bombs on a `nxn` game board. Given `k` bombs, design an algorithm that can place the `k` bombs on the board as randomized 
as possible.

**Brute Force**  
```
int random(int low, int high);


vector<vector<int>> generate_bomb(int k) {
  // initialize the board to all 0s
  // 0: empty
  // 1: bomb
  vector<vector<int>> board(n, vector<int> (n, 0));
  int n = board.size();
  
  while (k > 0) {
    int x = random(0, n-1);
    int y = random(0, n-1);
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
    swap(xy_arr[pair], xy_arr[n_xy]);
    n_xy--;
    k--;
  }
  
  return board;
}
```


## Hudson River Trading
### Vector
**How to implement a vector from scratch?**
```
private:
  // arr is the integer pointer which stores the address of our vector
  T* arr;
 
  // capacity is the total storage of the vector
  int max_capacity;
 
  // current is the number of elements currently present in the vector
  int current_capacity;
```

**How to deal with resizing?**
```
template <typename T> class vector
{
private:
	T* arr;
  int max_capacity;
  int current_capacity;
  
public:
	void push(T data) {
    // if the number of elements is equal to the capacity, that means we don't have space to accommodate more elements. 
    // We need to double the capacity.
    if (current_capacity == capacity) {
      // create an array with size that is 2 times the old array
      T* temp = new T[2 * max_capacity];
 
      // copying old array elements to new array
      for (int i = 0; i < max_capacity; i++) {
          temp[i] = arr[i];
      }
 
      // deleting previous array
      delete[] arr;
      max_capacity *= 2;
      arr = temp;
    }
 
    // Inserting data
    arr[current_capacity] = data;
    current_capacity++;
  }
```

**Why is adding an item in vector O(1) even if we resize it?**
Ha idk i didn't answer this question cuz im dumb

### Deque
**How to implement a deque?**   
**Implementation 1: Use doubly linked list**
Use front and rear pointers to keep track of the front and back of the list. This way enque and deque will be O(1).
```
Class Node {
	int data;
	Node* prev;
	Node* next;
}
Class deque {
	int size;
	Node* front;
	Node* rear;
}
```
However, we can't access intermediate elements in O(1)

**Implementation 2: Using circular array**
1. Creating an array of size n
    - Initialize 
        - Front = -1
        - Rear = 0
    - Insert one item:
        - Front = 0
        - Rear = 0
2. Inserting items
    - Front
        - First check if deque is full or not (size n)
        - If front == -1 or 0
            - Front = size-1  // basically the back of the array (has stuff) or 0 (no stuff)
        - Else 
            - Decrement front by one and push new item to arr[front]
    - End
        - If rear == size-1
            - Reinitialize rear = 0
        - Else 
            - Increment rear by 1 and push new item to arr[rear]
3. Deleting items
    - Front 
        - First check if deque is full or not (size n)
        - If only one element in deque
            - Front = 0  // basically move to the back of the array
        - Else 
            - Front = front + 1
    - End 
        - First check if deque is full or not (size n)
        - If only one element in deque
            - Rear = size-1  // basically move to the back of the array
        - Else 
            - Rear = rear-1

**Implementation 3: Use hash map**
The basic idea use a hash map with index as key and linked list nodes as the value. But I also didn't figure this one out lol.
