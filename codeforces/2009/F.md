# CodeForces contest 2008 (Div3) 第 G 題

問題係[呢度](https://codeforces.com/contest/2009/problem/F)

今日有個印度人問我題目，冇得用中文，好吧，用英文。

問題冇咩難度，只係分享吓咁樣寫 comment 嘅樂趣。

Commented code 係呢度。

```py
from sys import stdin                                                               # https://codeforces.com/contest/2009/problem/F
                                                                                    #
def run(arr, queries):                                                              #
    n = len(arr)                                                                    # Build the prefix sums for the array, sums[k] = sum(i = 0 to k exclusive) arr[i]
    s = 0                                                                           # The prefix sum is all the preprocessing we needed
    sums = [0]                                                                      #
    for v in arr:                                                                   #
        s += v                                                                      #
        sums.append(s)                                                              #
    for (l, r) in queries:                                                          # For each query
        l -= 1                                                                      # Translate the query into the normal, 0-based, array indexes
        r -= 1                                                                      #
                                                                                    #
        l_shift = l // n                                                            # l_shift is the number of complete cyclic shifts before l, it is also the number of times the array shifted in the current cyclic shift containing b[l], 
                                                                                    # This is slightly different from the problem statement, arr is the arr shifted 0 time (but problem statement call it the 1st cyclic shift)
                                                                                    #
        l_index = l % n                                                             # l_index is the index of the b[l] counted starting at 0 from its containing cyclic shift.
                                                                                    #
                                                                                    # Now we wanted to compute the sum b[0, l), starting with inside the cyclic shift contained b[l] first
        if l_index == 0:                                                            # In case l_index is 0, b[0, l) contains nothing in the cyclic shift containing b[l], so that part is 0
            prefix_discount = 0                                                     #
                                                                                    #
                                                                                    # Looking more closely into the cyclic shift block containing b[l], denote l_shift by s, the block is
                                                                                    #
                                                                                    #  arr[s], arr[s+1], ... arr[n-1], arr[0], arr[1], ... arr[s-1]    <- This is array index
                                                                                    # |                              |                             |   <- This is l_index
                                                                                    #
                                                                                    # 
        elif l_index + l_shift > n:                                                 # Suppose l_index is on the right hand side part, then l_index - length of the left hand side = l_index - (n - s) = l_index + s - n is the array index
                                                                                    # 
            prefix_discount = sums[l_index + l_shift - n] + sums[n] - sums[l_shift] # We compute the sum(arr[s, n)) + sum(arr[0, l_index + s - n]) by sum(arr[0, n)) - sum(arr[0, s)) + sum(arr[0, l_index + s - n])
        else:                                                                       # Suppose l_index is on the left hand side part, then l_index + s is the array index
                                                                                    # 
            prefix_discount = sums[l_index + l_shift] - sums[l_shift]               # We compute the sum(arr[s, l_index + s)) by sum(arr[0, l_index + s) - sum(arr[0, s))
        prefix_discount += sums[n] * l_shift                                        # Last but not least, we include the parts before the containing cyclic shift, the sum is obvious there, every cyclic shift has the same sum, and you just multiply the count of them.
                                                                                    #
        r_shift = r // n                                                            # The right hand side is basically the mirror of the left hand side.
        r_index = r % n                                                             #
                                                                                    #
        r_index += 1                                                                # The make r_index exclusive of the query, so become inclusive in the suffix part
        if r_index == 0:                                                            #
            suffix_discount = 0                                                     #
        elif r_index + r_shift > n:                                                 #
            suffix_discount = sums[r_index + r_shift - n] + sums[n] - sums[r_shift] #
        else:                                                                       #
            suffix_discount = sums[r_index + r_shift] - sums[r_shift]               #
        suffix_discount = sums[n] - suffix_discount                                 #
        suffix_discount += sums[n] * (n - r_shift - 1)                              #
                                                                                    #
        print(sums[n] * n - prefix_discount - suffix_discount)                      # Finally, the result is the sum of the full array b minus both the left hand side and right hand side.
                                                                                    #
def main():                                                                         #
    data = []                                                                       #
    for line in stdin:                                                              #
        data.append(line.strip())                                                   # Read all lines from the input to data
    num_test_case = int(data[0])                                                    # Read number of test cases
    line_number = 1                                                                 #
    for _ in range(num_test_case):                                                  #
        n, q  = [int(x) for x in data[line_number].split()]                         # Read the n q line
        line_number += 1                                                            #
        arr = [int(x) for x in data[line_number].split()]                           # Read the array
        line_number += 1                                                            #
        queries = []                                                                #
        for _ in range(q):                                                          # For each query
            l, r  = [int(x) for x in data[line_number].split()]                     # Read the l r line
            line_number += 1                                                        #
            queries.append((l, r))                                                  #
        run(arr, queries)                                                           # Solve the test case
                                                                                    #
if __name__ == "__main__":                                                          #
    main()                                                                          #
```