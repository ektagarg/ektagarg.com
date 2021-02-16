---
title: "Profiling and optimizing GO [PART-1]"
date: 2021-01-03T11:58:58+05:30
description: "Let's create a slack bot to get daily stats for your website."
tags: [Go, Profiling, Optimization]
categories: Go
comment : true
draft: true
---
### Profiling
A dynamic program analysis that measures space, time complexity, frequency and duration of function calls, usage of instructions.

Profiling is achieved by instrumenting either the program source code or its binary executable form using a tool called a profiler (or code profiler). Profilers may use a number of different techniques, such as event-based, statistical, instrumented, and simulation methods.

## Profiling in Go
One of the great things about Go is that it has really rich profiling tools included.
- CPU profiling (stack sampling) : CPU profiling uses stack sampling which basically means that every so often it will look at what's running in the CPU and record the full call stack.
- Heap profiling : It means that small percenatge of all of your allocation are profiled and tells exactly what happened on a call stack at that point.
- Block profiling
- Tracing

All of this is built into [pprof](https://golang.org/pkg/net/http/pprof/) and [runtime](https://golang.org/pkg/runtime/) packages.

Requirements:
For profiling, first you need to create a profile. What that means is that you need to record what's happening and record a binary file and there's a few ways you can do that:

1. Tests and benchmarks: When running go tests, you can pass -cpuprofile,  -memprofile flags and that generates a profile. 

2. Import [pprof](https://golang.org/pkg/net/http/pprof/) package: Importing a blank pprof package will add a few endpoints in your service. Once you have it installed you can directly run:
go tool pprof -seconds 5 http://localhost:8080/debug/pprof/profile

3. Another option, which is rarely used is start the profile from your code by calling runtime.StartCPUProfile and runtime.WriteHeapProfile.

Once you have created those profiles, you can analyze them using `go tool pprof`