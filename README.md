### Searching Tweets

#### Approaches
1. In order to search faster on larger dataset, I took advantage on the routine feature that Go provides. 
Currently, the program spawns at most 20 routines on large dataset, each of them searches a chunk of the 
tweets. And the main routine collects the result and get top 5 recent tweets from it.
2. The main routine uses a priority queue to get the top 5 recent routine.
3. In order to handle logical operand in the query, I implement a query handler. It takes in a query, separate 
it into logical operand and query strings. And convert it into reverse polish notation. And using it to filter
through the content to see if the content fits. It also handles the situation where no operand is present, then
it will fall back to old-fashioned traversal.
#### Benchmark
Running benchmark shows that the total time it takes to finish is 3.107s. While using only 1 routine is 4.372s.
The performance boost is nearly 30%.
#### Running tests
Just simply cd into haoran_zhang_project/src/hzhang directory and use command ``go test`` or 
``go test -bench=. index_test.go`` if you want to benchmark the program.
#### Design
The program assumes that the logical query is valid. Though it still provides some error handling, so that it will
not crash, just will think that no content fits with the query and return false.

The program assume the priority of operands is ! > & = |

Originally I only do topKRecent sort once for all the results returned. But to further
improve the performance, Now that each routine does its own topKRecent in parallel, so
that the results they returned has a bounded length, thus further reducing the time.

The program implement its own stack, which is not thread safe though since
each handler has its own stacks and is not necessary to be thread safe.
#### Complexity
For get topKRecent, the time complexity is O(nlogK), and since n is bounded to 
20(num of routines) * 5(num of result each routine returns), the time complexity in the
main routine is bounded to O(100log5), which is O(1).

For filter, the handler splits and traverses the query and do constant time calculation,
and it converts contents to a set of words to reduce look up time. Suppose the query
has a length of n and the content has a length of m, the total time complexity will be O(m + n).