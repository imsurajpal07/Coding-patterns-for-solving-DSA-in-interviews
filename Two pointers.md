# Pattern 2: Two Pointers

In problems where we deal with sorted arrays (or **LinkedList**s) and need to find a set of elements that fulfill certain constraints, the **two pointers** approach becomes quite useful. The set of elements could be a pair, a triplet or even a subarray. For example, take a look at the following problem:

> Given an array of sorted numbers and a target sum, find a pair in the array whose sum is equal to the given target.

To solve this problem, we can consider each element one by one (pointed out by the first pointer) and iterate through the remaining elements (pointed out by the second pointer) to find a pair with the given sum. The **time complexity** of this algorithm will be `O(N^2)` where `N` is the number of elements in the input array.

Given that the input array is sorted, an efficient way would be to start with one pointer in the beginning and another pointer at the end. At every step, we will see if the numbers pointed by the **two pointers** add up to the target sum. If they do not, we will do one of two things:
1. If the sum of the two numbers pointed by the **two pointers** is greater than the target sum, this means that we need a pair with a smaller sum. So, to try more pairs, we can decrement the end-pointer.
2. If the sum of the two numbers pointed by the **two pointers** is smaller than the target sum, this means that we need a pair with a larger sum. So, to try more pairs, we can increment the start-pointer.

## Pair with Target Sum  aka "Two Sum" (easy) 
https://leetcode.com/problems/two-sum/
> Given an array of sorted numbers and a `target` sum, find a pair in the array whose sum is equal to the given `target`.

Write a function to return the indices of the two numbers (i.e. the pair) such that they add up to the given `target`.

Since the given array is sorted, a brute-force solution could be to iterate through the array, taking one number at a time and searching for the second number through **Binary Search**. The **time complexity** of this algorithm will be `O(N*logN)`. Can we do better than this?

We can follow the **Two Pointers** approach. We will start with one pointer pointing to the beginning of the array and another pointing at the end. At every step, we will see if the numbers pointed by the **two pointers** add up to the `target` sum. If they do, we have found our pair; otherwise, we will do one of two things:
1. If the sum of the two numbers pointed by the **two pointers** is greater than the `target` sum, this means that we need a pair with a smaller sum. So, to try more pairs, we can decrement the end-pointer.
2. If the sum of the two numbers pointed by the **two pointers** is smaller than the `target` sum, this means that we need a pair with a larger sum. So, to try more pairs, we can increment the start-pointer.

### Brute Force Solution
```java
public static int[] pairWithTargetSum(int[] nums, int target) {
    for (int i = 0; i < nums.length; i++) {
        for (int j = i + 1; j < nums.length; j++) {
            if ((nums[i] + nums[j]) == target) {
                return new int[]{i, j};
            }
        }
    }
    return new int[]{-1, -1};
}
```

### Two pointer Solution
Assume the input is sorted
```java
public static int[] pairWithTargetSum(int[] arr, int target) {
    int start = 0;
    int end = arr.length - 1;
    
    while (start < end) {
        int currentSum = arr[start] + arr[end];
        
        if (currentSum == target) {
            return new int[]{start, end};
        }
        
        if (currentSum < target) {
            start++;
        } else {
            end--;
        }
    }
    return new int[]{-1, -1};
}

// Test cases
// pairWithTargetSum(new int[]{1, 2, 3, 4, 6}, 6) -> [1,3]
// pairWithTargetSum(new int[]{2, 5, 9, 11}, 11) -> [0,2]
```
- The **time complexity** of the above algorithm will be `O(N)`, where `N` is the total number of elements in the given array.
- The algorithm runs in constant space `O(1)`.

### Hash Table Solution 

Instead of using a two-pointer or a binary search approach, we can utilize a **HashMap** to search for the required pair. We can iterate through the array one number at a time. Let's say during our iteration we are at number `X`, so we need to find `Y` such that `"X + Y == Target"`. We will do two things here:
1. Search for `Y` (which is equivalent to `"Target - X"`) in the HashMap. If it is there, we have found the required pair.
2. Otherwise, insert `"X"` in the HashMap, so that we can search it for the later numbers.

```java
import java.util.HashMap;

public static int[] pairWithTargetSum(int[] nums, int target) {
    /* Instead of using a two-pointer or a binary search approach, 
     * we can utilize a HashMap to search for the required pair. 
     * We can iterate through the array one number at a time. 
     * Let's say during our iteration we are at number 'X', 
     * so we need to find 'Y' such that "X + Y == Target". 
     * We will do two things here: */
    
    HashMap<Integer, Integer> map = new HashMap<>();
    
    for (int i = 0; i < nums.length; i++) {
        int item = nums[i];
        
        if (map.containsKey(target - item)) {
            /* 1. Search for 'Y' (which is equivalent to "Target - X") in the HashMap. 
             * If it is there, we have found the required pair */
            return new int[]{map.get(target - item), i};
        }
        /* 2. Otherwise, insert "X" in the HashMap, so that we can search it for the later numbers. */
        map.put(nums[i], i);
    }
    return new int[]{-1, -1};
}

// Test cases
// pairWithTargetSum(new int[]{1, 2, 3, 4, 6}, 6) -> [1, 3]
// pairWithTargetSum(new int[]{2, 5, 9, 11}, 11) -> [0, 2]
// pairWithTargetSum(new int[]{2, 7, 11, 15}, 9) -> [0, 1]
// pairWithTargetSum(new int[]{3, 2, 4}, 6) -> [1, 2]
// pairWithTargetSum(new int[]{3, 3}, 6) -> [0, 1]
```

- The **time complexity** of the above algorithm will be `O(N)`, where `N` is the total number of elements in the given array.
- The **space complexity** will also be `O(N)`, as, in the worst case, we will be pushing `N` numbers in the HashMap.

## Remove Duplicates (easy)
https://leetcode.com/problems/remove-duplicates-from-sorted-array/

> Given an array of sorted numbers, **remove all duplicates** from it. You should **not use any extra space**; after removing the duplicates *in-place* return the length of the subarray that has no duplicate in it.

