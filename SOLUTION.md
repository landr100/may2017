Note: this programming assignment was sent via a tar.gz file and so my solution is also provided in that format. I looked but was not able to find the repo at https://github.com/nytm/messaging-interview

## Summary

A program written in Go, "RobinGowin2017.go", has been developed.
The goal is to meet all the requirements specified in README.md.
This document provides a summary and some notes.

SOLUTION.md
Author: Robin Gowin
Date: May 21, 2017
nytimes.com interview submission

## Design

For simplicity of this programming assignment, certain decisions were made that would probably be done differently
if this code was part of a production development repo.
- One main program contains all the components
- A small number of global variables are used
- Automated testing is limited
- Logging is minimized
- Assumptions are made about memory and size of the input file based on the data provided
- The most complex code is the recursive function that searches for possible solutions and returns the first one found.

## Implementation

The only external dependency is a library from github.com/gorilla/mux which is open source, i.e. not "for-pay software".
The main program initializes the REST API "router" and defines the three required endpoints, and then listens on port 8080.
The endpoint "/healthcheck" prints a JSON object to the user; this endpoint can be called at any time.
The endpoint "/dictionary" allows the user to pass in a file with the dictionary words; this should be invoked first; 
calling this endpoint updates a map which is used as the global dictionary.
The endpoint "/split" allows the user to pass in the concatenated list of words; it must be called after the user provides the dictionary.
This function calls a search function which iterates over the provided input, and returns when the first match is discovered.
There is a limit provided so that the recursion does not loop forever; the idea of "when to stop trying" is based on iteration.
If the input provided does not have any combination of words in the dictionary, a simple message like "search for ... did not return results" is displayed.
Most likely the algorithm runs in O(N\*\*2) time so that could be used as a bound for termination if the input has no matches.
Any endpoint besides the ones listed above will result in a 404 message (page not found).

## Verification

How to test (version 1):

## NOTES

- start the service in one tab: go run RobinGowin2017.go
- verify health check works properly: open http://localhost:8080/dictionary and confirm the expected status message is displayed
- store the dictionary:
    curl -i -X POST -T messaging-interview/words.txt -H "Content-Type: text/plain"  http://localhost:8080/dictionary
- send the provided words file:
    curl -X POST -T messaging-interview/concatenated.txt -H "Content-Type: text/plain"  http://localhost:8080/split
- confirm the output is as expected:
    curl -X POST -T messaging-interview/concatenated.txt -H "Content-Type: text/plain"  http://localhost:8080/split | tr -d '\n'  >out.txt
    diff out.txt messaging-interview/concatenated.txt # should return no differences

