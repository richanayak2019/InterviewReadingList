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


## 850. Rectangle Area II 
#### https://leetcode.com/articles/rectangle-area-ii/#

### Approach #2: Coordinate Compression
Intuition

Image from problem description
Suppose instead of rectangles = [[0,0,2,2],[1,0,2,3],[1,0,3,1]], we had [[0,0,200,200],[100,0,200,300],[100,0,300,100]]. The answer would just be 100 times bigger.

What about if rectangles = [[0,0,2,2],[1,0,2,3],[1,0,30002,1]] ? Only the blue region would have area 30000 instead of 1.

Our idea is this: we'll take all the x and y coordinates, and re-map them to 0, 1, 2, ... etc. For example, if rectangles = [[0,0,200,200],[100,0,200,300],[100,0,300,100]], we could re-map it to [[0,0,2,2],[1,0,2,3],[1,0,3,1]]. Then, we can solve the problem with brute force. However, each region may actually represent some larger area, so we'll need to adjust for that at the end.

Algorithm

Re-map each x coordinate to 0, 1, 2, .... Independently, re-map all y coordinates too.

We then have a problem that can be solved by brute force: for each rectangle with re-mapped coordinates (rx1, ry1, rx2, ry2), we can fill the grid grid[x][y] = True for rx1 <= x < rx2 and ry1 <= y < ry2.

Afterwards, each grid[rx][ry] represents the area (imapx(rx+1) - imapx(rx)) * (imapy(ry+1) - imapy(ry)), where if x got remapped to rx, then imapx(rx) = x ("inverse-map-x of remapped-x equals x"), and similarly for imapy.
```

class Solution {
    public int rectangleArea(int[][] rectangles) {
        int N = rectangles.length;
        Set<Integer> Xvals = new HashSet();
        Set<Integer> Yvals = new HashSet();

        for (int[] rec: rectangles) {
            Xvals.add(rec[0]);
            Xvals.add(rec[2]);
            Yvals.add(rec[1]);
            Yvals.add(rec[3]);
        }

        Integer[] imapx = Xvals.toArray(new Integer[0]);
        Arrays.sort(imapx);
        Integer[] imapy = Yvals.toArray(new Integer[0]);
        Arrays.sort(imapy);

        Map<Integer, Integer> mapx = new HashMap();
        Map<Integer, Integer> mapy = new HashMap();
        for (int i = 0; i < imapx.length; ++i)
            mapx.put(imapx[i], i);
        for (int i = 0; i < imapy.length; ++i)
            mapy.put(imapy[i], i);

        boolean[][] grid = new boolean[imapx.length][imapy.length];
        for (int[] rec: rectangles)
            for (int x = mapx.get(rec[0]); x < mapx.get(rec[2]); ++x)
                for (int y = mapy.get(rec[1]); y < mapy.get(rec[3]); ++y)
                    grid[x][y] = true;

        long ans = 0;
        for (int x = 0; x < grid.length; ++x)
            for (int y = 0; y < grid[0].length; ++y)
                if (grid[x][y])
                    ans += (long) (imapx[x+1] - imapx[x]) * (imapy[y+1] - imapy[y]);

        ans %= 1_000_000_007;
        return (int) ans;
    }
}
Complexity Analysis

Time Complexity: O(N^3)O(N 
3
 ), where NN is the number of rectangles.

Space Complexity: O(N^2)O(N 
2
 ). 




```
### nlogn approach
Approach #3: Line Sweep
Intuition

Imagine we pass a horizontal line from bottom to top over the shape. We have some active intervals on this horizontal line, which gets updated twice for each rectangle. In total, there are 2 * N2∗N events, and we can update our (up to NN) active horizontal intervals for each update.

Algorithm

For a rectangle like rec = [1,0,3,1], the first update is to add [1, 3] to the active set at y = 0, and the second update is to remove [1, 3] at y = 1. Note that adding and removing respects multiplicity - if we also added [0, 2] at y = 0, then removing [1, 3] at y = 1 will still leave us with [0, 2] active.

This gives us a plan: create these two events for each rectangle, then process all the events in sorted order of y. The issue now is deciding how to process the events add(x1, x2) and remove(x1, x2) such that we are able to query() the total horizontal length of our active intervals.

