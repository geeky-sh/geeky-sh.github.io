---
layout: post
title: Learning Elixir I
description: Go through some of the useful concepts in Elixir
---

I really like the new concepts that I came across while learning [Elixir](https://elixir-lang.org/) programming language. I have a python / ruby background and elixir was the first flavor of functional languaage that I learnt. I would like to list all my learnings over here so that this can be the sole article I can refer to if I want to quickly read up elixir basics.

And, I will be taking the approach of learning by example so that it is very clear and easy to understand and remember.

## Recursion

I am writing few variances of recursion over here. This will primarily help in understanding:
1. How to convert and iterative solution to a recursive one.
2. How to write an efficient recursive function

### To understand how to convert `for` loop into the recusive function

Problem - Iterate over first 100 numbers and calculate it’s sum:

```elixir
defmodule Mod do
    def _calc_sum([head | tail], acc), do: _calc_sum(tail, acc + head)
    def _calc_sum([], acc), do: acc
    def calc_sum() do
        nums = Enum.to_list 0..100
        _calc_sum(nums, 0)
    end
end
```

#### Steps of conversion:
- Create a separate function for `for` loop.  (In the above example, it is `_calc_sum`)
- One of the arguments in the function is the list that needs to be iterated
- All the other variables that remain common across the for loop should be taken as rest of the arguments in the function.

#### Approach 2:

Problem - Given a list of strings, return a list of it’s uppercase version

```elixir
defmodule Test do
    def _to_upcase([head | tail]), do: [String.upcase(head) | _to_upcase(tail)]
    def _to_upcase([]), do: []
    def to_upcase(llist) do
        _to_upcase(llist)
    end
end
```

### To understand how to iterate a list recursively in elixir

There are two ways to iterate a list in elixir:

```elixir
defmodule Test do
    @doc """
    - Preserves the order of the list
    - No additional variable required
    """
    def approach_1([head | tail], func), do: [func.(head) | approach_1(tail)]
    def approach_1([], _func), do: []

    @doc """
    - Reverse the resultant list
    - Additional variable required
    - Additional function that takes the additional variable required
    """
    def approach_2(list, func), do: _approach_2(list, func, [])
    def _approach_2([head | tail], func, result), do: _approach_2(tail, func, [func.(head) | result])
    def _approach_2([], _func, result), do: result
end
```

### To understand difference between inefficient and efficient (tail) recursion

```elixir
defmodule TestRecursion do
    def inefficient([head | tail]), do: head + inefficient(tail)
    def inefficient([]), do: 0

    def efficient_tail_recursion(list), do: do_tail_recursion(list, 0)
    def do_tail_recursion([head | tail], acc), do: do_tail_recursion(tail, acc + sum)
    def do_tail_recursion([], acc), do: acc
end
```

#### Info:

- The `inefficient` function adds stacktrace of the calls recursively to compute the final output.
- The efficient tail recursion doesn’t add stacktrace. Instead it just calls same function again. Internally, it just shifts the pointer to the beginning of the function. This doesn’t involve any overhead.
- To remember easily, consider the amount of memory one will need to have to execute `inefficient` function while for the `efficient` function it just needs to call the function recursively. The function takes the responsibility of managing state.
- The efficient approach is called **Tail Recursion**. To know more about this, one can go [here](https://www.geeksforgeeks.org/tail-recursion/)

## Functions

### Anonymous functions

How to define:

```elixir
a = fn x -> x*x end # approach 1
b = & (&1 * &1) # approach 2

# call
a.(2)
b.(3)
```

#### Info:

- Whenever it comes to passing functions in functions, anonymous functions are used, since they can be passed around just like variables
- This is why collection library like `Enum.map` / `Enum.reduce` take anonymous functions as second arguments.
- Unlike python, in elixir, functions are not first class citizen. If we want to treat them as on we have to convert them to anonymous functions

Pattern matching can also be applied in function signatures in anonymous functions


```elixir
func = fn
    {:ok, result} -> IO.puts("Everything seems ok")
    {:error, result} -> IO.puts("There is some error")
    _ -> IO.puts("What! This has never happened before")
end
```

#### Converting normal function to an anonymous function

```elixir
defmodule Test do
    def square(x), do: x*x
end

# normal function call
Test.square(2)
# convert to anonymous function call, approach 1
a = fn x -> Test.square(x) end
# approach 2, convert to anonymous function call
a = & (Test.square(&1))
# approach 3, convert to anonymous function call
a = & (Test.square/1)
```

### Magic of functions

Multiple ways of writing or shortening or pattern matching in a function


```elixir
defmodule TestF do
    def square(x) do
        x * x
    end

    def sum(a, b), do: a + b

    def check_cond(a, b) when a + b == 11, do: IO.puts("Sum is 11")
    def check_cond(_a, _b), do: IO.puts("Something else")

    def check_pat({:ok, _content} = result), do: IO.inspect(result, label: "input is ok")
    def check_pat({:error, _content} = result), do: IO.inspect(result, label: "Input is error")
    def check_pat(result), do: IO.inspect(result, label: "Something else entirely")

    def check_list([2 | _tail] = result), do: IO.inspect(result, label: "Starts with 2")
    def check_list([1, 2, 3 | _tail] = result), do: IO.inspect(result, label: "Starts with 1, 2, 3")
    def check_list(result), do: IO.inspect(result, label: "Something else entirely")
end
```

## Conditionals and Iterators:
-  `case` . It is used to compare values against many patterns.


```elixir
defmodule Test do
    def case_check() do
        case File.open("value.exs") do
            {:ok, _fp} -> IO.puts("file exists and openend")
            {:error, _other} -> IO.puts("file does not exist")
            _ -> IO.puts("something strange has happened")
        end
    end
end
```

- `cond` . It is used to check different conditions


```elixir
defmodule Test do
    def condition_check(a, b) do
        cond do
            a + b == 5 -> IO.puts("sum is 5")
            a * b == 121 -> IO.puts("both values are 11")
            true -> IO.puts("Something else entirely")
        end
    end
end
```

- `if` statement


```elixir
defmodule TestIf do
    def approach_1(a, b) do
    if a + b == 5 do
        IO.puts("sum is 5")
    else
        IO.puts("sum is not 5")
    end
    end

    def approach_2(a, b) do
        if a + b == 5, do: IO.puts("sum: 5"), else: IO.puts("Not sum: 5")
    end
end
```

- `for` loop


```elixir
defmodule TestFor do
    def square(list) do
        for x <- list, do: x*x
    end
end
```

## Working with strings, charlist and binaries

### Info:

- String are binaries internally
- Each character in the string is represented by at least 1 byte.
- To get each character in the string we can do:
    - `String.graphemes(str)`
    - `String.charlist(str)`
- Representation:
    - `<<121>> # binary representation`
    - `<<121::8>> == <<121>> # each code point is represented by 8 bits`
    - `<<3::2, 4::4, 3::2>> == <<211>> # :: represent no of bits to be used`
    - `<<head, rest::binary>> = "banana``"`


## Use agent to store state

Create a cache store to keep values

```elixir
defmodule CacheStore do
    use Agent
    def start_link() do
        Agent.start_link(fn -> %{} end, name: __MODULE__)
    end
    def get(key) do
        Agent.get(__MODULE__, fn map -> Map.get(map, key) end)
    end
    def put(key, value) do
        Agent.update(__MODULE__, fn map -> Map.put(map, key, value) end)
    end
    def get_name(), do: __MODULE__
    def get_all() do
        Agent.get(__MODULE__, fn map -> map end)
    end
end
```

## Other Important Info
- Lists are internally represented as linked lists and they are not the same as array. So, going to the nth element of the linked list is not O(1) time













