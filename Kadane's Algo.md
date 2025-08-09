# Pattern: Kadane's Algorithm

**Kadane's Algorithm** is a powerful dynamic programming technique used to solve the **Maximum Subarray Problem** and its variations. The core idea is to efficiently find the contiguous subarray within a one-dimensional array of numbers that has the largest sum.

The algorithm works by maintaining two key variables:
1. **Current Sum**: The maximum sum ending at the current position
2. **Global Maximum**: The overall maximum sum found so far

At each step, we decide whether to:
- **Extend** the existing subarray by adding the current element
- **Start fresh** from the current element (if starting fresh gives a better result)

The key insight is: **If the current sum becomes negative, it's better to start a new subarray from the current element rather than carrying forward the negative sum.**

## Core Template

```java
public static int findMaximumSubarraySum(int[] numbers) {
    if (numbers.length == 0) return 0;
    
    int globalMaximum = numbers[0];     // Global maximum
    int currentSum = numbers[0];        // Current sum
    
    for (int i = 1; i < numbers.length; i++) {
        /* Key decision: extend existing subarray or start fresh */
        currentSum = Math.max(numbers[i], currentSum + numbers[i]);
        
        /* Update global maximum */
        globalMaximum = Math.max(globalMaximum, currentSum);
    }
    
    return globalMaximum;
}
```

---

# Easy Problems

## Maximum Subarray (easy)
https://leetcode.com/problems/maximum-subarray/

> Given an integer array `nums`, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

This is the classic application of Kadane's algorithm. The algorithm runs in O(n) time and O(1) space.

```java
public static int findMaximumSubarraySum(int[] numbers) {
    /* Handle edge case */
    if (numbers == null || numbers.length == 0) {
        return 0;
    }
    
    int globalMaximum = numbers[0];     /* Global maximum sum */
    int currentSum = numbers[0];        /* Maximum sum ending at current position */
    
    for (int i = 1; i < numbers.length; i++) {
        /* Either extend the existing subarray or start a new one */
        currentSum = Math.max(numbers[i], currentSum + numbers[i]);
        
        /* Update the global maximum */
        globalMaximum = Math.max(globalMaximum, currentSum);
    }
    
    return globalMaximum;
}

// Test cases
// findMaximumSubarraySum(new int[]{-2, 1, -3, 4, -1, 2, 1, -5, 4}) -> 6 (subarray [4,-1,2,1])
// findMaximumSubarraySum(new int[]{1}) -> 1
// findMaximumSubarraySum(new int[]{5, 4, -1, 7, 8}) -> 23 (entire array)
// findMaximumSubarraySum(new int[]{-2, -1}) -> -1
```

**Alternative Implementation with Tracking Indices:**

```java
public static SubarrayResult findMaximumSubarrayWithIndices(int[] numbers) {
    if (numbers == null || numbers.length == 0) {
        return new SubarrayResult(0, -1, -1);
    }
    
    int globalMaximum = numbers[0];
    int currentSum = numbers[0];
    int startIndex = 0, endIndex = 0, temporaryStartIndex = 0;
    
    for (int i = 1; i < numbers.length; i++) {
        if (currentSum < 0) {
            /* Start a new subarray */
            currentSum = numbers[i];
            temporaryStartIndex = i;
        } else {
            /* Extend existing subarray */
            currentSum += numbers[i];
        }
        
        if (currentSum > globalMaximum) {
            globalMaximum = currentSum;
            startIndex = temporaryStartIndex;
            endIndex = i;
        }
    }
    
    return new SubarrayResult(globalMaximum, startIndex, endIndex);
}

static class SubarrayResult {
    int maximumSum;
    int startIndex;
    int endIndex;
    
    SubarrayResult(int sum, int start, int end) {
        this.maximumSum = sum;
        this.startIndex = start;
        this.endIndex = end;
    }
}
```

- **Time complexity**: `O(N)` where N is the length of the array
- **Space complexity**: `O(1)` constant space

## Best Time to Buy and Sell Stock (easy)
https://leetcode.com/problems/best-time-to-buy-and-sell-stock/

> You are given an array `prices` where `prices[i]` is the price of a given stock on the `i`th day. You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock. Return the maximum profit you can achieve from this transaction.

