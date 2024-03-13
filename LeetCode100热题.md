# LeetCode 100热题

## 哈希表

### 1.两数之和

双指针：

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> a;
        int n = nums.size();
        // 对a进行初始化
        for (int i = 0; i < n; i++)
            a.push_back(i);

        // 把a的下标按升序排序根据nums的元素大小
        sort(a.begin(), a.end(),
             [a, nums](int i, int j) { return nums[a[i]] < nums[a[j]]; });

        int l = 0, r = n - 1; // 双指针
        vector<int> ret;
        while (l < r) {
            if (nums[a[l]] + nums[a[r]] == target) {
                ret.push_back(a[l]);
                ret.push_back(a[r]);
                break;
            } else if (nums[a[l]] + nums[a[r]] > target) {
                r--;
            } else {
                l++;
            }
        }
        return ret;
    }
};
```

遍历：

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int n = nums.size();
        vector<int> ret;
        int i,j;
        for(i = 0;i < n - 1;i++)
            for(j = i+1;j < n;j++){
                if(nums[i] + nums[j] == target){
                    ret.push_back(i);
                    ret.push_back(j);
                    return ret;
                }
            }
        return ret;
    }
};
```

哈希表（最快）:

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int,int> hashmap;
        int n = nums.size();
        //查找target-nums[i]是否在表中
        for(int i = 0;i < n ;i++){
            auto a = hashmap.find(target - nums[i]);
            //begin()和end()相当于左闭右开的函数区间
            if(a != hashmap.end())
                return {a->second, i};
            hashmap[nums[i]] = i;
        }
        return {};
    }
};
```

### 49.字母异位词分组

哈希表：

```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> hmp;
        int n = strs.size();
        // 降重排序列一样的放到对应的key
        for (const auto& s : strs) {
            auto key = s;
            sort(key.begin(), key.end());
            hmp[key].push_back(s);
        }
        vector<vector<string>> ret;
        for (const auto& s : hmp) {
            ret.push_back(s.second);
        }
        return ret;
    }
};
```

### 128.最长连续序列

哈希表：

```c++
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_map<int, int> hmp;
        for (int x : nums)
            hmp[x] = 1;
        int ret = 0;
        for (auto& i : hmp)
            if (i.second) {
                int val = i.first,length = 1;;
                
                for (int j = 1; hmp.count(val - j) && hmp[val - j]; j++) {
                    length++;
                    hmp[val - j] = 0;
                }
                for (int j = 1; hmp.count(val + j) && hmp[val + j]; j++) {
                    length++;
                    hmp[val + j] = 0;
                }
                ret = max(ret,length);
            }
        return ret;
    }
};
```

## 双指针

### 283.移动零

```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {

        int n = nums.size();
        int l = 0, r = 1;
       
        int a = 0; // 交换次数
        while(l <= n-1 && r <= n - 1){
            if(nums[l] == 0 && nums[r] != 0){
                nums[l] = nums[r];
                nums[r] = 0;
                l++;
                r++;
            }else if((nums[l] != 0 && nums[r] != 0) || (nums[l] != 0 && nums[r] == 0)){
                l++;
                r++;
            }else if(nums[l] == 0 && nums[r] == 0){
                r++;
            }
        }
    }
};
```

```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        //双指针，index代表坑位，元素分为0和非0的，0可抢
        int index = 0;
        int n = nums.size();
        for(int i = 0;i < n;++i){
            if(nums[i])
                nums[index++] = nums[i];
        }
        while(index < n)
            nums[index++] = 0;
    }
};
```

### 11.盛水最多的容器

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int ret = 0;
        int l = 0, r = height.size() - 1;
        while (l < r) {
            ret = max(ret, min(height[l], height[r]) * (r - l));
            if (height[l] < height[r])
                l++;
            else
                r--;
        }
        return ret;
    }
};
```



### 15.三数之和

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> ret;
        int n = nums.size();
        sort(nums.begin(), nums.end());
        for (int i = 0; i < n; i++) {
            if (i > 0 && nums[i] == nums[i - 1])
                continue;
            int l = i + 1, r = n - 1;
            int target = 0 - nums[i];
            while (l < r) {
                if (nums[l] + nums[r] == target) {
                    ret.push_back({nums[i], nums[l], nums[r]});
                    while (l < r && nums[l] == nums[l + 1])
                        l++;
                    while (l < r && nums[r] == nums[r - 1])
                        r--;
                    l++, r--;
                } else if (nums[l] + nums[r] < target) {
                    l++;
                } else {
                    r--;
                }
            }
        }
        return ret;
    }
};
```

### 42.接雨水

动态规划：

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        // 能接雨水为当前柱子左右两边最大高度的最小值减去当前柱子高度
        int n = height.size();
        if (n == 0) {
            return 0;
        }

        // 左侧最大值
        vector<int> leftMax(n);
        leftMax[0] = height[0];
        for (int i = 1; i < n; i++) {
            leftMax[i] = max(leftMax[i - 1], height[i]);
        }
        // 右侧最大值
        vector<int> rightMax(n);
        rightMax[n - 1] = height[n - 1];
        for (int i = n - 2; i >= 0; --i) {
            rightMax[i] = max(rightMax[i + 1], height[i]);
        }
        int ret = 0;
        for (int i = 0; i < n; ++i) {
            ret += min(leftMax[i], rightMax[i]) - height[i];
        }
        return ret;
    }
};
```

双指针：

```C++
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        int leftMax = 0, rightMax = 0;
        int l = 0, r = n - 1;
        int ret = 0;
        while (l < r) {
            leftMax = max(leftMax, height[l]);
            rightMax = max(rightMax, height[r]);
            if (height[l] < height[r]) {
                ret += leftMax - height[l++];
            } else {
                ret += rightMax - height[r--];
            }
        }

        return ret;
    }
};
```

