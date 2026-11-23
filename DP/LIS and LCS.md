## LIS（LC300）：

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        // 方法一：dp
        int n = nums.size();
        vector<int> dp(n,1);
        int ans = 1;

        for(int i = 1;i < n;++i){
            for(int j = 0;j < i;++j){
                if(nums[j] < nums[i]) dp[i] = max(dp[i], dp[j]+1);
            }

            ans = max(ans,dp[i]);
        }

        return ans;

        
        // 方法二：贪心+二分
        int n = nums.size();
        vector<int> tails;

        for(int& num : nums){
            auto it = ranges::lower_bound(tails,num);

            if(it == tails.end()) tails.emplace_back(num);
            else *it = num;
        }

        return tails.size();
    }
};
```





## LIS 变例 + Dilworth 定理（洛谷1020）

```cpp
#include <bits/stdc++.h>
using namespace std;

int z = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

vector<int> nums;
int x;

int main() {
    while (cin >> x) nums.emplace_back(x);

    vector<int> a;
    vector<int> b;			//Dilworth 定理：链覆盖数 = 最长反链
    for (int& num : nums) {
        auto it1 = upper_bound(a.begin(), a.end(), num, greater<int>()) - a.begin();
        auto it2 = lower_bound(b.begin(), b.end(), num) - b.begin();
        
        if (it1 == a.size()) a.emplace_back(num);
        else a[it1] = num;
        if (it2 == b.size()) b.emplace_back(num);
        else b[it2] = num;
    }
    cout << a.size() << '\n' << b.size();
}
```



## LCS 转化为 LIS（洛谷1439）：

```cpp
#include <bits/stdc++.h>
using namespace std;

int z = []{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    return 0;
}();

int lengthOfLIS(vector<int>& nums) {
    vector<int> tails;

    for(int i = 1; i < nums.size(); ++i){
        auto it = ranges::lower_bound(tails,nums[i]);

        if(it == tails.end()) tails.emplace_back(nums[i]);
        else *it = nums[i];
    }

    return tails.size();
}

vector<int> nums1, nums2;
int n;
unordered_map<int, int> pos;

int main() {
    cin >> n;
    nums1.resize(n + 1), nums2.resize(n + 1);
    for (int i = 1; i <= n; ++i) {
        cin >> nums1[i];
        pos[nums1[i]] = i;
    }
    for (int i = 1; i <= n; ++i) {
        cin >> nums2[i];
        nums2[i] = pos[nums2[i]];
    }
    
    cout << lengthOfLIS(nums2);
}
```



