## segment tree solution for count of sum lies between given range 
#### https://leetcode.com/problems/count-of-range-sum/submissions/
```
Clean Segment Tree Solution
0
chase's avatar
chase
29
March 21, 2018 8:42 AM

156 VIEWS

class Solution {
    public int countRangeSum(int[] nums, int lower, int upper) {
        
        if (nums == null) {
            return 0;
        }
        
        int n = nums.length;
        if (n == 0) {
            return 0;
        }
        
        long[] prefixSum = new long[n + 1];
        for (int i = 1; i <= n; i++) {
            prefixSum[i] = prefixSum[i - 1] + nums[i - 1];
        }
        
        Arrays.sort(prefixSum);
        Node root = build(prefixSum, 0, n);
        
        prefixSum[0] = 0;
        for (int i = 1; i <= n; i++) {
            prefixSum[i] = prefixSum[i - 1] + nums[i - 1];
        }
        
        int result = 0;
        modify(root, 0);
        for (int i = 1; i <= n; i++) {
            result += query(root, prefixSum[i] - upper, prefixSum[i] - lower);
            modify(root, prefixSum[i]);
        }
        
        return result;
    }
    
    private int query(Node root, long start, long end) {
        
        if (root.start >= start && root.end <= end) {
            return root.count;
        }
        
				// Only need to check the left node, since its supposed to be full binary tree
        if (root.left == null) {
            return 0;
        }
        
        if (start > root.left.end) {
            return query(root.right, start, end);
        }
        
        if (end < root.right.start) {
            return query(root.left, start, end);
        }
        
        return query(root.left, start, root.left.end) + query(root.right, root.right.start, end);
    }
    
    private void modify(Node root, long val) {
        
        root.count++;
        
        if (root.left == null && root.right == null) {
            return;
        }
        
        if (val <= root.left.end) {
            modify(root.left, val);
        } else {
            modify(root.right, val);
        }
    }
    
    private Node build(long[] sum, int left, int right) {
        Node root = new Node(sum[left], sum[right]);
        
        if (left == right) {
            return root;
        }
        
        int mid = (left + right) / 2;
        root.left = build(sum, left, mid);
        root.right = build(sum, mid + 1, right);
        return root;
    }
    
		// The number of element that is between start and end inclusive
    private class Node {
        Node left;
        Node right;
        long start;
        long end;
        int count;
        
        Node(long start, long end) {
            this.start = start;
            this.end = end;
        }
    }
}
```


## Java MergeSort O(n long n) Solution for Reverse Pairs, Count of Smaller after self, and Count of Range Sum. Template Coding!
```
Reverse Pairs

	public int reversePairs(int[] nums) {
		if (nums == null || nums.length < 2) return 0;
        return mergesort(nums, 0, nums.length-1);
    }
	
	private int mergesort(int[] nums, int low, int high){
		if (low >= high) return 0;
		int mid = low + (high - low) / 2;
		int count = mergesort(nums, low, mid) + mergesort(nums, mid+1, high);
		for (int i = low, j = mid+1; i <= mid && j <= high;){
			if (nums[i] > (long) nums[j] * 2){
                count += mid - i + 1;
                j++;
            }
            else i++;
		}
		
		merge(nums, low, high);
		return count;
	}
	
	private void merge(int[] nums, int low, int high){
		int mid = low + (high - low) / 2;
		int[] arr = new int[high - low + 1];
		
		int i = low, j = mid + 1, k = 0;
		while (k < arr.length){
			int num1 = i > mid ? Integer.MAX_VALUE : nums[i];
			int num2 = j > high ? Integer.MAX_VALUE : nums[j];
			
			arr[k++] = num1 <= num2 ? nums[i++] : nums[j++];
		}
		
		for (int p = 0; p < arr.length; p++) nums[p+low] = arr[p];
	}
Count of Smaller after self

public List<Integer> countSmaller(int[] nums) {
        List<Integer> res = new ArrayList<>();
        int[] count = new int[nums.length];
        int[] index = new int[nums.length];
        for (int i = 0; i < index.length; i++) index[i] = i;
        
        mergeSortCountSmaller(nums, index, count, 0, nums.length-1);
        
        for (int i : count) res.add(i);
        return res;
    }
	
	private void mergeSortCountSmaller(int[] nums, int[] index, int[] count, int low, int high){
		if (low >= high) return;
		int mid = low + (high - low) / 2;
		mergeSortCountSmaller(nums, index, count, low, mid);
		mergeSortCountSmaller(nums, index, count, mid+1, high);
		int rightCount = 0, i = low;
		for (int j = mid + 1; i <= mid && j <= high; ){
			if (nums[index[j]] < nums[index[i]]){
				rightCount++;
				j++;
			}
			else count[index[i++]] += rightCount;
		}
		
		while (i <= mid) count[index[i++]] += rightCount;
		
		mergeCountSmaller(nums, index, low, high);
	}
	
	private void mergeCountSmaller(int[] nums, int[] index, int low, int high){
		int mid = low + (high - low) / 2;
		int[] arr = new int[high - low + 1];
		int i = low, j = mid + 1, k = 0;
		
		while (k < arr.length){
			int num1 = i <= mid ? nums[index[i]] : Integer.MAX_VALUE;
			int num2 = j <= high ? nums[index[j]] : Integer.MAX_VALUE;
			
			arr[k++] = num1 <= num2 ? index[i++] : index[j++];
		}
		
		for (int p = 0; p < arr.length; p++) index[p+low] = arr[p];
	}
Count of Range Sum

	public int countRangeSum(int[] nums, int lower, int upper) {
        if (nums == null || nums.length == 0) return 0;
        
        long[] sums = new long[nums.length+1];
        for (int i = 1; i < sums.length; i++){
            sums[i] = nums[i-1] + sums[i-1];
        }
        
        return mergesortCountRangeSum(sums, lower, upper, 0, sums.length-1);
    }
	
	private int mergesortCountRangeSum(long[] sums, int lower, int upper, int low, int high){
		if (low >= high) return 0;
		
		int mid = low + (high - low) / 2;
		int res = mergesortCountRangeSum(sums, lower, upper, low, mid) + 
					mergesortCountRangeSum(sums, lower, upper, mid+1, high);
		int i = mid+1, j = mid+1;
		
		// Time complexity: for i or j, it could only be moved from mid+1 to high,
		// so this is a two pointer problem, not 2 loops
		for (int k = low; k <= mid; k++){
			while (i <= high && sums[i] - sums[k] < lower) i++;
			while (j <= high && sums[j] - sums[k] <= upper) j++;
			res += j - i;
		}
		
		mergeCountRangeSum(sums, low, high);
		return res;
	}
	
	private void mergeCountRangeSum(long[] sums, int low, int high){
		int mid = low + (high - low) / 2;
		int i = low, j = mid+1, k = 0;
		long[] arr = new long[high-low+1];
		
		while (k < arr.length){
			long num1 = i <= mid ? sums[i] : Long.MAX_VALUE;
			long num2 = j <= high ? sums[j] : Long.MAX_VALUE;
			
			arr[k++] = num1 <= num2 ? sums[i++] : sums[j++];
		}
		
		for (int p = 0; p < arr.length; p++) sums[p+low] = arr[p];
	}

```
