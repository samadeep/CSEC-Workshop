
**Link to Session Scribbles** : 
https://drive.google.com/file/d/1YTlKpF59MmIPZv8OemIiqTmXQ3R2_xHd/view?usp=drive_link


### Two Pointers 

**Q1** : [Two Sum - Coding Ninjas](https://www.codingninjas.com/studio/problems/two-sum_839653?leftPanelTabValue=SUBMISSION)
```cpp
vector<pair<int,int>> twoSum(vector<int>& nums, int target, int n)
{
	    n = nums.size();
        int l = 0 , r = n - 1;
        
        std::sort(nums.begin(),nums.end());
        // [1 3 2]  -> [1 2 3]
        
        vector<pair<int,int>> ans;

        // for every l if we had checked r 
        // we need to decrease our choices 
        // l < r so that array size is atleast 2

        while( l < r ){
            
            int sum = nums[l] + nums[r];
            
            if( sum > target ){
                r--;
            }
            else if( sum < target ){
                l++;
            }
            else{
				
                ans.push_back({nums[l],nums[r]});
                l += 1;
				r -= 1;
            }
        }

        if( ans.size() == 0 ) ans.push_back({-1,-1});

        return ans;
}
```
### Q2 : [LC1793. Maximum Score of a Good Subarray](https://leetcode.com/problems/maximum-score-of-a-good-subarray/)
Code :
```cpp
class Solution {
public:
    int maximumScore(vector<int>& nums, int k) {

        int n = nums.size();

        int l = k , r = k;
        int score = nums[k] * 1;

        int subarray_min = nums[k];

        //     [left [ ... ]] right
        
        // in atleast one direction i can increase the length
        while( l - 1 >= 0 || r + 1 < n  ){
            
            // choose
            int left_el = l - 1 >= 0 ? nums[l-1] : 0;
            int right_el = r + 1 < n ? nums[r+1] : 0;

            // array size extend
            if( left_el > right_el ){
                subarray_min = min( subarray_min , left_el );
                l -= 1;
            }

            if( left_el <= right_el ){
                subarray_min = min( subarray_min , right_el );
                r += 1;
            }

            // update new score
            score = max( score , subarray_min * ( r - l + 1 ) );

        } 

        return score;
        
    }
};
```

**Related Questions :**
- [LC1147. Longest Chunked Palindrome Decomposition](https://leetcode.com/problems/longest-chunked-palindrome-decomposition/)


### Sliding Window
[LC209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)
```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {

        ios_base::sync_with_stdio(false);
        std::cin.tie(0);
        
        // sum is greater than or equal to target
        int n = nums.size();
        int ans = n + 1;

        int sum = 0;
        int l = 0 , r = 0;
        /*
            int start = 0;
            for( int end .... ){

                // window increase 

                // ans update 

                // window decrease -> start -= 1;
            }
        */
        
        int start = 0 ;

        for( int end = 0 ; end < n ; end++ ){
            
            sum += nums[end];

            while( sum >= target ){
                int len_subarray = end - start + 1;
                ans = min( ans , len_subarray );
                sum -= nums[start];
                start++;
            }

        }

        return ans == n + 1 ? 0 : ans;

    }
};
```


**Related Questions :**
- [LC209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)
- [LC76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)




### Priority Queue - Introduction and Applications in Scheduling

Question : [LC1882. Process Tasks Using Servers](https://leetcode.com/problems/process-tasks-using-servers/)

### Explanation 
Here, we possess two minimum heaps, organized by [time, weight, index]: one labeled "free_server" and the other "busy_server." 
Initially, we populate the available heap with all free_servers, setting their time to zero.

As we handle our tasks:

- **Transfer servers** that have completed their tasks (time <= t) from the busy heap to the free heap.
- **Select** the top server from the available heap and move it to the busy heap, setting its time to t + tasks[t]. At this point, we also log the server's index for the respective task.

- **What occurs if no servers are available?** In such a scenario, the server closest to completing its task will undertake the new task. Hence:
- We shift the **top server from the busy heap** to the available heap.

- Subsequently, we return it to the busy heap, but this time we prolong the completion time by the duration of the new task (time + task[t]). In essence, the time is set to i + task[i] when an available server is present, as opposed to time + task[i] when one is not.


Code : 
```cpp
// c++
using arr3 = array<long,3>;

// array<int,2> arr
// vector<vector<int>> 
// tuple<int,int,int> 

class Solution {
public:
    vector<int> assignTasks(vector<int>& servers, vector<int>& tasks)   {

        // p1 p2 p3 p4 ... pn
        // s1 , s2 , s3 , s4 .....
        
        // [ w1 , i1 ] , [ w2 , i2 ] , [ w3 , i3 ]
        // pq -> [ [time] , [wt] , [index] ]
        // min_heap
        
        // pq -> which server is available ???
        // time -> most recent free server 
        // same time -> wt1 , wt2  
        // same wt -> min index

        priority_queue< arr3 , vector<arr3> , greater<arr3>> free_servers , busy_servers;

        // intially all are free servers
        
        int index = 0;
        // decomposition declaration
        for( auto server : servers ){
            free_servers.push({0,server, index});
            index += 1;
        }
        

        int n_tasks = tasks.size();
        vector<int> ans(n_tasks);

        for( int i = 0 ; i < n_tasks ; i++ ){

            int arrival = i;
            
            // take the busy servers who have completed their tasks
            while( !busy_servers.empty() && ( busy_servers.top()[0] <= arrival || free_servers.empty() )  )
            {
                auto [ free_time , wt , index ] = busy_servers.top();// t , w , i
                busy_servers.pop();
                int time_free = free_time <= arrival ? 0 : free_time; // now or when it becomes free
                free_servers.push({time_free,wt, index});

            }


            // assign
            auto [ free_time , wt , index ] = free_servers.top(); 
            free_servers.pop();

            ans[i] = index;

            int time_free = max( (long)arrival , free_time  ) + tasks[i];

            busy_servers.push( { time_free , wt , index } );

        
        }

        return ans;
        
    }
};
```


**Related Questions :**
- [767. Reorganize String](https://leetcode.com/problems/reorganize-string/)
- [1834. Single-Threaded CPU](https://leetcode.com/problems/single-threaded-cpu/)
- [1882. Process Tasks Using Servers](https://leetcode.com/problems/process-tasks-using-servers/)
- [621. Task Scheduler](https://leetcode.com/problems/task-scheduler/)