In this problem, we need to remove the duplicates *in-place* such that the resultant length of the array remains sorted. As the input array is sorted, therefore, one way to do this is to shift the elements left whenever we encounter duplicates. In other words, we will keep one pointer for iterating the array and one pointer for placing the next non-duplicate number. So our algorithm will be to iterate the array and whenever we see a non-duplicate number we move it next to the last non-duplicate number we've seen.

**Assume the input is sorted**

```java
public static int removeDuplicates(int[] arr) {
    /* shift the elements left when we encounter duplicates */
    
    /* one pointer for iterating */
    int i = 1;
    
    /* one pointer for placing this next non-duplicate */
    int nextNonDupe = 1;

    while (i < arr.length) {
        if (arr[nextNonDupe - 1] != arr[i]) {
            arr[nextNonDupe] = arr[i];
            nextNonDupe++;
        }
        i++;
    }
    return nextNonDupe;
}

// Test cases
// removeDuplicates(new int[]{2, 3, 3, 3, 6, 9, 9}) -> 4
// The first four elements after removing the duplicates will be [2, 3, 6, 9].

// removeDuplicates(new int[]{2, 2, 2, 11}) -> 2
// The first two elements after removing the duplicates will be [2, 11].
```
- The **time complexity** of the above algorithm will be `O(N)`, where `N` is the total number of elements in the given array.
- The algorithm runs in constant space `O(1)`.

### Remove Element
https://leetcode.com/problems/remove-element/

> Given an unsorted array of numbers and a target `key`, remove all instances of `key` *in-place* and return the new length of the array.

```java
public static int removeElement(int[] arr, int key) {
    /* pointer for index of the next element which is not the key */
    int nextElement = 0;

    for (int i = 0; i < arr.length; i++) {
        if (arr[i] != key) {
            arr[nextElement] = arr[i];
            nextElement++;
        }
    }
    return nextElement;
}

// Test cases
// removeElement(new int[]{3, 2, 3, 6, 3, 10, 9, 3}, 3) -> 4
// The array, [2, 6, 10, 9], will have a length of 4 after removing every 'key'.

// removeElement(new int[]{2, 11, 2, 2, 1}, 2) -> 2
// The array, [11, 1], will have a length of 2 after removing every 'key'.
```
- The **time complexity** of the above algorithm will be `O(N)`, where `N` is the total number of elements in the given array.
- The algorithm runs in constant space `O(1)`.

## Squaring a Sorted Array (easy)
https://leetcode.com/problems/squares-of-a-sorted-array/

This is a straightforward question. The only trick is that we can have negative numbers in the input array, which will make it a bit difficult to generate the output array with squares in sorted order.

An easier approach could be to first find the index of the first non-negative number in the array. After that, we can use **Two Pointers** to iterate the array. One pointer will move forward to iterate the non-negative numbers, and the other pointer will move backward to iterate the negative numbers. At any step, whichever number gives us a bigger square will be added to the output array. 

Since the numbers at both ends can give us the largest square, an alternate approach could be to use **two pointers** starting at both ends of the input array. At any step, whichever pointer gives us the bigger square, we add it to the result array and move to the next/previous number according to the pointer. 

```java
public static int[] makeSquares(int[] arr) {
    /* The only trick is that we can have negative numbers in the input array, which will make it a bit difficult to generate the output array with squares in sorted order.
     * An easier approach could be to first find the index of the first non-negative number in the array. 
     * After that, we can use Two Pointers to iterate the array. 
     * One pointer will move forward to iterate the non-negative numbers
     * the other pointer will move backward to iterate the negative numbers. At any step, whichever number gives us a bigger square will be added to the output array.
     * Since the numbers at both ends can give us the largest square, an alternate approach could be to use two pointers starting at both ends of the input array. At any step, whichever pointer gives us the bigger square, we add it to the result array and move to the next/previous number according to the pointer. */
    
    int n = arr.length;
    int[] squares = new int[n];
    int highestSquareIndex = n - 1;
    int start = 0;
    int end = n - 1;
    
    while (start <= end) {
        int startSquare = arr[start] * arr[start];
        int endSquare = arr[end] * arr[end];
        
        if (startSquare > endSquare) {
            squares[highestSquareIndex] = startSquare;
            start++;
        } else {
            squares[highestSquareIndex] = endSquare;
            end--;
        }
        highestSquareIndex--;
    }
    return squares;
}

// Test cases
// makeSquares(new int[]{-2, -1, 0, 2, 3}) -> [0, 1, 4, 4, 9]
// makeSquares(new int[]{-3, -1, 0, 1, 2}) -> [0, 1, 1, 4, 9]
```
- The above algorithm's **time complexity** will be `O(N)` as we are iterating the input array only once.
- The above algorithm's **space complexity** will also be `O(N)`; this space will be used for the output array.

## Triplet Sum to Zero (medium)
https://leetcode.com/problems/3sum/

> Given an array of unsorted numbers, find all unique triplets in it that add up to zero.

