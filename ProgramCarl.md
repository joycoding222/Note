# Carl

### 160. 链表香蕉

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        // get length
        ListNode *fast = headA, *slow = headB;
        int lengthA = 0, lengthB = 0;
        while (fast != nullptr)
        {
            ++lengthA;
            fast = fast->next;
        }
        while (slow != nullptr)
        {
            ++lengthB;
            slow = slow->next;
        }

        // get Delta
        int delta = abs(lengthA - lengthB);

        // fast move first
        if (lengthA >= lengthB)
        {
            fast = headA;
            slow = headB;
        }
        else if (lengthA < lengthB)
        {
            fast = headB;
            slow = headA;
        }

        while (delta-- && fast != nullptr)
        {
            fast = fast->next;
        }

        // fast & slow move
        while (fast != slow && fast != nullptr)
        {
            fast = fast->next;
            slow = slow->next;
        }

        return fast;
    }
};
```

### 142. 环形链表Ⅱ

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        // if there is a loop
        ListNode *fast = head, *slow = head;
        while (fast != nullptr && fast->next != nullptr)
        {
            fast = fast->next->next;
            slow = slow->next;

            if (fast == slow)
            {
                // loop
                ListNode *p1 = fast, *p2 = head;
                while (p1 != p2)
                {
                    p1 = p1->next;
                    p2 = p2->next;
                }
                return p1;
            }
        }
        // no loop
        return nullptr;
    }
};
```

### 2. 两数相加

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode dummy;
        ListNode* curr = &dummy;
        int carry = 0;  // 进位

        while (l1 || l2 || carry)
        {
            if (l1)
            {
                // l1 非空
                carry += l1->val;
                l1 = l1->next;
            }
            if (l2)
            {
                // l2 非空
                carry += l2->val;
                l2 = l2->next;
            }
            curr = curr->next = new ListNode(carry % 10);
            carry /= 10;
        }

        return dummy.next;

    }
};
```

C++中的**连续赋值语法**：(`a = b = c`)

```cpp
// 连续赋值语法
curr = curr->next = new ListNode(carry % 10);

// 相当于
curr->next = new ListNode(carry % 10);	// 先
curr = curr->next;						// 后
```



### 15. 三数之和

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> ans;
        // 排序
        ranges::sort(nums);

        int n = nums.size();
        // 枚举 i
        for(int i = 0; i < n - 2; ++i)
        {
            int x = nums[i];
            // 防止重复
            if (i > 0 && nums[i] == nums[i - 1])
                continue;
            // 优化1（可省略）
            if (x + nums[i + 1] + nums[i + 2] > 0)
                break;
            // 优化2（可省略）
            if (x + nums[n - 1] + nums[n - 2] < 0)
                continue;
            // 转换成双指针两数之和
            int j = i + 1;
            int k = n - 1;
            while (j < k)
            {
                int s = x + nums[j] + nums[k];
                if (s > 0)
                    k -= 1;
                else if (s < 0)
                    j += 1;
                else
                {
                    ans.push_back({x, nums[j], nums[k]});
                    j += 1;
                    // 防止重复
                    while (j < k && nums[j] == nums[j - 1])
                        j += 1;
                    k -= 1;
                    // 防止重复
                    while (j < k && nums[k] == nums[k + 1])
                        k -= 1;
                }
            }
        }
        return ans;
    }
};
```

### 18. 四数之和

```cpp
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        // 返回值
        vector<vector<int>> ans;
        // 排序
        ranges::sort(nums);
        // 枚举第一个数
        int size = nums.size();
        for(int a = 0; a < size - 3; a++)
        {
            if(a > 0 && nums[a] == nums[a - 1])	// 避免重复
                continue;
            long x1 = nums[a];

            // 枚举第二个数
            for(int b = a + 1; b < size - 2; b++)
            {
                if (b > a + 1 && nums[b] == nums[b - 1])	// 避免重复
                    continue;
                long x2 = nums[b];
				// 双指针
                int j = b + 1;
                int k = size - 1;
                while (j < k)
                {
                    long long sum = x1 + x2 + nums[j] + nums[k];
                    if (sum > target)
                    {
                        k -= 1;
                    }
                    else if (sum < target)
                    {
                        j += 1;
                    }
                    else{
                        ans.push_back({static_cast<int>(x1), static_cast<int>(x2), nums[j], nums[k]});
                        j += 1;
                        while (j < k && nums[j] == nums[j - 1])
                            j += 1;
                        k -= 1;
                        while (j < k && nums[k] == nums[k + 1])
                            k -= 1;
                    }
                }
            }

        }
        return ans;
    }
};
```

