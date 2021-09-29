---
title: "Debugging in golang"
tags: ["go", "dlv", "debugging"]
slug: "debugging-in-golang"
date: 2021-09-27T10:33:57+05:30
draft: false
---
![](/img/debugging.jpg)
###### <a href='https://www.freepik.com/photos/technology'>Technology photo created by pressfoto - www.freepik.com</a>

# Debugging in golang

We all know that debugging is a very important part of development process and sometimes it can become a nightmare. But how can we do it in golang?

> `delve` or `dlv` is a tool that can help us to debug our code. It lets us set breakpoints, get value of variables, step into functions, step over functions, step out of functions, run to a specific line, update the variable values etc.

To get started with `delve`, we need to install it.
```bash
$ git clone https://github.com/go-delve/delve
$ cd delve
$ go install github.com/go-delve/delve/cmd/dlv
```
>Note: You can refer to [installation docs](https://github.com/go-delve/delve/tree/master/Documentation/installation) if you face any issues.

Once we have delve installed, we can start debugging our code. To do so, run `dlv debug <FILENAME.go>`. Normally we will run `dlv debug main.go` to start debugging our code as shown below.

![](/img/11_2_dlv_cmd.png)

Now that we are running `dlv`, we can set breakpoints using `b <FILENAME.go>:LINE_NUMBER`. For example, to set a breakpoint at line number `5` in `main.go`, run `b ./main.go:5`.

![](/img/11_3_dlv_breakpoint.png)

Once the break point is set, we can start debugging. To start debugging, we can hit `c` and enter. Our application will start as it is, but will stop if breakpoint is hit.

#### Example:
```go
package main                    // Line 1
                                // Line 2
func main() {                   // Line 3
	var x, y, z int             // Line 4
	x, y = 1, 2                 // Line 5
	z = x + y                   // Line 6
	println(z)                  // Line 7
}                               // Line 8
```
For the above code, if we set breakpoint at `b ./main.go:5` and then hit `c`, the control will stop at line 5 as you can see below. 
- `n` will move the control to next line (i.e. line 6).
- `print <VARIABLE>` will print the value of variable. Eg. `print x` will print `1` and `print y` will print `2`.
- `n` will move the control to next line (i.e. line 7) which is println statement itself.
- `n` will move the control to next line (i.e. line 8). Before moving to next line, it will execute line 7 and print `3`.

![](/img/11_4_dlv_moving.png)


Now that we know how the control works and how we can navigate through lines, we can look at `step-in`. Sometimes we would like to step-in to a function call and iterate over, and then come out of the function. 

#### Example:
```go
package main                    // Line 1
								// Line 2
func add(x, y int) int {		// Line 3
	return x + y				// Line 4
}								// Line 5
func main() {                   // Line 6
	var x, y, z int             // Line 7
	x, y = 1, 2                 // Line 8
	z = add(x, y)               // Line 9
	println(z)                  // Line 10
}                               // Line 11
```

For the above code, if we set breakpoint at `b ./main.go:9` and then hit `c`, the control will stop at line 8 as we can see below. 
- `s` will move control to the `add` method
- `n` will move the control to line 4 and another `n` will move the control to line 5 and then it will exit the add function. The control will move back to line 9, but this time `variable z` will have the returned value.

![](/img/11_5_dlv_step.png)