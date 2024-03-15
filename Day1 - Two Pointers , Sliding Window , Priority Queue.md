
**Link to Session Scribbles** : 
https://drive.google.com/file/d/1YTlKpF59MmIPZv8OemIiqTmXQ3R2_xHd/view?usp=drive_link


### Two Pointers 

**Link** : https://www.codingninjas.com/studio/problems/two-sum_839653?leftPanelTabValue=SUBMISSION
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


**Related Questions :**
- [1147. Longest Chunked Palindrome Decomposition](https://leetcode.com/problems/longest-chunked-palindrome-decomposition/)


### Sliding Window
[209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)
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
- [1793. Maximum Score of a Good Subarray](https://leetcode.com/problems/maximum-score-of-a-good-subarray/)
- [209. Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/)
- [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)



### Priority Queue - Introduction and Applications in Scheduling

Question : [1882. Process Tasks Using Servers](https://leetcode.com/problems/process-tasks-using-servers/)

Here, we have two min heaps, ordered by [time, weight, index]: available and busy. First, we put all servers into the available heap with time equals to zero.

As we process our tasks:

Move servers that have finished (time <= t) from busy to avail.
Pick the top server from the avail heap and move it to the busy heap with time = t + tasks[t].
This is where we record the index of the server for that task.
What happens if no servers are available? In this case, the server that is closest to finishing its task will pick up the new task. So:

We move the top server from busy to avail heap.
Then we move it back to the busy heap, but this time we extend the completion time for the duration of the new task time + task[t].
In other words, the time is t + task[t] if we have an available server vs. time + task[t].


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