This can be solved using a Kadane's algorithm approach by thinking of it as maximum subarray of price differences.

```java
public static int calculateMaximumProfit(int[] stockPrices) {
    if (stockPrices == null || stockPrices.length < 2) {
        return 0;
    }
    
    int lowestPrice = stockPrices[0];
    int maximumProfit = 0;
    
    for (int i = 1; i < stockPrices.length; i++) {
        /* Update maximum profit if selling today */
        maximumProfit = Math.max(maximumProfit, stockPrices[i] - lowestPrice);
        
        /* Update minimum price seen so far */
        lowestPrice = Math.min(lowestPrice, stockPrices[i]);
    }
    
    return maximumProfit;
}

/* Alternative: Kadane's approach on price differences */
public static int calculateMaximumProfitKadaneStyle(int[] stockPrices) {
    if (stockPrices == null || stockPrices.length < 2) {
        return 0;
    }
    
    int currentProfit = 0;
    int maximumProfit = 0;
    
    for (int i = 1; i < stockPrices.length; i++) {
        /* Profit from buying previous day and selling today */
        int dailyPriceChange = stockPrices[i] - stockPrices[i - 1];
        
        /* Apply Kadane's algorithm on daily price changes */
        currentProfit = Math.max(dailyPriceChange, currentProfit + dailyPriceChange);
        maximumProfit = Math.max(maximumProfit, currentProfit);
    }
    
    return maximumProfit;
}

// Test cases
// calculateMaximumProfit(new int[]{7, 1, 5, 3, 6, 4}) -> 5 (buy at 1, sell at 6)
// calculateMaximumProfit(new int[]{7, 6, 4, 3, 1}) -> 0 (no profit possible)
// calculateMaximumProfit(new int[]{1, 2}) -> 1
// calculateMaximumProfit(new int[]{2, 4, 1}) -> 2
```

- **Time complexity**: `O(N)` where N is the number of days
- **Space complexity**: `O(1)` constant space

---

# Medium Problems

## Maximum Product Subarray (medium)
https://leetcode.com/problems/maximum-product-subarray/

> Given an integer array `nums`, find a contiguous non-empty subarray within the array that has the largest product, and return the product.

This is a variation of Kadane's algorithm. The key insight is that we need to track both maximum and minimum products because a negative number can turn a small negative product into a large positive product.

```java
public static int findMaximumProductSubarray(int[] numbers) {
    if (numbers == null || numbers.length == 0) {
        return 0;
    }
    
    /* Track both max and min products ending at current position */
    int globalMaximum = numbers[0];
    int currentMaximumProduct = numbers[0];
    int currentMinimumProduct = numbers[0];
    
    for (int i = 1; i < numbers.length; i++) {
        /* If current number is negative, swap max and min */
        if (numbers[i] < 0) {
            int temporary = currentMaximumProduct;
            currentMaximumProduct = currentMinimumProduct;
            currentMinimumProduct = temporary;
        }
        
        /* Update max and min products ending here */
        currentMaximumProduct = Math.max(numbers[i], currentMaximumProduct * numbers[i]);
        currentMinimumProduct = Math.min(numbers[i], currentMinimumProduct * numbers[i]);
        
        /* Update global maximum */
        globalMaximum = Math.max(globalMaximum, currentMaximumProduct);
    }
    
    return globalMaximum;
}

// Test cases
// findMaximumProductSubarray(new int[]{2, 3, -2, 4}) -> 6 (subarray [2,3])
// findMaximumProductSubarray(new int[]{-2, 0, -1}) -> 0
// findMaximumProductSubarray(new int[]{-2, 3, -4}) -> 24 (subarray [-2,3,-4])
// findMaximumProductSubarray(new int[]{0, 2}) -> 2
```

- **Time complexity**: `O(N)` where N is the length of the array
- **Space complexity**: `O(1)` constant space

## Maximum Sum Circular Subarray (medium)
https://leetcode.com/problems/maximum-sum-circular-subarray/

> Given a circular integer array `nums` of length `n`, return the maximum possible sum of a non-empty subarray of `nums`.

This problem has two cases:
1. **Non-circular**: Maximum subarray doesn't wrap around (use standard Kadane's)
2. **Circular**: Maximum subarray wraps around (total sum - minimum subarray)

