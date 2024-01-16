# Introduction
## Goals
- Confidence to write small programs correctly (up to a couple thousand lines of code)
- Knowledge of data structures and algorithms
- Experience with C
- Systematic approach to solving problems
- Good time management
### Programming Skills
- Transforming algorithmic ideas into code
	- Code that works the first time around (deliberate programming)
	- Testing code to ensure no bugs
- Imperative programming in C and C0
- Basic unix skills
### Algorithms
- Asymptotic complexity
	- Time/space
	- Worst case/average case/amortized analysis
### Computational Thinking
- Systematic approach to solving a problem
- Finding solutions that are correct
- Finding solutions that are efficient
### Technical Comprehension
- Transforming technical specification into code
	- Problem statements will get longer throughout the course
	- Dots to be connected will be further apart
	- Will become more confident
	- Will try more things independently
## Logistics
### Lectures
- Lectures are Tuesdays and Thursdays
- Be active
	- Ask/answer questions, pay attention
	- Lecture notes, slides, and online modules for review
- Laptops for note-taking
### Labs and Recitations
- Labs Monday (programming practice)
- Recitations Friday (conceptual practice)
- These are intended to be very collaborative
### Getting Started
- Laptop setup office hours
	- Set up the C0 tools with Andrew Linux
- Wednesday, 6 to 7 pm in TBA
- Linux workshop at Thursday, 4:30 to 6:30 in TBA
### Online Resources
- https://c0.cs.cmu.edu/docs/c0-reference.pdf
### Online Communication
- __Diderot__ for announcements, questions, and communications with course staff
- __Autolab__ and __Gradescope__ for homework
- Grades from course home page
- Cluster linux machines and SSH to shared machines for assignments
### Help
- Office hours calendar on course web page
- Bootcamps run by TAs will be announced throughout the semester for extra help
- Student Academic Success Center support
	- Supplemental Instruction: TBA, TBA
	- Peer Tutoring
### Grading
- 50% - exams (two midterms and a final)
- 45% - Weekly Homework
	- Written due Monday by 9pm on Gradescope
		- Written 1 is already out
	- Programming due Thursdays 9pm on Autolab
- 5% - In-class activities and labs
### Academic Integrity (commit many violations)
- OK to discuss course material, practice problems, blank assignments, study sessions, handed-back homework
- NOT OK to discuss homework answers and sharing code
### AI Assistance
- ChatGPT is a dipshit
- It's not good for learning
- AIV violation
## How to be Successful
- Don't stress over grades (cap)
- Participate
- Manage time wisely
- Start homework early
- Get help
- Make time for fun (impossible)
# Contracts

We take an unknown function written in C0, a memory safe subset of C:

```c
int f(int x, int y) {
	int r = 1;
	while (y > 1) {
		if (y % 2 == 1) {
			r = x * r;
		}
		x = x * x;
		y = y / 2;
	}
	return r * x;
}
```

We test a combination of inputs and outputs

| x | y | f(x, y) |
| ---: | :--: | :--- |
| 2 | 4 | 16 |
| 3 | 2 | 9 |
| 1 | 1 | 1 |
| 5 | 1 | 5 |
| 2 | 3 | 8 |
| 2 | 0 | 2 |
| 2 | -1 | 2 |

It appears the function evaluates $f(x,y)=x^y$ for positive values of $x$ and $y$, but is incorrect on negative and zero values.

We can define $x^{y}$ as:
$$x\times x\times\ldots\times x$$
As an alternative recursive formulation we can define $x^y$ as
$$f(x,y)=\begin{cases}1&y=0 \\ x\cdot x^{y-1}&y>0\end{cases}$$
Note the power function is undefined for negative values of $y$. 
### Preconditions

In C0 we can define a *precondition* to ensure a condition is true at the start of the function like so:

```c
int f(int x, int y) 
// @requires y >= 0;
{
	int r = 1;
	while (y > 1) {
		if (y % 2 == 1) {
			r = x * r;
		}
		x = x * x;
		y = y / 2;
	}
	return r * x;
}
```

It is said to be *unsafe* if we call a function with parameters that violate the precondition. If we call coin (the C0 compiler) with the `-d` flag, execution aborts on an error, but without `-d` the function returns an incorrect result.
### Postconditions

In comparison with preconditions which are checked before the function starts executing, postconditions are checked after the function is executed to test the results. A postcondition can be written like so:

```c
int f(int x, int y) 
// @requires y >= 0;
// @ensures \result == x**y;
{
	int r = 1;
	while (y > 1) {
		if (y % 2 == 1) {
			r = x * r;
		}
		x = x * x;
		y = y / 2;
	}
	return r * x;
}
```

Note `\result` is used in place of the value returned by the function. There is no built-in power function to C0 (so this code won't compile), so we need to add our own:

```c
int POW(int x, int y)
@requires y >= 0
{
	if (y == 0) return 1;
	return POW(x, y - 1) * x;
}

int f(int x, int y) 
// @requires y >= 0;
// @ensures \result == POW(x, y);
{
	int r = 1;
	while (y > 1) {
		if (y % 2 == 1) {
			r = x * r;
		}
		x = x * x;
		y = y / 2;
	}
	return r * x;
}
```

This code still throws an error! As $x$ is modified throughout the function, we can't use it in the postcondition. To avoid this, we create another variable to hold the modified value of $x$ so we don't change the original parameter.

```c
int POW(int x, int y)
@requires y >= 0
{
	if (y == 0) return 1;
	return POW(x, y - 1) * x;
}

int f(int x, int y) 
// @requires y >= 0;
// @ensures \result == POW(x, y);
{
	int b = x;
	int e = y;
	int r = 1;
	while (e > 1) {
		if (e % 2 == 1) {
			r = b * r;
		}
		b = b * b;
		e = e / 2;
	}
	return r * b;
}
```

This code functions correctly by throwing an error on cases where this power function is incorrect.
### Correctness

If a call violates a function's postconditions, the function is written incorrectly (assuming the preconditions were met). We can mathematically analyze functions to prove they return the correct result.
- If the preconditions are violated it's the caller's fault.
- If the postconditions are violated it's the implementation's fault.
#### Analyzing `POW`

The body of `POW` calls `POW`, how do we ensure this is safe? We can analyze it like so:
1. `// @requires y >= 0` ensures that `y` is greater than zero.
2. We divide into cases
	1. `y == 0`: return 
	2. `y >= 1`: it follows `y - 1 >= 0` which guarantees the internal call to `POW` will not violate its preconditions.

#### Abstraction

The only thing a caller should need to know is the function's name, preconditions, and postconditions. For the `f` function this would be:

```c
int f(int x, int y)
//@requires y >= 0;
//@ensures \result == POW(x, y);
```

This is essential to abstract away internal details of how a function works into just what it's supposed to do. This is important to make code readable and easy to understand.
#### Analyzing `f`

The complicated part of `f` is the loop, it's unclear what is happening at each iteration or even how many iterations there are. We can use *loop invariants* to write conditions that must be satisfied at every loop iteration.

We can find a loop invariant by tracing the code through an example call. We will run `f` on the sample inputs `(2,8)`.

| b | e | r | b^e |
| :--: | :--: | :--: | ---- |
| 2 | 8 | 1 | 256 |
| 4 | 4 | 1 | 256 |
| 16 | 2 | 1 | 256 |
| 256 | 1 | 1 | 256 |

Is this always true, what if we test on more sample inputs?
