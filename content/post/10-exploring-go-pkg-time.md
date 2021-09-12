---
title: "Exploring go pkg - time"
slug: "exploring-go-pkg-time"
tags: ["go", "pkg", "time"]
date: 2021-09-12T18:29:08+05:30
draft: true
---

# Exploring go pkg - time

Go's time package provides a number of functions and types for working with dates and times. Go time formatting is different from other languages. The primary layouts used by the time package are described below. 

```go
    const (
        UnixDate    = "Mon Jan _2 15:04:05 MST 2006"
        RFC1123     = "Mon, 02 Jan 2006 15:04:05 MST"
        RFC1123Z    = "Mon, 02 Jan 2006 15:04:05 -0700" // RFC1123 with numeric zone
        RFC3339     = "2006-01-02T15:04:05Z07:00"
        RFC3339Nano = "2006-01-02T15:04:05.999999999Z07:00"
    )
```
You can define your own formats using the constants.

Personally, I prefer to use the `RFC3339` format. One should use the RFC822, RFC850, and RFC1123 formats with local times.

> #### INFO: time.Duration is the unit used to denote nanosecond. 

## Lets talk about some functions available in time pkg:

- ### func After(d Duration) <-chan Time

    Returns a channel that will send the time t+d after the time t where d is time duration to wait.

    **Example**: In the example below, we will spawn a goroutine that will `doSomething`. If the `doSomething` takes more than 5 second to complete, we will get current time from `time.After` and will break out of the loop.

    ```go
        func main() {
            c := make(chan int, 0)
            go doSomething(c)
            OUTER:
                for {
                    select {
                    case <-c:
                        fmt.Println("Work Done")
                        break OUTER // break the loop when work is done
                    case <-time.After(5 * time.Second):
                        fmt.Println("timed out")
                        break OUTER // break the loop when timed out
                    }	
                }
        }

        func doSomething(c chan int) {
            // Do some work which should finish in max 5 seconds
            time.Sleep(2 * time.Second)
            c <- 2
        }

    ```



- ### func ParseDuration(s string) (Duration, error)

    Parses the duration from string. Valid time units are 'ns', 'us' (or 'µs'), 'ms', 's', 'm', 'h'. 

    **Example**: In the example below, we will parse the duration from string.
    ```go
        func main() {
            complex, _ := time.ParseDuration("1h10m10s")
            fmt.Printf("There are %.0f seconds in %v.\n", complex.Seconds(), complex)
        }
    ```