```java
public static int findMaximumCircularSubarraySum(int[] numbers) {
    /* Case 1: Maximum subarray is non-circular (standard Kadane's) */
    int maximumNormalSubarray = findMaximumSubarraySum(numbers);
    
    /* Case 2: Maximum subarray is circular */
    int totalSum = 0;
    for (int number : numbers) {
        totalSum += number;
    }
    
    /* Find minimum subarray sum using Kadane's on negated array */
    int minimumSubarray = findMinimumSubarraySum(numbers);
    int maximumCircularSubarray = totalSum - minimumSubarray;
    
    /* Edge case: if all numbers are negative, maximumCircularSubarray will be 0
     * but we need at least one element, so return maximumNormalSubarray */
    if (maximumCircularSubarray == 0) {
        return maximumNormalSubarray;
    }
    
    /* Return maximum of both cases */
    return Math.max(maximumNormalSubarray, maximumCircularSubarray);
}

private static int findMaximumSubarraySum(int[] numbers) {
    int globalMaximum = numbers[0];
    int currentSum = numbers[0];
    
    for (int i = 1; i < numbers.length; i++) {
        currentSum = Math.max(numbers[i], currentSum + numbers[i]);
        globalMaximum = Math.max(globalMaximum, currentSum);
    }
    
    return globalMaximum;
}

private static int findMinimumSubarraySum(int[] numbers) {
    int globalMinimum = numbers[0];
    int currentSum = numbers[0];
    
    for (int i = 1; i < numbers.length; i++) {
        currentSum = Math.min(numbers[i], currentSum + numbers[i]);
        globalMinimum = Math.min(globalMinimum, currentSum);
    }
    
    return globalMinimum;
}

// Test cases
// findMaximumCircularSubarraySum(new int[]{1, -2, 3, -2}) -> 3
// findMaximumCircularSubarraySum(new int[]{5, -3, 5}) -> 10 (circular: [5, 5])
// findMaximumCircularSubarraySum(new int[]{-3, -2, -3}) -> -2
// findMaximumCircularSubarraySum(new int[]{3, -2, 2, -3}) -> 3
```

- **Time complexity**: `O(N)` where N is the length of the array
- **Space complexity**: `O(1)` constant space

## House Robber (medium)
https://leetcode.com/problems/house-robber/

> You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed, the only constraint stopping you from robbing each of them is that adjacent houses have security systems connected and it will automatically contact the police if two adjacent houses were broken into on the same night. Given an integer array `nums` representing the amount of money of each house, return the maximum amount of money you can rob tonight without alerting the police.

This is a variation of Kadane's algorithm for non-adjacent elements.

```java
public static int calculateMaximumRobbedMoney(int[] houseMoney) {
    if (houseMoney == null || houseMoney.length == 0) {
        return 0;
    }
    if (houseMoney.length == 1) {
        return houseMoney[0];
    }
    
    /* maxMoney[i] represents max money that can be robbed up to house i */
    int previousPreviousMax = houseMoney[0];           /* maxMoney[i-2] */
    int previousMax = Math.max(houseMoney[0], houseMoney[1]); /* maxMoney[i-1] */
    
    for (int i = 2; i < houseMoney.length; i++) {
        /* Either rob current house + best from i-2, or skip current house */
        int currentMax = Math.max(previousMax, previousPreviousMax + houseMoney[i]);
        previousPreviousMax = previousMax;
        previousMax = currentMax;
    }
    
    return previousMax;
}

// Alternative Kadane-style implementation
public static int calculateMaximumRobbedMoneyKadaneStyle(int[] houseMoney) {
    if (houseMoney == null || houseMoney.length == 0) return 0;
    
    int includeCurrentHouse = 0; /* Maximum money including previous house */
    int excludeCurrentHouse = 0; /* Maximum money excluding previous house */
    
    for (int money : houseMoney) {
        /* Current max excluding current house */
        int newExclude = Math.max(includeCurrentHouse, excludeCurrentHouse);
        
        /* Current max including current house */
        includeCurrentHouse = excludeCurrentHouse + money;
        
        excludeCurrentHouse = newExclude;
    }
    
    return Math.max(includeCurrentHouse, excludeCurrentHouse);
}

// Test cases
// calculateMaximumRobbedMoney(new int[]{1, 2, 3, 1}) -> 4 (rob house 0 and 2)
// calculateMaximumRobbedMoney(new int[]{2, 7, 9, 3, 1}) -> 12 (rob house 0, 2, and 4)
// calculateMaximumRobbedMoney(new int[]{2, 1, 1, 2}) -> 4
// calculateMaximumRobbedMoney(new int[]{5}) -> 5
```

