# codepratice-day6
代码打卡思考笔记6
代码随想录打卡第6次

今天的内容是后打卡的，因为得肠胃炎了。
内容需要多复习，之后病好了看吧。
哈希表、双指针。

[四数相加II](https://leetcode.cn/problems/4sum-ii/description/)
哈希表得用法，先将前两个数组得数加起来，作为哈希表的key，再找后两个数组相加*-1这个值是否在哈希表中存在。
存在加上出现的次数就行。

```CPP
class Solution {
public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
        unordered_map<int,int> umap;
        for(int i=0;i<nums1.size();i++)
        {
            for(int j=0;j<nums2.size();j++)
            {
                umap[nums1[i]+nums2[j]]+=1;
            }
        }

        int count=0;
        for(int i=0;i<nums3.size();i++)
        {
            for(int j=0;j<nums4.size();j++)
            {
                if(umap.find(-(nums3[i]+nums4[j]))!=umap.end())
                {
                    count+=umap[-(nums3[i]+nums4[j])];
                }
            }
        }
        return count;
    }
};
```

[赎金信](https://leetcode.cn/problems/ransom-note/description/)
思路简单，用数组模拟哈希表，两次循环判断，是否出现的字母数足够覆盖目标。

```CPP
class Solution {
public:
    bool canConstruct(string ransomNote, string magazine) {
        int record[26]={0};
        if(ransomNote.length()>magazine.length())
        {
            return false;
        }

        for(int i=0;i<magazine.length();i++)
        {
            record[magazine[i]-'a']++;
        }

        for(int i=0;i<ransomNote.length();i++)
        {
            record[ransomNote[i]-'a']--;
            if(record[ransomNote[i]-'a']<0)
                return false;
            
        }
        return true;

    }
};
```

[三数之和](https://leetcode.cn/problems/3sum/description/)
这道题和四数之和都挺难，正解用双指针，其中去重是难点。三数之和也可以用哈希表。
哈希表法
```CPP
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        //在一个数组中找到3个数形成的三元组，它们的和为0，不能重复使用（三数下标互不相同），且三元组不能重复。
        //b(存储)==0-（a+c）（检索）
        vector<vector<int>> result;
        sort(nums.begin(),nums.end());

        for(int i=0;i<nums.size();i++)
        {
            //如果a是证书，a<b<c，不可能形成和为0的三元组
            if(nums[i]>0)
                break;
            
            //[a,a,...]如果本轮a和上轮a相同，那么找到的b,c也是相同的，所以去重a
            if(i>0&&nums[i]==nums[i-1])
                continue;
            
            //这个set的作用是存储b
            unordered_set<int> set;

            for(int k=i+1;k<nums.size();k++)
            {
                //去重b=c时的b和c
                if(k>i+2&&nums[k]==nums[k-1]&&nums[k-1]==nums[k-2])
                {
                    continue;
                }
                //a+b+c=0=>b=0-(a+c)
                int target=0-(nums[i]+nums[k]);
                if(set.find(target)!=set.end())
                {
                    result.push_back({nums[i],target,nums[k]});
                    set.erase(target);
                }
                else
                {
                    set.insert(nums[k]);
                }
            }
            
        }
        return result;
    }
};
```

双指针法
这里也有个疑问，就是leetcode题解有点不同。没细看
```CPP
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> result;
        sort(nums.begin(),nums.end());

        for(int i=0;i<nums.size();i++)
        {
            if(nums[i]>0)
                return result;
            
            if(i>0&&nums[i]==nums[i-1])
                continue;
            
            int left=i+1;
            int right=nums.size()-1;
            while(right>left)
            {
                if(nums[i]+nums[left]+nums[right]>0) right--;
                else if(nums[i]+nums[left]+nums[right]<0) left++;
                else
                {
                    result.push_back(vector<int>{nums[i],nums[left],nums[right]});
                    while(right>left&&nums[right]==nums[right-1]) right--;
                    while(right>left&&nums[left]==nums[left+1]) left++;

                    right--;
                    left++;
                }
            }
        }
        return result;
    }
};
```

[四数之和](https://leetcode.cn/problems/4sum/submissions/593490618/)
这道题和三数之和类似，用双指针法，将时间复杂度降一个维度。这道题和三数之和后面需要背下来。

```CPP
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        vector<vector<int>> result;
        sort(nums.begin(),nums.end());
        for(int k=0;k<nums.size();k++)
        {
            //剪枝
            if(nums[k]>target&&nums[k]>=0)
            {
                break;
            }

            if(k>0&&nums[k]==nums[k-1])
            {
                continue;
            }

            for(int i=k+1;i<nums.size();i++)
            {
                //2级剪枝处理
                if(nums[k]+nums[i]>target&&nums[k]+nums[i]>=0)
                {
                    break;
                }

                if(i>k+1&&nums[i]==nums[i-1])
                {
                    continue;
                }

                int left=i+1;
                int right=nums.size()-1;
                while(right>left)
                {
                    if((long)nums[k]+nums[i]+nums[left]+nums[right]>target)
                    {
                        right--;
                    }
                    else if((long)nums[k]+nums[i]+nums[left]+nums[right]<target)
                    {
                        left++;
                    }
                    else
                    {
                        result.push_back(vector<int>{nums[k],nums[i],nums[left],nums[right]});
                        //去重
                        while(right>left&&nums[right]==nums[right-1]) right--;
                        while(right>left&&nums[left]==nums[left+1]) left++;

                        right--;
                        left++;
                    }
                }
            }
        }
        return result;
    }
};
```