## 栈与队列

1. 栈与队列不能使用迭代器对元素进行遍历；
2. STL 中栈常常被归类为容器适配器（container adapter）
3. 底层实现容器是deque（双向队列）

### 232. 用栈实现队列

```cpp
class MyQueue {
public:
    std::stack<int> st1;    // 入栈
    std::stack<int> st2;    // 出栈
    MyQueue() {

    }
    
    void push(int x) {
        st1.push(x);
    }
    
    int pop() {
        if (st2.empty())
        {
            // 入栈元素全部进入出栈
            while (!st1.empty())
            {
                int temp = st1.top();
                st1.pop();
                st2.push(temp);
            }
            int ret = st2.top();
            st2.pop();
            return ret;
        }
        int temp = st2.top();
        st2.pop();
        return temp;
    }
    
    int peek() {
        if (st2.empty())
        {
            // 入栈元素全部进入出栈
            while (!st1.empty())
            {
                int temp = st1.top();
                st1.pop();
                st2.push(temp);
            }
            int temp = st2.top();
            return temp;
        }
        int temp = st2.top();
        return temp;
    }
    
    bool empty() {
        return st1.empty() && st2.empty();
    }
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue* obj = new MyQueue();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->peek();
 * bool param_4 = obj->empty();
 */
```

### 225. 用队列实现栈

```cpp
class MyStack {
public:
    std::queue<int> q1;
    std::queue<int> q2;
    MyStack() {
        
    }
    
    // 正常进
    void push(int x) {
        q1.push(x);
    }
    
    int pop() {
        // q1剩一个元素，其余去q2
        int size = q1.size();
        while (--size)
        {
            q2.push(q1.front());
            q1.pop();
        }

        // q1的元素弹出
        int ret = q1.front();
        q1.pop();

        // 所有元素回到q1并清空q2
        q1 = q2;
        while (!q2.empty())
        {
            q2.pop();
        }

        return ret;
    }
    
    int top() {
        int res = this->pop();
        this->push(res);
        return res;
    }
    
    bool empty() {
        return q1.empty();
    }
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack* obj = new MyStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->top();
 * bool param_4 = obj->empty();
 */
```



# 0x3F

### 1422. 分割字符串的最大得分

```cpp
class Solution {
public:
    int maxScore(string s) {
        // 灵神写法一
        int right1 = ranges::count(s, '1'); // 字符串s中'1'的个数C++20
        // 相当于:int right1 = std::count(s.begin(), s.end(), '1');
        int ans = 0, left0 = 0;
        for (int i = 0; i + 1 < s.size(); ++i)  // i相当于左侧字符串的最大索引
        {
            if (s[i] == '0')    // 统计left0
            {
                left0++;
            }
            if (s[i] == '1')    // 统计right1
            {
                right1--;
            }
            ans = max(ans, left0 + right1);
        }
        return ans;
    }
};
```



### 2586. 统计范围内的元音字符数

```cpp
class Solution {
public:
    int vowelStrings(vector<string>& words, int left, int right) {
        const string vowel = "aeiou";
        int count = 0;
        for (int i = left; i <= right; ++i)
        {
            string& s = words[i];
            // 方法一：
            auto temp = vowel.find(s[0]);
            auto temp2 = vowel.find(s.back());
            if (temp != string::npos && temp2 != string::npos)
            {
                // 找到
                ++count;
            }
            // 方法二：
            // count += vowel.find(s[0]) != string::npos && vowel.find(s.back()) != string::npos;
        }
        return count;
    }
};
```