- **Time complexity**: `O(N)` where N is the number of houses
- **Space complexity**: `O(1)` constant space

## Maximum Subarray Sum with One Deletion (medium)
https://leetcode.com/problems/maximum-subarray-sum-with-one-deletion/

> Given an array of integers, return the maximum sum for a non-empty subarray (contiguous elements) with at most one element deletion.

This extends Kadane's algorithm to handle the option of deleting one element.

```java
public static int findMaximumSumWithOneDeletion(int[] numbers) {
    if (numbers == null || numbers.length == 0) return 0;
    if (numbers.length == 1) return numbers[0];
    
    /* Two states: with deletion and without deletion */
    int sumWithoutDeletion = numbers[0]; /* Max sum without using deletion */
    int sumWithDeletion = 0;             /* Max sum with one deletion used */
    int globalMaximum = numbers[0];
    
    for (int i = 1; i < numbers.length; i++) {
        /* Update sumWithDeletion: either delete current element or extend previous deletion */
        sumWithDeletion = Math.max(sumWithoutDeletion, sumWithDeletion + numbers[i]);
        
        /* Update sumWithoutDeletion: standard Kadane's algorithm */
        sumWithoutDeletion = Math.max(numbers[i], sumWithoutDeletion + numbers[i]);
        
        /* Update global maximum */
        globalMaximum = Math.max(globalMaximum, Math.max(sumWithDeletion, sumWithoutDeletion));
    }
    
    return globalMaximum;
}

// Test cases
// findMaximumSumWithOneDeletion(new int[]{1, -2, 0, 3}) -> 4 (delete -2, subarray [1,0,3])
// findMaximumSumWithOneDeletion(new int[]{1, -2, -2, 3}) -> 3 (delete one -2, subarray [3])
// findMaximumSumWithOneDeletion(new int[]{-1, -1, -1, -1}) -> -1 (delete one element)
// findMaximumSumWithOneDeletion(new int[]{2, 1, -2, -5, -2}) -> 3 (subarray [2,1])
```

- **Time complexity**: `O(N)` where N is the length of the array
- **Space complexity**: `O(1)` constant space

## Maximum Length of Subarray With Positive Product (medium)
https://leetcode.com/problems/maximum-length-of-subarray-with-positive-product/

> Given an array of integers `nums`, find the maximum length of a subarray where the product of all its elements is positive.

This applies Kadane's concept to track lengths instead of sums, considering positive/negative products.

```java
public static int findMaximumLengthPositiveProduct(int[] numbers) {
    if (numbers == null || numbers.length == 0) return 0;
    
    int maximumLength = 0;
    int positiveProductLength = 0; /* Length of current subarray with positive product */
    int negativeProductLength = 0; /* Length of current subarray with negative product */
    
    for (int number : numbers) {
        if (number == 0) {
            /* Reset both lengths */
            positiveProductLength = 0;
            negativeProductLength = 0;
        } else if (number > 0) {
            /* Positive number extends both positive and negative subarrays */
            positiveProductLength++;
            negativeProductLength = (negativeProductLength == 0) ? 0 : negativeProductLength + 1;
        } else {
            /* Negative number: positive becomes negative, negative becomes positive */
            int temporaryPositive = positiveProductLength;
            positiveProductLength = (negativeProductLength == 0) ? 0 : negativeProductLength + 1;
            negativeProductLength = temporaryPositive + 1;
        }
        
        maximumLength = Math.max(maximumLength, positiveProductLength);
    }
    
    return maximumLength;
}

// Test cases
// findMaximumLengthPositiveProduct(new int[]{1, -2, -3, 4}) -> 4 (entire array has positive product)
// findMaximumLengthPositiveProduct(new int[]{0, 1, -2, -3, -4}) -> 3 (subarray [-2,-3,-4])
// findMaximumLengthPositiveProduct(new int[]{-1, -2, -3, 0, 1}) -> 2 (subarray [-1,-2])
// findMaximumLengthPositiveProduct(new int[]{1, 2, 3, 5, -6, 4, 0, 10}) -> 4 (subarray [1,2,3,5] or [4] or [10])
```

