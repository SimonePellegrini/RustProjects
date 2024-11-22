# The 8 Queens Problem

## The problem
The 8 Queens Problem is a classic algorithmic puzzle in computer science and mathematics. The challenge is to place 8 queens on a 8x8 chessboard so that no two queens threaten each other. This means that no two queens can share the same raw, column or diagonal.

## The Hill Climbing algorithm
To solve this problem I used a heuristic search algorithm known as "Hill Climbing". In this algorithm, the execution starts from a random state of the problem and then iteratively moves toward a successor state that maximizes a given evaluation function until a maximum is found.

In the 8 Queens Problem, every possible configuration of the 8 queens (with a queen for each column) represents a state and the value to maximize is the negative value of the heuristic function $h(x)$ defined as "the number of queens threatening each other in the state x".
Given a possible board configuration, every other arrangement that has exactly 1 queen in a different position is a successor of that state. Clearly, our goal is to reach a state $s$ with $h(s)=0$.

One of the main problem that we have to address when implementing this algorithm is the fact that we could get stuck on a local maximum, without reaching a global maximum. For instance, we could reach a configuration $x$ with $h(x)=2$ where all its successors have a worse value of $h(x)$,in that case the algorithm would be unable to find a solution.
To face this issue we can use a technique called **random restart**. Specifically, if we encounter a local maximum, we can restart the algorithm from a new random state in the hope of eventually reaching a global maximum.

## The code
```rust
use rand::Rng;

fn main() {   
    //a board is represented as an array of 8 integers,
    //each one representing the row position of a queen in the i-th column
    let mut rng = rand::thread_rng();
    //generate a random board
    let mut board: [i8;8] = [rng.gen_range(0..8),rng.gen_range(0..8),rng.gen_range(0..8),rng.gen_range(0..8),rng.gen_range(0..8),rng.gen_range(0..8),rng.gen_range(0..8),rng.gen_range(0..8)];
    let mut prev = h(&board);

    //while h(x)>0 search for a better successor
    while h(&board) != 0{
        //check for a local maximum
        if h(&board) == prev{
            //Perform a random restart
            board = [rng.gen_range(0..8),rng.gen_range(0..8),rng.gen_range(0..8),rng.gen_range(0..8),rng.gen_range(0..8),rng.gen_range(0..8),rng.gen_range(0..8),rng.gen_range(0..8)];
        }
        else{
            prev = h(&board);
        }
        board = best_successor(board);
    }
    //plot the result
    plot(board);
}

```

```rust
//Given a state x, return its value h(x)
fn h(arr: &[i8])->u8{
    let mut conflicts = 0;
    
    // Iterate over all pairs of queens to check for conflicts.
    for x in 0..8{
        for y in x+1..8{
            //if two queens share the same row or diagonal, then increment the number of conflicts
            if arr[x] == arr[y] || (arr[x]-arr[y]).abs() == (x as i8 - y as i8).abs(){
                conflicts = conflicts+1;
            }
        }
    } 
    
    //return the number of conflicts
    conflicts
}
```

```rust
//takes a chessboard as input and outputs the best successor
fn best_successor(mut arr: [i8;8]) -> [i8;8]{

    let mut best_successor = arr;
    let mut original_number : i8;
    let mut h_successor = h(&arr);
    
    //iterate over all possible successors (56 in total) 
    //pick the one with the lowest value of h(x)
    for x in 0..8{
        original_number = arr[x];
        for y in 0..8{
            arr[x] = y;
            if h(&arr) < h_successor{
                h_successor = h(&arr);
                best_successor = arr;
            } 
        }
        arr[x] = original_number;
    }

    //return the best successor possible
    best_successor
}
```
```rust
//takes a chessboard as input and plots it on the terminal
fn plot(arr: [i8;8]){
    println!("    +-------------------------+");
    for i in arr{
        print!("    | ");
        for _ in 0..i{
            print!("⚪ ");
        }
        print!("👑 ");
        for _ in i+1..8{
            print!("⚪ ");
        }
        println!("|");
    }
    println!("    +-------------------------+")
}
```

## Result
Here's a screenshot of the result, hope you enjoy it!

![image](https://github.com/user-attachments/assets/3dd8a7f5-0094-4fe9-be96-2307a9f49676)