思路：

由题目描述，在字符串数组words中寻找：元音开头元音结尾的字符串；因此，需要遍历范围内的字符串，检查其首字符和尾字符是否为元音；由此可以想到，将问题转化为：在所有元音中，寻找每个字符串的首位字符

## 基础算法精讲

### 167. 两数之和Ⅱ

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        int left = 0, right = numbers.size() - 1;
        while (numbers[left] + numbers[right] != target)	// 题目保证一定有唯一解
        {
            if (numbers[left] + numbers[right] > target)
            {
                right -= 1;
            }
            if (numbers[right] + numbers[left] < target)
            {
                left += 1;
            }
        }
        return {left+1, right+1};
    }
};
```



# HOT100

## 哈希

### 2. 两数之和

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> idx;
        for (int j = 0; ; ++j)
        {
            auto it = idx.find(target - nums[j]);
            if (it != idx.end())
            {
                return {j, it->second};
            }
            idx[nums[j]] = j;
        }
    }
};
```

## 双指针

### 283. 移动0

```cpp
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
      for (int i = 0; i < nums.size(); ++i)
      {
        for (int j = i + 1; j < nums.size(); ++j)
        {
            if (nums[i] == 0)
            {
                swap(nums[i], nums[j]);
            }
        }
      }  
    }
};

class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int n = nums.size();
        int left = 0, right  = 0;
        while (right < n)
        {
            if (nums[right])
            {
                swap(nums[right], nums[left]);
                left++;
            }
            right++;
        }
    }
};
```

### 11. 盛水最多的容器

```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int ans = 0;
        int left = 0;
        int right = height.size() - 1;
        while (left < right)
        {
            int area = (right - left) * min(height[left], height[right]);
            ans = max(ans, area);
            if(height[left] < height[right])
            {
                left++;
            }
            else
            {
                right--;
            }
        }
        return ans;
    }
};
```

### 15. 三数之和

```cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> ans;
        // 排序
        ranges::sort(nums);

        int n = nums.size();
        // 枚举 i
        for(int i = 0; i < n - 2; ++i)
        {
            int x = nums[i];
            // 防止重复
            if (i > 0 && nums[i] == nums[i - 1])
                continue;
            // 优化1（可省略）
            if (x + nums[i + 1] + nums[i + 2] > 0)
                break;
            // 优化2（可省略）
            if (x + nums[n - 1] + nums[n - 2] < 0)
                continue;
            // 转换成双指针两数之和
            int j = i + 1;
            int k = n - 1;
            while (j < k)
            {
                int s = x + nums[j] + nums[k];
                if (s > 0)
                    k -= 1;
                else if (s < 0)
                    j += 1;
                else
                {
                    ans.push_back({x, nums[j], nums[k]});
                    j += 1;
                    // 防止重复
                    while (j < k && nums[j] == nums[j - 1])
                        j += 1;
                    k -= 1;
                    // 防止重复
                    while (j < k && nums[k] == nums[k + 1])
                        k -= 1;
                }
            }
        }
        return ans;
    }
};
```

## 链表

### 160. 相交链表

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* p1 = headA;
        ListNode* p2 = headB;
        // 计数
        int sizeA = 0, sizeB = 0;
        while (p1 != NULL)
        {
            p1 = p1->next;
            ++sizeA;
        }
        while (p2 != NULL)
        {
            p2 = p2->next;
            ++sizeB;
        }
        // 快指针先走
        p1 = headA;
        p2 = headB;
        int delta = abs(sizeA - sizeB);
        if (sizeA > sizeB)
        {
            while(delta--)
            {
                p1 = p1->next;
            }
        }
        else if(sizeA < sizeB)
        {
            while(delta--)
            {
                p2 = p2->next;
            }
        }
        // 找交点
        while (p1 != p2)
        {
            p1 = p1->next;
            p2 = p2->next;
        }
        return p1;
    }
};
```



# C

1. `std::string::npos`：静态成员变量。表示**字符串未找到目标位置**，常常和`find`方法配合使用。例如第2586题