- **Time complexity**: `O(N)` where N is the length of the array
- **Space complexity**: `O(1)` constant space

---

# Hard Problems

## Maximum Sum of 3 Non-Overlapping Subarrays (hard)
https://leetcode.com/problems/maximum-sum-of-3-non-overlapping-subarrays/

> Given an integer array `nums` and an integer `k`, find three non-overlapping subarrays of length `k` with maximum sum and return them.

This combines Kadane's concept with dynamic programming for multiple subarrays.

```java
public static int[] findThreeMaximumSumSubarrays(int[] numbers, int subarrayLength) {
    int arrayLength = numbers.length;
    
    /* Calculate sum of each k-length subarray */
    int[] subarraySums = new int[arrayLength - subarrayLength + 1];
    int windowSum = 0;
    for (int i = 0; i < subarrayLength; i++) {
        windowSum += numbers[i];
    }
    subarraySums[0] = windowSum;
    
    for (int i = 1; i < subarraySums.length; i++) {
        windowSum = windowSum - numbers[i - 1] + numbers[i + subarrayLength - 1];
        subarraySums[i] = windowSum;
    }
    
    /* Track best single subarray ending at or before each position */
    int[] bestLeftSubarray = new int[subarraySums.length];
    int bestLeftIndex = 0;
    for (int i = 0; i < subarraySums.length; i++) {
        if (subarraySums[i] > subarraySums[bestLeftIndex]) {
            bestLeftIndex = i;
        }
        bestLeftSubarray[i] = bestLeftIndex;
    }
    
    /* Track best single subarray starting at or after each position */
    int[] bestRightSubarray = new int[subarraySums.length];
    int bestRightIndex = subarraySums.length - 1;
    for (int i = subarraySums.length - 1; i >= 0; i--) {
        if (subarraySums[i] >= subarraySums[bestRightIndex]) {
            bestRightIndex = i;
        }
        bestRightSubarray[i] = bestRightIndex;
    }
    
    /* Find the best middle subarray */
    int[] resultIndices = new int[3];
    int maximumTotalSum = 0;
    
    for (int middleIndex = subarrayLength; middleIndex < subarraySums.length - subarrayLength; middleIndex++) {
        int leftIndex = bestLeftSubarray[middleIndex - subarrayLength];
        int rightIndex = bestRightSubarray[middleIndex + subarrayLength];
        int totalSum = subarraySums[leftIndex] + subarraySums[middleIndex] + subarraySums[rightIndex];
        
        if (totalSum > maximumTotalSum) {
            maximumTotalSum = totalSum;
            resultIndices[0] = leftIndex;
            resultIndices[1] = middleIndex;
            resultIndices[2] = rightIndex;
        }
    }
    
    return resultIndices;
}

// Test cases
// findThreeMaximumSumSubarrays(new int[]{1,2,1,2,6,7,5,1}, 2) -> [0,3,5] (subarrays [1,2], [2,6], [7,5])
// findThreeMaximumSumSubarrays(new int[]{1,2,1,2,1,2,1,2,1}, 2) -> [0,2,4] (subarrays [1,2], [1,2], [1,2])
```

- **Time complexity**: `O(N)` where N is the length of the array
- **Space complexity**: `O(N)` for auxiliary arrays

## Maximum Subarray Sum After One Operation (hard)
https://leetcode.com/problems/maximum-subarray-sum-after-one-operation/

> You are given an integer array `nums`. You must perform exactly one operation where you can replace one element `nums[i]` with `nums[i] * nums[i]`. Return the maximum possible subarray sum after performing this operation.

This extends Kadane's algorithm to handle the mandatory square operation.