We can use the fact that our remove(...) operation will always be on an interval that was previously added. Let's store all the (x1, x2) intervals in sorted order. Then, we can query() in linear time using a technique similar to a classic LeetCode problem, Merge Intervals.
```
class Solution {
    public int rectangleArea(int[][] rectangles) {
        int OPEN = 0, CLOSE = 1;
        int[][] events = new int[rectangles.length * 2][];
        int t = 0;
        for (int[] rec: rectangles) {
            events[t++] = new int[]{rec[1], OPEN, rec[0], rec[2]};
            events[t++] = new int[]{rec[3], CLOSE, rec[0], rec[2]};
        }

        Arrays.sort(events, (a, b) -> Integer.compare(a[0], b[0]));

        List<int[]> active = new ArrayList();
        int cur_y = events[0][0];
        long ans = 0;
        for (int[] event: events) {
            int y = event[0], typ = event[1], x1 = event[2], x2 = event[3];

            // Calculate query
            long query = 0;
            int cur = -1;
            for (int[] xs: active) {
                cur = Math.max(cur, xs[0]);
                query += Math.max(xs[1] - cur, 0);
                cur = Math.max(cur, xs[1]);
            }

            ans += query * (y - cur_y);

            if (typ == OPEN) {
                active.add(new int[]{x1, x2});
                Collections.sort(active, (a, b) -> Integer.compare(a[0], b[0]));
            } else {
                for (int i = 0; i < active.size(); ++i)
                    if (active.get(i)[0] == x1 && active.get(i)[1] == x2) {
                        active.remove(i);
                        break;
                    }
            }

            cur_y = y;
        }

        ans %= 1_000_000_007;
        return (int) ans;
    }
}

```


## count of smaller than self

The basic idea is to do merge sort to nums[]. To record the result, we need to keep the index of each number in the original array. So instead of sort the number in nums, we sort the indexes of each number.
Example: nums = [5,2,6,1], indexes = [0,1,2,3]
After sort: indexes = [3,1,0,2]

While doing the merge part, say that we are merging left[] and right[], left[] and right[] are already sorted.

We keep a rightcount to record how many numbers from right[] we have added and keep an array count[] to record the result.

When we move a number from right[] into the new sorted array, we increase rightcount by 1.

When we move a number from left[] into the new sorted array, we increase count[ index of the number ] by rightcount.

```

int[] count;
public List<Integer> countSmaller(int[] nums) {
    List<Integer> res = new ArrayList<Integer>();     

    count = new int[nums.length];
    int[] indexes = new int[nums.length];
    for(int i = 0; i < nums.length; i++){
    	indexes[i] = i;
    }
    mergesort(nums, indexes, 0, nums.length - 1);
    for(int i = 0; i < count.length; i++){
    	res.add(count[i]);
    }
    return res;
}
private void mergesort(int[] nums, int[] indexes, int start, int end){
	if(end <= start){
		return;
	}
	int mid = (start + end) / 2;
	mergesort(nums, indexes, start, mid);
	mergesort(nums, indexes, mid + 1, end);
	
	merge(nums, indexes, start, end);
}
private void merge(int[] nums, int[] indexes, int start, int end){
	int mid = (start + end) / 2;
	int left_index = start;
	int right_index = mid+1;
	int rightcount = 0;    	
	int[] new_indexes = new int[end - start + 1];

	int sort_index = 0;
	while(left_index <= mid && right_index <= end){
		if(nums[indexes[right_index]] < nums[indexes[left_index]]){
			new_indexes[sort_index] = indexes[right_index];
			rightcount++;
			right_index++;
		}else{
			new_indexes[sort_index] = indexes[left_index];
			count[indexes[left_index]] += rightcount;
			left_index++;
		}
		sort_index++;
	}
	while(left_index <= mid){
		new_indexes[sort_index] = indexes[left_index];
		count[indexes[left_index]] += rightcount;
		left_index++;
		sort_index++;
	}
	while(right_index <= end){
		new_indexes[sort_index++] = indexes[right_index++];
	}
	for(int i = start; i <= end; i++){
		indexes[i] = new_indexes[i - start];
	}
}

```

## python  count smaller than self 

#### segment tree , bit and divide and conquore approach

