
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