```java
public static int findMaximumSumAfterSquareOperation(int[] numbers) {
    if (numbers == null || numbers.length == 0) return 0;
    
    /* Three states: no operation, operation not used, operation used */
    int sumWithoutOperation = numbers[0];        /* Max sum without using operation */
    int sumWithOperationUsed = numbers[0] * numbers[0]; /* Max sum with operation used */
    int globalMaximum = Math.max(sumWithoutOperation, sumWithOperationUsed);
    
    for (int i = 1; i < numbers.length; i++) {
        int currentNumber = numbers[i];
        int squaredNumber = currentNumber * currentNumber;
        
        /* Update sumWithOperationUsed: either use operation on current element or extend previous */
        int newSumWithOperation = Math.max(
            Math.max(squaredNumber, sumWithoutOperation + squaredNumber), /* Use operation on current */
            sumWithOperationUsed + currentNumber                         /* Extend with operation already used */
        );
        
        /* Update sumWithoutOperation: standard Kadane's without using operation */
        sumWithoutOperation = Math.max(currentNumber, sumWithoutOperation + currentNumber);
        
        sumWithOperationUsed = newSumWithOperation;
        globalMaximum = Math.max(globalMaximum, Math.max(sumWithoutOperation, sumWithOperationUsed));
    }
    
    return globalMaximum;
}

// Test cases
// findMaximumSumAfterSquareOperation(new int[]{2, -1, -4, -3}) -> 17
// findMaximumSumAfterSquareOperation(new int[]{1, -1, 1, 1, -1, -1, 1}) -> 4
```

- **Time complexity**: `O(N)` where N is the length of the array
- **Space complexity**: `O(1)` constant space

## Count Number of Nice Subarrays (hard - Kadane variation)
https://leetcode.com/problems/count-number-of-nice-subarrays/

> Given an array of integers `nums` and an integer `k`, return the number of nice subarrays. A nice subarray is a subarray where the number of odd numbers in it is exactly equal to `k`.

This uses a Kadane-like approach with prefix sums and hash map to count subarrays.

```java
import java.util.HashMap;

public static int countNiceSubarrays(int[] numbers, int targetOddCount) {
    /* Transform array: 1 for odd, 0 for even */
    HashMap<Integer, Integer> prefixSumFrequency = new HashMap<>();
    prefixSumFrequency.put(0, 1); /* Empty prefix has sum 0 */
    
    int currentPrefixSum = 0;
    int totalNiceSubarrays = 0;
    
    for (int number : numbers) {
        /* Add 1 if odd, 0 if even */
        currentPrefixSum += (number % 2 == 1) ? 1 : 0;
        
        /* Look for prefix sum that would give us exactly k odds */
        int requiredPreviousSum = currentPrefixSum - targetOddCount;
        totalNiceSubarrays += prefixSumFrequency.getOrDefault(requiredPreviousSum, 0);
        
        /* Add current prefix sum to map */
        prefixSumFrequency.put(currentPrefixSum, 
            prefixSumFrequency.getOrDefault(currentPrefixSum, 0) + 1);
    }
    
    return totalNiceSubarrays;
}

/* Alternative sliding window approach */
public static int countNiceSubarraysWithSlidingWindow(int[] numbers, int targetOddCount) {
    return countSubarraysWithAtMostKOdds(numbers, targetOddCount) - 
           countSubarraysWithAtMostKOdds(numbers, targetOddCount - 1);
}

private static int countSubarraysWithAtMostKOdds(int[] numbers, int maxOddCount) {
    int leftPointer = 0;
    int currentOddCount = 0;
    int totalSubarrays = 0;
    
    for (int rightPointer = 0; rightPointer < numbers.length; rightPointer++) {
        if (numbers[rightPointer] % 2 == 1) {
            currentOddCount++;
        }
        
        while (currentOddCount > maxOddCount) {
            if (numbers[leftPointer] % 2 == 1) {
                currentOddCount--;
            }
            leftPointer++;
        }
        
        /* All subarrays ending at rightPointer with at most maxOddCount odds */
        totalSubarrays += rightPointer - leftPointer + 1;
    }
    
    return totalSubarrays;
}

// Test cases
// countNiceSubarrays(new int[]{1,1,2,1,1}, 3) -> 2 (subarrays [1,1,2,1,1] and [1,2,1,1])
// countNiceSubarrays(new int[]{2,4,6}, 1) -> 0 (no odd numbers)
// countNiceSubarrays(new int[]{2,2,2,1,2,2,1,2,2,2}, 2) -> 16
```

- **Time complexity**: `O(N)` where N is the length of the array
- **Space complexity**: `O(N)` for the hash map