```
# Insertion Sort Method
class Solution(object):
    def countSmaller(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        visited_nums = []
        res = []
        for i in xrange(len(nums) - 1,- 1,- 1):
            idx = self.first_larger_or_equal(visited_nums,nums[i])
            res.append(idx)
        
        return res[:: - 1]
    
    def first_larger_or_equal(self,nums,target):
        if not nums:
            nums.append(target)
            return 0
        l,r = 0,len(nums) - 1
        while l + 1 < r:
            mid = l + (r - l) / 2
            if nums[mid] > target:
                r = mid
            elif nums[mid] == target:
                r = mid
            else:
                l = mid
        if nums[l] >= target:
            nums.insert(l,target)
            return l
        if nums[r] >= target:
            nums.insert(r,target)
            return r
        nums.append(target)
        return len(nums) - 1

# Divide Conquer Method
class Solution(object):
    def countSmaller(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        if not nums:
            return []
        res = [0 for _ in xrange(len(nums))]
        self.check(nums,res,0,len(nums) - 1)
        return res
    
    def check(self,nums,res,start,end):
        if start >= end:
            return [start]
        mid = (start + end) / 2
        left = self.check(nums,res,start,mid)
        right = self.check(nums,res,mid + 1,end)
        new_nums = []
        idx_l,idx_r = 0,0
        while idx_l <= mid - start and idx_r <= end - mid - 1:
            if nums[left[idx_l]] > nums[right[idx_r]]:
                res[left[idx_l]] += end - mid - 1 - idx_r + 1
                new_nums.append(left[idx_l])
                idx_l += 1
                
            else:
                new_nums.append(right[idx_r])
                idx_r += 1
                
        while idx_l <= mid - start:
            new_nums.append(left[idx_l])
            idx_l += 1
        while idx_r <= end - mid - 1:
            new_nums.append(right[idx_r])
            idx_r += 1
        return new_nums      

# Segment Tree Method
class TreeNode(object):
    def __init__(self,start,end):
        self.start,self.end = start,end
        self.left,self.right = None,None
        self.val = 0
    
class Solution(object):
    def countSmaller(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        nums_unique = list(set(nums))
        nums_sorted = sorted(nums_unique)
        num_to_idx = {num:i + 1 for i,num in enumerate(nums_sorted)}
        
        root = self.build(1,len(nums_unique))
        res = []
        
        for i in xrange(len(nums) - 1,- 1,- 1):
            self.modify(root,num_to_idx[nums[i]])
            res.append(self.query(root,1,num_to_idx[nums[i]] - 1))
        
        return res[:: - 1]
    
    def build(self,start,end):
        if start > end:
            return 
        
        root = TreeNode(start,end)
        if start == end:
            return root
        mid = (start + end) / 2
        root.left = self.build(start,mid)
        root.right = self.build(mid + 1,end)
        return root
    
    def modify(self,node,idx):
        start,end = node.start,node.end
        if start == end:
            node.val += 1
            return
        
        mid = (start + end) / 2
        if idx <= mid:
            self.modify(node.left,idx)
        if idx > mid:
            self.modify(node.right,idx)
            
        node.val = node.left.val + node.right.val
        
    
    def query(self,node,l,r):
        start,end = node.start,node.end
        if r < start or l > end:
            return 0
        if l == start and r == end:
            return node.val 
        
        mid = (start + end) / 2
        if r <= mid:
            return self.query(node.left,max(l,start),r)
        elif l > mid:
            return self.query(node.right,l,min(r,end))
        else:
            return self.query(node.left,max(l,start),mid) + self.query(node.right,mid + 1,min(r,end))    
        
# Binary Indexed Tree Method
class Solution(object):
    def countSmaller(self, nums):
        """
        :type nums: List[int]
        :rtype: List[int]
        """
        nums_unique = list(set(nums))
        nums_sorted = sorted(nums_unique)
        num_to_idx = {num:i + 1 for i,num in enumerate(nums_sorted)}
        
        bit = [0 for _ in xrange(len(nums_unique) + 1)]
        res = []
        for i in xrange(len(nums) - 1,- 1,- 1):
            self.update(bit,num_to_idx[nums[i]])
            res.append(self.query(bit,num_to_idx[nums[i]] - 1))
        
        return res[:: - 1]
    
    def update(self,bit,num):
        while num <= len(bit) - 1:
            bit[num] += 1
            num += self.lowbit(num)
    
    def query(self,bit,num):
        res = 0
        while num > 0:
            res += bit[num]
            num -= self.lowbit(num)
        return res
    
    def lowbit(self,num):
        return num & - num 
        
	
```
