---
title: "Exploring go pkg - time"
slug: "exploring-go-pkg-time"
tags: 
    - go
    - pkg
    - time
date: 2021-09-12T18:29:08+05:30
draft: false
---
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

    Returns a channel that will send the current time (t+d) where d is time duration to wait.

    **Example**: We will spawn a goroutine that will `doSomething`. If the `doSomething` takes more than 5 second to complete, we will get current time from `time.After` and will break out of the loop.

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
######
- ### func ParseDuration(s string) (Duration, error)

    Parses the duration from string. Valid time units are 'ns', 'us' (or 'µs'), 'ms', 's', 'm', 'h'. 

    **Example**: We will parse the duration from string.
    ```go
        func main() {
            complex, _ := time.ParseDuration("1h10m10s")
            fmt.Printf("There are %.0f seconds in %v.\n", complex.Seconds(), complex)
        }
    ```
######
- ### func (d Duration) Round(m Duration) Duration

    Rounds the duration to the nearest multiple of m.

    **Example**: We will round the duration to the nearest durations.
    ```go
        func main() {
            d, _ := time.ParseDuration("1h15m30.918273645s")
            
            // We are rounding towards nearest second. So, "30.918273645s" becomes "31".
            fmt.Printf("d.Round(%6s) = %s\n", r, d.Round(time.Second).String()) // d.Round(1s) = 1h15m31s

            // We are rounding towards nearest hour. So, "1h15m30.918273645s" becomes "1h00m00s".
            fmt.Printf("d.Round(%6s) = %s\n", r, d.Round(time.Hour).String()) // d.Round(1h0m0s) = 1h0m0s
        }
    ```

######
- ### func (d Duration) Truncate(m Duration) Duration

    Truncates the duration to the nearest multiple of m. Similar to round up, truncate will round down to nearest multiple.

    **Example**: We will truncate the duration to the nearest durations.
    ```go
        func main() {
            d, _ := time.ParseDuration("1h15m30.918273645s")
            
            // We are truncating towards nearest second. So, "30.918273645s" becomes "30".
            fmt.Printf("d.Truncate(%6s) = %s\n", r, d.Truncate(time.Second).String()) // d.Truncate(1s) = 1h15m30s

            // We are truncating towards nearest hour. So, "1h15m30.918273645s" becomes "1h00m00s".
            fmt.Printf("d.Truncate(%6s) = %s\n", r, d.Truncate(time.Hour).String()) // d.Truncate(1h0m0s) = 1h0m0s
        }
    ```

######
- ### func NewTicker(d Duration) *Ticker

    Returns a new Ticker containing a channel that will send the time with a period specified by d. 
    - `d` must be greater than 0, otherwise ticker will panic
    - To release resources, we need to stop the ticker using `<ticker_instance>.Stop()`

    **Example**:
    ```go
        func main() {
            
            ticker := time.NewTicker(1 * time.Minute)
            defer ticker.Stop()
            go func() {
                for {
                    select {
                    case <-ticker.C:
                        go doSomething()
                    }
                }
            }()
        }
    ```

######
- ### func NewTimer(d Duration) *Timer

    The Timer type represents a single event. When the Timer expires, the current time will be sent on C

    **Example**:
    ```go
        func main(){
            timer := time.NewTimer(1 * time.Minute)
            ch := make(chan bool)
            go func(ch chan bool) {
                _ = <-timer.C
                ch <- true
            }(ch)
            
            OUTER:
                for {
                    select {
                        case <-ch:
                            fmt.Println("1 minute done")
                            break OUTER
                    }
                }
        }
    ```



######
## Some important pointers of Time struct:
- A Time represents an instant in time with nanosecond precision. 
- Programs using times should typically store and pass them as values, not pointers. That is, time variables and struct fields should be of type time.Time, not *time.Time. 
- A Time value can be used by multiple goroutines simultaneously except that the methods GobDecode, UnmarshalBinary, UnmarshalJSON and UnmarshalText are not concurrency-safe. 
- Time instants can be compared using the Before, After, and Equal methods.
- (PARSING) For layouts specifying the two-digit year 06, a value NN >= 69 will be treated as 19NN and a value NN < 69 will be treated as 20NN. 