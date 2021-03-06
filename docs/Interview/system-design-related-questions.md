# System Design Questions

## Algorithm Problem: External Storage

### Q1.1: Merge k **sorted** arrays

Duplication: input two arrays can have duplications, but output array cannot.

#### Analysis

Use a `priority_queue` to keep track of the smallest value for each array. Use a `pair<int,int>` to represent **which array** and **which element** from the array.

This solution will read and write each element **once** from the disk  (because we are using a pointer to point each array).


Assume the average size of each array is **n**, then the time complexity is $O(k \times log_2{k} \times N)$ 

+ Other Common Solution
  1. Iterative Reduction: sort first two O(n) -> first three O(2n) -> ... -> all K O(kn): Time $O(k^2 \times n)$ + Space $O(k \times n)$ because of the aux array
  2. Binary Reduction: merge 1 & 2, merge 3 & 4 ... merge k - 1 & k. -> merge 12 & 34 ... -> merge k-3k-2 & k-1k: Time $O(k \times n \times log_2{k})$: $log_2{k}$: merge ops, for each merge it takes n to merge and there are k arrays + Space $O(k \times n)$. This solution has the same compleixty as the `priority_queue` solution, however if the array size is too big, then we need to read and write multiple times.

#### Code

```c
vector<int> merge(vector<vector<int>> nums) {
    // write your solution here
    typedef pair<int, int> PII;
    int m = nums.size();

    auto cmp = [&](const PII& l, const PII& r) {
      return nums[l.first][l.second] > nums[r.first][r.second];
    };
    priority_queue<PII, vector<PII>, decltype(cmp)> pq(cmp);
    for (int i = 0; i < m; ++i) {
      if (!nums[i].empty())
        pq.push(make_pair(i, 0));
    }
    vector<int> res;
    while (!pq.empty()) {
      int r, c;
      tie(r, c) = pq.top();
      pq.pop();
      res.push_back(nums[r][c]);
      if (c + 1 < nums[r].size()) pq.push(make_pair(r, c + 1));
    }
    return res;
  }
```

### Q1.2: External Sort 1

Given a simple computer with **one** core CPU, which has **2GB** memory and **1GB** avaliable to used, it also has **two 100GB** Hard Disk, how to sort **80GB** integer of 64 bits?


1. make 320 paritions for the 80GB data, so that each parition has 0.25 GB size (which is less than 1 GB, and we can use the additional free space to do other things, such as running our algorithm). 

2. maintain a min-heap (320 partition files) with fix size to sort externally. 

- Below are quoted from [wikipedia](https://en.wikipedia.org/wiki/External_sorting#:~:text=External%20sorting%20is%20a%20class,usually%20a%20hard%20disk%20drive.)
```
One example of external sorting is the external merge sort algorithm, which is a K-way merge algorithm. It sorts chunks that each fit in RAM, then merges the sorted chunks together.

The algorithm first sorts M items at a time and puts the sorted lists back into external memory. It then recursively does a M/B-way merge on those sorted lists. To do this merge, B elements from each sorted list are loaded into internal memory, and the minimum is repeatedly outputted.

For example, for sorting 900 megabytes of data using only 100 megabytes of RAM:

Read 100 MB of the data in main memory and sort by some conventional method, like quicksort.
Write the sorted data to disk.
Repeat steps 1 and 2 until all of the data is in sorted 100 MB chunks (there are 900MB / 100MB = 9 chunks), which now need to be merged into one single output file.
Read the first 10 MB (= 100MB / (9 chunks + 1)) of each sorted chunk into input buffers in main memory and allocate the remaining 10 MB for an output buffer. (In practice, it might provide better performance to make the output buffer larger and the input buffers slightly smaller.)
Perform a 9-way merge and store the result in the output buffer. Whenever the output buffer fills, write it to the final sorted file and empty it. Whenever any of the 9 input buffers empties, fill it with the next 10 MB of its associated 100 MB sorted chunk until no more data from the chunk is available. This is the key step that makes external merge sort work externally -- because the merge algorithm only makes one pass sequentially through each of the chunks, each chunk does not have to be loaded completely; rather, sequential parts of the chunk can be loaded as needed.
Historically, instead of a sort, sometimes a replacement-selection algorithm was used to perform the initial distribution, to produce on average half as many output chunks of double the length.
```

### Q1.3: External Sort 2
#### LC Followup for **Contains Duplicate II**

[link](https://leetcode.com/problems/intersection-of-two-arrays-ii/discuss/82243/Solution-to-3rd-follow-up-question)

### Q1.4: 2Sum on 1TB data

1 GB memory vs 1TB data

#### Solution

use external sort to sort and apply two pointers.

assume duplicate exist.

If we cannot read the entire dataset to memory, then we need to know how to partition the list s.t. we can decrease the search range.


How to partition the list? ans: by doing the sampling of data to find the **pattern**


1. do sampling on HD to get the distribution of the values => let's say the distribution is even distributed
2. scan the HD (1TB data) and put data into correspondng range into a file. (-100-0, 1-100, 101-200...) assume now we have k files.
3. load each file into the memory, e.g. for a file of range [1, 100], the only possible other file should be in the range of [101, 200], so we only need to load these two files into the memory.__

### Q2.1 Find common elements in two arrays

#### Solutions

1. Solution 1 (assume sorted): use two pointer (i points to the start of array1, j points to the start of array2). Time: $O(m + n)$, Space: $O(1)$
2. Solution 2 (assume unsoted): Use a hashset to contain the array whose size is less, then check the other array to see if exist in the set. Time: $O(m + n)$, Space: $O(min(m,n))$
3. Solution 3 (assume sorted): use binary search on the large array. Time: $O(m \times log_2{n})$, Space: $O(1)$

### Q2.2 Find common elements in k sorted arrays

Assume the size of each array is approx equal to n

#### Solutions

1. Solution 1: iterative reduction -> do the search between 1, 2, use the commom elements to search the 3, do the same thing for 4... Time: $O(k \times n)$, Space: $O(n)$
2. Solution 2: binary reduction -> 1,2 common + 3,4 common + .... + (k - 1) + (k) common then merge, 

For implementation: [stackoverflow link](https://stackoverflow.com/a/44069571)