This problem follows the **Two Pointers** pattern and shares similarities with [Pair with Target Sum](#🌴-pair-with-target-sum-aka-"two-sum"-easy). A couple of differences are that the input array is not sorted and instead of a pair we need to find triplets with a target sum of zero.

To follow a similar approach, first, we will sort the array and then iterate through it taking one number at a time. Let's say during our iteration we are at number `X`, so we need to find `Y` and `Z` such that `X + Y + Z == 0`. At this stage, our problem translates into finding a pair whose sum is equal to `-X` (as from the above equation `Y + Z == -X`).

Another difference from **[Pair with Target Sum](#🌴-pair-with-target-sum-aka-"two-sum"-easy)** is that we need to find all the unique triplets. To handle this, we have to skip any duplicate number. Since we will be sorting the array, so all the duplicate numbers will be next to each other and are easier to skip.

```java
import java.util.*;

public static List<List<Integer>> searchTriplets(int[] arr) {
    Arrays.sort(arr);
    List<List<Integer>> triplets = new ArrayList<>();

    for (int i = 0; i < arr.length - 2; i++) {
        if (i > 0 && arr[i] == arr[i - 1]) {
            /* skip the same element to avoid dupes */
            continue;
        }
        searchPair(arr, -arr[i], i + 1, triplets);
    }
    return triplets;
}

private static void searchPair(int[] arr, int targetSum, int start, List<List<Integer>> triplets) {
    int end = arr.length - 1;

    while (start < end) {
        int currentSum = arr[start] + arr[end];
        if (currentSum == targetSum) {
            /* found the triplet */
            triplets.add(Arrays.asList(-targetSum, arr[start], arr[end]));
            start++;
            end--;
            while (start < end && arr[start] == arr[start - 1]) {
                /* skip same element to avoid duplicates */
                start++;
            }
            while (start < end && arr[end] == arr[end + 1]) {
                /* skip same element to avoid duplicates */
                end--;
            }
        } else if (targetSum > currentSum) {
            /* we need a pair with a bigger sum */
            start++;
        } else {
            /* we need a pair with a smaller sum */
            end--;
        }
    }
}

// Test cases
// searchTriplets(new int[]{-3, 0, 1, 2, -1, 1, -2}) 
// -> [[-3, 1, 2], [-2, 0, 2], [-2, 1, 1], [-1, 0, 1]]

// searchTriplets(new int[]{-5, 2, -1, -2, 3}) 
// -> [[-5, 2, 3], [-2, -1, 3]]
```
- Sorting the array will take `O(N * logN)`. The `searchPair()` function will take `O(N)`. As we are calling `searchPair()` for every number in the input array, this means that overall `searchTriplets()` will take `O(N * logN + N^2)`, which is asymptotically equivalent to `O(N^2)`.
- Ignoring the space required for the output array, the **space complexity** of the above algorithm will be `O(N)` which is required for sorting.

## Triplet Sum Close to Target (medium)
https://leetcode.com/problems/3sum-closest/

> Given an array of unsorted numbers and a `targetSum`, find a **triplet in the array whose sum is as close to the `targetSum` as possible**, return the sum of the triplet. If there are more than one such triplet, return the sum of the triplet with the smallest sum.

This problem follows the **Two Pointers** pattern and is quite similar to **[Triplet Sum to Zero](#🌟-triplet-sum-to-zero-medium)**.

We can follow a similar approach to iterate through the array, taking one number at a time. At every step, we will save the difference between the triplet and the `targetSum`, so that in the end, we can return the triplet with the closest sum.

```java
public static int tripletSumCloseToTarget(int[] arr, int targetSum) {
    Arrays.sort(arr);
    
    int smallestDifference = Integer.MAX_VALUE;
    
    for (int i = 0; i < arr.length - 2; i++) {
        int start = i + 1;
        int end = arr.length - 1;
        
        while (start < end) {
            int targetDifference = targetSum - arr[i] - arr[start] - arr[end];
            
            if (targetDifference == 0) {
                /* we've found a triplet with an exact sum
                 * so return the sum of all the numbers */
                return targetSum - targetDifference;
            }
            
            /* the second part of the following 'if' is to handle the smallest sum
             * when we have more than one solution */
            if (Math.abs(targetDifference) < Math.abs(smallestDifference) 
                || (Math.abs(targetDifference) == Math.abs(smallestDifference) 
                    && targetDifference > smallestDifference)) {
                /* save the closest and the biggest difference */
                smallestDifference = targetDifference;
            }
            
            if (targetDifference > 0) {
                /* we need a triplet with a bigger sum */
                start++;
            } else {
                /* we need a triplet with a smaller sum */
                end--;
            }
        }
    }
    return targetSum - smallestDifference;
}

// Test cases
// tripletSumCloseToTarget(new int[]{-2, 0, 1, 2}, 2) -> 1
// The triplet [-2, 1, 2] has the closest sum to the target.

// tripletSumCloseToTarget(new int[]{-3, -1, 1, 2}, 1) -> 0
// The triplet [-3, 1, 2] has the closest sum to the target.

// tripletSumCloseToTarget(new int[]{1, 0, 1, 1}, 100) -> 3
// The triplet [1, 1, 1] has the closest sum to the target.

// tripletSumCloseToTarget(new int[]{-1, 2, 1, -4}, 1) -> 2
// The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```
- Sorting the array will take `O(N* logN)`. Overall, the function will take `O(N * logN + N^2)`, which is asymptotically equivalent to `O(N^2)`
- The above algorithm's **space complexity** will be `O(N)`, which is required for sorting.

## Triplets with Smaller Sum (medium)
https://leetcode.com/problems/3sum-smaller/

> Given an array `arr` of unsorted numbers and a `target` sum, count all triplets in it such that `arr[i] + arr[j] + arr[k] < target` where `i`, `j`, and `k` are three different indices. Write a function to return the count of such triplets.

This problem follows the **Two Pointers pattern** and shares similarities with **Triplet Sum to Zero**. The only difference is that, in this problem, we need to find the triplets whose sum is less than the given target. To meet the condition `i != j != k` we need to make sure that each number is not used more than once.

Following a similar approach, first, we can sort the array and then iterate through it, taking one number at a time. Let's say during our iteration we are at number `X`, so we need to find `Y` and `Z` such that `X + Y + Z < target`. At this stage, our problem translates into finding a pair whose sum is less than `"target - X"` (as from the above equation `Y + Z == target - X`). We can use a similar approach as discussed in **Triplet Sum to Zero**.

```java
public static int tripletWithSmallerSum(int[] arr, int target) {
    Arrays.sort(arr);
    int count = 0;
    
    for (int i = 0; i < arr.length - 2; i++) {
        count += searchPair(arr, target - arr[i], i);
    }
    return count;
}

private static int searchPair(int[] arr, int targetSum, int first) {
    int count = 0;
    int start = first + 1;
    int end = arr.length - 1;
    
    while (start < end) {
        if (arr[start] + arr[end] < targetSum) {
            /* we found the a triplet
             * since arr[end] >= arr[start], therefore, we can replace arr[end]
             * by any number between start and end to get a sum less than the targetSum */
            count += end - start;
            start++;
        } else {
            /* we need a pair with a smaller sum */
            end--;
        }
    }
    return count;
}

// Test cases
// tripletWithSmallerSum(new int[]{-1, 0, 2, 3}, 3) -> 2
// There are two triplets whose sum is less than the target: [-1, 0, 3], [-1, 0, 2]

// tripletWithSmallerSum(new int[]{-1, 4, 2, 1, 3}, 5) -> 4
// There are four triplets whose sum is less than the target: [-1, 1, 4], [-1, 1, 3], [-1, 1, 2], [-1, 2, 3]

// tripletWithSmallerSum(new int[]{-2, 0, 1, 3}, 2) -> 2
// Because there are two triplets which sums are less than 2: [-2,0,1], [-2,0,3]

// tripletWithSmallerSum(new int[]{}, 0) -> 0
// tripletWithSmallerSum(new int[]{0}, 0) -> 0
```
- Sorting the array will take `O(N * logN)`. The `searchPair()` will take `O(N)`. So, overall `searchTriplets()` will take `O(N * logN + N^2)`, which is asymptotically equivalent to `O(N^2)`.
- The **space complexity** of the above algorithm will be `O(N)` which is required for sorting if we are not using an *in-place* sorting algorithm.

> Write a function to return the list of all such triplets instead of the count. How will the **time complexity** change in this case?

```java
public static List<List<Integer>> tripletWithSmallerSum(int[] arr, int target) {
    Arrays.sort(arr);
    List<List<Integer>> triplets = new ArrayList<>();
    
    for (int i = 0; i < arr.length - 2; i++) {
        searchPair(arr, target - arr[i], i, triplets);
    }
    return triplets;
}

private static void searchPair(int[] arr, int targetSum, int first, List<List<Integer>> triplets) {
    int start = first + 1;
    int end = arr.length - 1;
    
    while (start < end) {
        if (arr[start] + arr[end] < targetSum) {
            /* we found the a triplet
             * since arr[end] >= arr[start], therefore, we can replace arr[end]
             * by any number between start and end to get a sum less than the targetSum */
            for (int i = end; i > start; i--) {
                triplets.add(Arrays.asList(arr[first], arr[start], arr[i]));
            }
            start++;
        } else {
            /* we need a pair with a smaller sum */
            end--;
        }
    }
}
```
- Sorting the array will take `O(N * logN)`. The `searchPair()`, in this case, will take `O(N^2)`; the main while loop will run in `O(N)` but the nested for loop can also take `O(N)` - this will happen when the target sum is bigger than every triplet in the array.  So, overall `searchTriplets()` will take `O(N * logN + N^3)`, which is asymptotically equivalent to `O(N^3)`.
- Ignoring the space required for the output array, the **space complexity** of the above algorithm will be `O(N)` which is required for sorting.

## Subarrays with Product Less than a Target (medium)
https://leetcode.com/problems/subarray-product-less-than-k/

> Given an array with positive numbers and a `targetSum`, find all of its contiguous subarrays whose **product is less than the `targetSum`**.

This problem follows the **Sliding Window** and the **Two Pointers** pattern and shares similarities with **[Triplets with Smaller Sum](#triplets-with-smaller-sum-medium)** with two differences:
1. In this problem, the input array is not sorted.
2. Instead of finding triplets with sum less than a target, we need to find all subarrays having a product less than the target.
The implementation will be quite similar to **[Triplets with Smaller Sum](#triplets-with-smaller-sum-medium)**.

```java
import java.util.*;

public static List<List<Integer>> findSubarrays(int[] arr, int target) {
    List<List<Integer>> result = new ArrayList<>();
    double product = 1;
    int start = 0;
    
    for (int end = 0; end < arr.length; end++) {
        product *= arr[end];
        while (product >= target && start < arr.length) {
            product /= arr[start];
            start++;
        }
        /* since the product of all numbers from start to end is less than the target. 
         * Therefore, all subarrays from start to end will have a product less than the target too; 
         * to avoid duplicates, we will start with a subarray containing only arr[end] and then extend it */
        List<Integer> tempList = new LinkedList<>();
        for (int i = end; i >= start; i--) {
            tempList.add(0, arr[i]);
            result.add(new ArrayList<>(tempList));
        }
    }
    return result;
}

// Test cases
// findSubarrays(new int[]{2, 5, 3, 10}, 30)
// -> [[2], [5], [2, 5], [3], [5, 3], [10]] There are six contiguous subarrays whose product is less than the target.

// findSubarrays(new int[]{8, 2, 6, 5}, 50)
// -> [[8], [2], [8, 2], [6], [2, 6], [5], [6, 5]] There are seven contiguous subarrays whose product is less than the target.

// findSubarrays(new int[]{10, 5, 2, 6}, 100)
// -> The 8 subarrays that have product less than 100 are: [10], [5], [2], [6], [10, 5], [5, 2], [2, 6], [5, 2, 6].
```

- The main for-loop managing the sliding window takes `O(N)` but creating subarrays can take up to `O(N^2)` in the worst case. Therefore overall, our algorithm will take `O(N^3)`.
- Ignoring the space required for the output list, the algorithm runs in `O(N)` space which is used for the temp list.

## Dutch National Flag Problem (medium)
https://leetcode.com/problems/sort-colors/

> Given an array containing `0`s, `1`s and `2`s, sort the array *in-place*. You should treat numbers of the array as objects, hence, we can't count `0`s, `1`s, and `2`s to recreate the array.

The flag of the Netherlands consists of three colors: red, white and blue; and since our input array also consists of three different numbers that is why it is called **Dutch National Flag problem**.

The brute force solution will be to use an *in-place* sorting algorithm like *Heapsort* which will take `O(N*logN)`. Can we do better than this? Is it possible to sort the array in one iteration?

We can use a **Two Pointers** approach while iterating through the array. Let's say the **two pointers** are called `low` and `high` which are pointing to the first and the last element of the array respectively. So while iterating, we will move all `0`s before `low` and all `2`s after `high` so that in the end, all `1`s will be between `low` and `high`.

```java
public static void dutchFlagSort(int[] arr) {
    /* all elements < low are 0, and all elements > high are 2
     * all elements >= low < i are 1 */
    int low = 0;
    int high = arr.length - 1;
    int i = 0;
    
    while (i <= high) {
        if (arr[i] == 0) {
            /* swap */
            int temp = arr[i];
            arr[i] = arr[low];
            arr[low] = temp;
            /* increment i and low */
            i++;
            low++;
        } else if (arr[i] == 1) {
            i++;
        } else {
            /* the case for arr[i] == 2 */
            /* swap */
            int temp = arr[i];
            arr[i] = arr[high];
            arr[high] = temp;
            /* decrement high only, after the swap the number
             * at index i could be 0, 1, or 2 */
            high--;
        }
    }
}

// Test cases
// dutchFlagSort(new int[]{1, 0, 2, 1, 0}) -> [0, 0, 1, 1, 2]
// dutchFlagSort(new int[]{2, 2, 0, 1, 2, 0}) -> [0, 0, 1, 2, 2, 2]
```
- The **time complexity** of the above algorithm will be `O(N)` as we are iterating the input array only once.
- The algorithm runs in constant space `O(1)`.

## Quadruple Sum to Target (medium)
https://leetcode.com/problems/4sum/

> Given an array of unsorted numbers and a `targetSum`, find all **unique quadruplets** in it, whose **sum is equal to the `targetSum`**.

This problem follows the **Two Pointers** pattern and shares similarities with **[Triplet Sum to Zero](#🌟-triplet-sum-to-zero-medium)**.

We can follow a similar approach to iterate through the array, taking one number at a time. At every step during the iteration, we will search for the quadruplets similar to **Triplet Sum to Zero** whose sum is equal to the given `target`.

```java
import java.util.*;

public static List<List<Integer>> searchQuads(int[] arr, int target) {
    /* sort the array */
    Arrays.sort(arr);
    
    List<List<Integer>> quads = new ArrayList<>();
     
    for (int i = 0; i < arr.length - 3; i++) {
        /* skip the same element to avoid duplicate quadruplets */
        if (i > 0 && arr[i] == arr[i - 1]) {
            continue;
        }
        for (int j = i + 1; j < arr.length - 2; j++) {
            /* skip the same element to avoid duplicate quadruplets */
            if (j > i + 1 && arr[j] == arr[j - 1]) {
                continue;
            }
            searchPairs(arr, target, i, j, quads);
        }
    }
    return quads;
}

private static void searchPairs(int[] arr, int targetSum, int first, int second, List<List<Integer>> quads) {
    int start = second + 1;
    int end = arr.length - 1;
    
    while (start < end) {
        int sum = arr[first] + arr[second] + arr[start] + arr[end];
        if (sum == targetSum) {
            /* found the quadruplet */
            quads.add(Arrays.asList(arr[first], arr[second], arr[start], arr[end]));
            start++;
            end--;
            while (start < end && arr[start] == arr[start - 1]) {
                /* skip the same element to avoid duplicate quadruplets */
                start++;
            }
            while (start < end && arr[end] == arr[end + 1]) {
                /* skip the same element to avoid duplicate quadruplets */
                end--;
            }
        } else if (sum < targetSum) {
            /* we need a pair with a bigger sum */
            start++;
        } else {
            /* we need a pair with a smaller sum */
            end--;
        }
    }
}

// Test cases
// searchQuads(new int[]{4, 1, 2, -1, 1, -3}, 1) -> [[-3, -1, 1, 4], [-3, 1, 1, 2]]
// searchQuads(new int[]{2, 0, -1, 1, -2, 2}, 2) -> [[-2, 0, 2, 2], [-1, 0, 1, 2]]
```
- Sorting the array will take `O(N*logN)`. Overall `searchQuads()` will take `O(N * logN + N³)`, which is asymptotically equivalent to `O(N³)`.
- The **space complexity** of the above algorithm will be `O(N)` which is required for sorting.

## Comparing Strings containing Backspaces (medium)
https://leetcode.com/problems/backspace-string-compare/
>Given two strings containing backspaces (identified by the character `#`), check if the two strings are equal.

To compare the given strings, first, we need to apply the backspaces. An efficient way to do this would be from the end of both the strings. We can have separate pointers, pointing to the last element of the given strings. We can start comparing the characters pointed out by both the pointers to see if the strings are equal. If, at any stage, the character pointed out by any of the pointers is a backspace (`#`), we will skip and apply the backspace until we have a valid character available for comparison.

```java
public static boolean backspaceCompare(String str1, String str2) {
    /* use two pointer approach to compare the strings */
    int pointerOne = str1.length() - 1;
    int pointerTwo = str2.length() - 1;
    
    while (pointerOne >= 0 || pointerTwo >= 0) {
        int i = getNextChar(str1, pointerOne);
        int j = getNextChar(str2, pointerTwo);
        
        if (i < 0 && j < 0) {
            /* reached the end of both strings */
            return true;
        }
        if (i < 0 || j < 0) {
            /* reached the end of one string */
            return false;
        }
        if (str1.charAt(i) != str2.charAt(j)) {
            /* check if the characters are equal */
            return false;
        }
        pointerOne = i - 1;
        pointerTwo = j - 1;
    }
    return true;
}

private static int getNextChar(String str, int index) {
    int backspaceCount = 0;
    while (index >= 0) {
        if (str.charAt(index) == '#') {
            /* found a backspace character */
            backspaceCount++;
        } else if (backspaceCount > 0) {
            /* a non-backspace character */
            backspaceCount--;
        } else {
            break;
        }
        /* skip a backspace or valid character */
        index--;
    }
    return index;
}

// Test cases
// backspaceCompare("xy#z", "xzz#") -> true
// After applying backspaces the strings become "xz" and "xz" respectively.

// backspaceCompare("xy#z", "xyz#") -> false
// After applying backspaces the strings become "xz" and "xy" respectively.

// backspaceCompare("xp#", "xyz##") -> true
// After applying backspaces the strings become "x" and "x" respectively.  
// In "xyz##", the first '#' removes the character 'z' and the second '#' removes the character 'y'.

// backspaceCompare("xywrrmp", "xywrrmu#p") -> true
// After applying backspaces the strings become "xywrrmp" and "xywrrmp" respectively.
```
- The **time complexity** of the above algorithm will be `O(M+N)` where `M` and `N` are the lengths of the two input strings respectively.
- The algorithm runs in constant space `O(1)`.

## Minimum Window Sort (medium)
https://leetcode.com/problems/shortest-subarray-to-be-removed-to-make-array-sorted/

> Given an array, find the length of the smallest subarray in it which when sorted will sort the whole array.

As we know, once an array is sorted (in ascending order), the smallest number is at the beginning and the largest number is at the end of the array. So if we start from the beginning of the array to find the first element which is out of sorting order i.e., which is smaller than its previous element, and similarly from the end of array to find the first element which is bigger than its previous element, will sorting the subarray between these two numbers result in the whole array being sorted?

Let's try to understand this with the Example mentioned above. In the following array, what are the first numbers out of sorting order from the beginning and the end of the array:

```
[1, 3, 2, 0, -1, 7, 10]
```
Starting from the beginning of the array the first number out of the sorting order is `2` as it is smaller than its previous element which is `3`.
Starting from the end of the array the first number out of the sorting order is `0` as it is bigger than its previous element which is `-1`

As you can see, sorting the numbers between `3` and `-1` will not sort the whole array. To see this, the following will be our original array after the sorted subarray:
```
[1, -1, 0, 2, 3, 7, 10]
```
The problem here is that the smallest number of our subarray is `-1` which dictates that we need to include more numbers from the beginning of the array to make the whole array sorted. We will have a similar problem if the maximum of the subarray is bigger than some elements at the end of the array. To sort the whole array we need to include all such elements that are smaller than the biggest element of the subarray. So our final algorithm will look like:
1. From the beginning and end of the array, find the first elements that are out of the sorting order. The two elements will be our candidate subarray.
2. Find the maximum and minimum of this subarray.
3. Extend the subarray from beginning to include any number which is bigger than the minimum of the subarray.
4. Similarly, extend the subarray from the end to include any number which is smaller than the maximum of the subarray.

```java
public static int shortestWindowSort(int[] arr) {
    int low = 0;
    int high = arr.length - 1;
    
    /* find the first number out of sorting order from the beginning */
    while (low < arr.length - 1 && arr[low] <= arr[low + 1]) {
        low++;
    }
    if (low == arr.length - 1) {
        /* if the array is already sorted */
        return 0;
    }
    /* find the first number out of sorting order from the end */
    while (high > 0 && arr[high] >= arr[high - 1]) {
        high--;
    }
    
    /* find the max and min of the subarray */
    int subArrayMax = Integer.MIN_VALUE;
    int subArrayMin = Integer.MAX_VALUE;
    
    for (int k = low; k <= high; k++) {
        subArrayMax = Math.max(subArrayMax, arr[k]);
        subArrayMin = Math.min(subArrayMin, arr[k]);
    }
    
    /* extend the subarray to include any number which is bigger than the minimum of the subarray */
    while (low > 0 && arr[low - 1] > subArrayMin) {
        low--;
    }
    /* extend the subarray to include any number which is smaller than the maximum of the subarray */
    while (high < arr.length - 1 && arr[high + 1] < subArrayMax) {
        high++;
    }
    
    return high - low + 1;
}

// Test cases
// shortestWindowSort(new int[]{1, 2, 5, 3, 7, 10, 9, 12}) -> 5
// We need to sort only the subarray [5, 3, 7, 10, 9] to make the whole array sorted

// shortestWindowSort(new int[]{1, 3, 2, 0, -1, 7, 10}) -> 5
// We need to sort only the subarray [1, 3, 2, 0, -1] to make the whole array sorted

// shortestWindowSort(new int[]{1, 2, 3}) -> 0
// The array is already sorted

// shortestWindowSort(new int[]{3, 2, 1}) -> 3
// The whole array needs to be sorted.
```
- The **time complexity** of the above algorithm will be `O(N)`.
- The algorithm runs in constant space `O(1)`.

## Container With Most Water (medium-hard)
https://leetcode.com/problems/container-with-most-water/

> You are given an integer array `height` of length `n`. There are `n` vertical lines drawn such that the two endpoints of the `i`th line are `(i, 0)` and `(i, height[i])`. Find two lines that together with the x-axis form a container, such that the container contains the most water. Return the maximum amount of water a container can store.

The key insight is that the area is determined by the shorter of the two lines and the distance between them. We use two pointers starting from both ends and move the pointer pointing to the shorter line inward, as moving the taller line won't increase the area.

```java
public static int maxArea(int[] height) {
    int maxWater = 0;
    int left = 0;
    int right = height.length - 1;
    
    while (left < right) {
        /* Calculate current area */
        int currentArea = Math.min(height[left], height[right]) * (right - left);
        maxWater = Math.max(maxWater, currentArea);
        
        /* Move the pointer pointing to the shorter line
         * since moving the taller line won't increase the area */
        if (height[left] < height[right]) {
            left++;
        } else {
            right--;
        }
    }
    
    return maxWater;
}

// Test cases
// maxArea(new int[]{1, 8, 6, 2, 5, 4, 8, 3, 7}) -> 49
// maxArea(new int[]{1, 1}) -> 1
// maxArea(new int[]{4, 3, 2, 1, 4}) -> 16
// maxArea(new int[]{1, 2, 1}) -> 2
```
- **Time complexity**: `O(N)` where N is the length of the array
- **Space complexity**: `O(1)` constant space

## Trapping Rain Water (hard)
https://leetcode.com/problems/trapping-rain-water/

> Given `n` non-negative integers representing an elevation map where the width of each bar is `1`, compute how much water it can trap after raining.

This is a classic hard problem that can be solved elegantly with two pointers. The key insight is that the water level at any position is determined by the minimum of the maximum heights to its left and right.

```java
public static int trap(int[] height) {
    if (height == null || height.length <= 2) {
        return 0;
    }
    
    int left = 0;
    int right = height.length - 1;
    int leftMax = 0;
    int rightMax = 0;
    int totalWater = 0;
    
    while (left < right) {
        if (height[left] < height[right]) {
            /* Process left side */
            if (height[left] >= leftMax) {
                leftMax = height[left];
            } else {
                /* Water can be trapped */
                totalWater += leftMax - height[left];
            }
            left++;
        } else {
            /* Process right side */
            if (height[right] >= rightMax) {
                rightMax = height[right];
            } else {
                /* Water can be trapped */
                totalWater += rightMax - height[right];
            }
            right--;
        }
    }
    
    return totalWater;
}

// Test cases
// trap(new int[]{0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1}) -> 6
// trap(new int[]{4, 2, 0, 3, 2, 5}) -> 9
// trap(new int[]{3, 0, 2, 0, 4}) -> 7
```
- **Time complexity**: `O(N)` single pass through the array
- **Space complexity**: `O(1)` constant space

## Longest Mountain in Array (hard)
https://leetcode.com/problems/longest-mountain-in-array/

> You may recall that an array `arr` is a mountain array if and only if it has at least 3 elements and there exists some index `i` (0-indexed) such that: `arr[0] < arr[1] < ... < arr[i - 1] < arr[i]` and `arr[i] > arr[i + 1] > ... > arr[arr.length - 1]`. Given an integer array `arr`, return the length of the longest subarray which is a mountain.

```java
public static int longestMountain(int[] arr) {
    if (arr.length < 3) return 0;
    
    int maxLength = 0;
    int start = 0;
    
    while (start < arr.length - 2) {
        /* Find potential mountain start */
        while (start < arr.length - 1 && arr[start] >= arr[start + 1]) {
            start++;
        }
        
        if (start >= arr.length - 2) break;
        
        /* Find peak */
        int peak = start;
        while (peak < arr.length - 1 && arr[peak] < arr[peak + 1]) {
            peak++;
        }
        
        /* Find end of mountain (downward slope) */
        int end = peak;
        while (end < arr.length - 1 && arr[end] > arr[end + 1]) {
            end++;
        }
        
        /* Check if we have a valid mountain */
        if (peak > start && end > peak) {
            maxLength = Math.max(maxLength, end - start + 1);
        }
        
        start = end;
    }
    
    return maxLength;
}

// Test cases
// longestMountain(new int[]{2, 1, 4, 7, 3, 2, 5}) -> 5 (subarray [1,4,7,3,2])
// longestMountain(new int[]{2, 2, 2}) -> 0
// longestMountain(new int[]{0, 1, 2, 3, 4, 5, 4, 3, 2, 1, 0}) -> 11
```
- **Time complexity**: `O(N)` where N is the length of array
- **Space complexity**: `O(1)` constant space

## Substring with Concatenation of All Words (hard)
https://leetcode.com/problems/substring-with-concatenation-of-all-words/

> You are given a string `s` and an array of strings `words`. All strings in `words` are of the same length. A concatenated substring in `s` is a substring that contains exactly one occurrence of each string in any order. Return the starting indices of all the concatenated substrings in `s`.

```java
import java.util.*;

public static List<Integer> findSubstring(String s, String[] words) {
    List<Integer> result = new ArrayList<>();
    if (s == null || s.length() == 0 || words == null || words.length == 0) {
        return result;
    }
    
    int wordLen = words[0].length();
    int totalLen = wordLen * words.length;
    
    /* Create frequency map of words */
    Map<String, Integer> wordCount = new HashMap<>();
    for (String word : words) {
        wordCount.put(word, wordCount.getOrDefault(word, 0) + 1);
    }
    
    /* Try starting from each position within word length */
    for (int i = 0; i < wordLen; i++) {
        int left = i;
        int right = i;
        Map<String, Integer> currentCount = new HashMap<>();
        
        while (right + wordLen <= s.length()) {
            /* Add word at right pointer */
            String rightWord = s.substring(right, right + wordLen);
            right += wordLen;
            
            if (wordCount.containsKey(rightWord)) {
                currentCount.put(rightWord, currentCount.getOrDefault(rightWord, 0) + 1);
                
                /* Remove excess occurrences from left */
                while (currentCount.get(rightWord) > wordCount.get(rightWord)) {
                    String leftWord = s.substring(left, left + wordLen);
                    currentCount.put(leftWord, currentCount.get(leftWord) - 1);
                    left += wordLen;
                }
                
                /* Check if we have a valid window */
                if (right - left == totalLen) {
                    result.add(left);
                }
            } else {
                /* Reset window */
                currentCount.clear();
                left = right;
            }
        }
    }
    
    return result;
}

// Test cases
// findSubstring("barfoothefoobarman", new String[]{"foo","bar"}) -> [0, 9]
// findSubstring("wordgoodgoodgoodbestword", new String[]{"word","good","best","good"}) -> [8]
// findSubstring("barfoobar", new String[]{"foo","bar"}) -> [0, 3]
```
- **Time complexity**: `O(N * M)` where N is length of string s, M is length of each word
- **Space complexity**: `O(K)` where K is number of unique words

## Minimum Window Substring (hard)
https://leetcode.com/problems/minimum-window-substring/

> Given two strings `s` and `t` of lengths `m` and `n` respectively, return the minimum window substring of `s` such that every character in `t` (including duplicates) is included in the window. If there is no such window in `s` that covers all characters in `t`, return the empty string `""`.

```java
import java.util.HashMap;

public static String minWindow(String s, String t) {
    if (s.length() < t.length()) return "";
    
    /* Count characters in t */
    HashMap<Character, Integer> tCount = new HashMap<>();
    for (char c : t.toCharArray()) {
        tCount.put(c, tCount.getOrDefault(c, 0) + 1);
    }
    
    int required = tCount.size(); /* Number of unique chars in t that need to be present */
    int formed = 0; /* Number of unique chars in current window with desired frequency */
    
    HashMap<Character, Integer> windowCount = new HashMap<>();
    
    /* Two pointers for sliding window */
    int left = 0, right = 0;
    
    /* Result: (window length, left, right) */
    int[] result = {-1, 0, 0};
    
    while (right < s.length()) {
        /* Add character from right to window */
        char rightChar = s.charAt(right);
        windowCount.put(rightChar, windowCount.getOrDefault(rightChar, 0) + 1);
        
        /* Check if frequency matches required frequency */
        if (tCount.containsKey(rightChar) && 
            windowCount.get(rightChar).intValue() == tCount.get(rightChar).intValue()) {
            formed++;
        }
        
        /* Try to shrink window from left */
        while (left <= right && formed == required) {
            /* Update result if this window is smaller */
            if (result[0] == -1 || right - left + 1 < result[0]) {
                result[0] = right - left + 1;
                result[1] = left;
                result[2] = right;
            }
            
            /* Remove character from left */
            char leftChar = s.charAt(left);
            windowCount.put(leftChar, windowCount.get(leftChar) - 1);
            if (tCount.containsKey(leftChar) && 
                windowCount.get(leftChar) < tCount.get(leftChar)) {
                formed--;
            }
            
            left++;
        }
        
        right++;
    }
    
    return result[0] == -1 ? "" : s.substring(result[1], result[2] + 1);
}

// Test cases
// minWindow("ADOBECODEBANC", "ABC") -> "BANC"
// minWindow("a", "a") -> "a"
// minWindow("a", "aa") -> ""
// minWindow("ab", "b") -> "b"
```
- **Time complexity**: `O(|S| + |T|)` where |S| and |T| are lengths of strings S and T
- **Space complexity**: `O(|S| + |T|)` for the hashmaps

## Longest Substring with At Most K Distinct Characters (hard)
https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/

> Given a string `s` and an integer `k`, return the length of the longest substring of `s` that contains at most `k` distinct characters.

```java
import java.util.HashMap;

public static int lengthOfLongestSubstringKDistinct(String s, int k) {
    if (s == null || s.length() == 0 || k == 0) return 0;
    
    HashMap<Character, Integer> charCount = new HashMap<>();
    int left = 0;
    int maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char rightChar = s.charAt(right);
        charCount.put(rightChar, charCount.getOrDefault(rightChar, 0) + 1);
        
        /* Shrink window if we have more than k distinct characters */
        while (charCount.size() > k) {
            char leftChar = s.charAt(left);
            charCount.put(leftChar, charCount.get(leftChar) - 1);
            if (charCount.get(leftChar) == 0) {
                charCount.remove(leftChar);
            }
            left++;
        }
        
        maxLen = Math.max(maxLen, right - left + 1);
    }
    
    return maxLen;
}

// Test cases
// lengthOfLongestSubstringKDistinct("eceba", 2) -> 3 ("ece")
// lengthOfLongestSubstringKDistinct("aa", 1) -> 2
// lengthOfLongestSubstringKDistinct("abaccc", 2) -> 4 ("accc")
```
- **Time complexity**: `O(N)` where N is length of string
- **Space complexity**: `O(k)` for storing at most k+1 characters in hashmap

## Palindromic Substrings (hard - advanced two pointers)
https://leetcode.com/problems/palindromic-substrings/

> Given a string `s`, return the number of palindromic substrings in it. A string is a palindrome when it reads the same backward as forward. A substring is a contiguous sequence of characters within the string.

```java
public static int countSubstrings(String s) {
    if (s == null || s.length() == 0) return 0;
    
    int count = 0;
    
    /* Check for palindromes centered at each character and between characters */
    for (int i = 0; i < s.length(); i++) {
        /* Odd length palindromes (centered at i) */
        count += expandAroundCenter(s, i, i);
        
        /* Even length palindromes (centered between i and i+1) */
        count += expandAroundCenter(s, i, i + 1);
    }
    
    return count;
}

private static int expandAroundCenter(String s, int left, int right) {
    int count = 0;
    
    while (left >= 0 && right < s.length() && s.charAt(left) == s.charAt(right)) {
        count++;
        left--;
        right++;
    }
    
    return count;
}

// Test cases
// countSubstrings("abc") -> 3 ("a", "b", "c")
// countSubstrings("aaa") -> 6 ("a", "a", "a", "aa", "aa", "aaa")
// countSubstrings("aba") -> 4 ("a", "b", "a", "aba")
```
- **Time complexity**: `O(N^2)` where N is length of string
- **Space complexity**: `O(1)` constant space

## Split Array Largest Sum (hard)
https://leetcode.com/problems/split-array-largest-sum/

> Given an integer array `nums` and an integer `k`, split `nums` into `k` non-empty subarrays such that the largest sum of any subarray is minimized. Return the minimized largest sum of the split.

This uses binary search with two pointers validation - a classic hard pattern combination.

```java
public static int splitArray(int[] nums, int k) {
    /* Binary search on the answer */
    long left = 0; /* minimum possible answer */
    long right = 0; /* maximum possible answer */
    
    for (int num : nums) {
        left = Math.max(left, num); /* largest single element */
        right += num; /* sum of all elements */
    }
    
    while (left < right) {
        long mid = left + (right - left) / 2;
        
        if (canSplit(nums, k, mid)) {
            right = mid;
        } else {
            left = mid + 1;
        }
    }
    
    return (int) left;
}

/* Check if we can split array into k subarrays with max sum <= maxSum */
private static boolean canSplit(int[] nums, int k, long maxSum) {
    int subarrays = 1;
    long currentSum = 0;
    
    for (int num : nums) {
        currentSum += num;
        
        if (currentSum > maxSum) {
            /* Need a new subarray */
            subarrays++;
            currentSum = num;
            
            if (subarrays > k) {
                return false;
            }
        }
    }
    
    return true;
}

// Test cases
// splitArray(new int[]{7, 2, 5, 10, 8}, 2) -> 18
// splitArray(new int[]{1, 2, 3, 4, 5}, 2) -> 9
// splitArray(new int[]{1, 4, 4}, 3) -> 4
```
- **Time complexity**: `O(N * log(sum - max))` where N is array length
- **Space complexity**: `O(1)` constant space
