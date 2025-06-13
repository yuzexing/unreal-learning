# 394. 字符串解码

### 初次提交

```
class Solution {
public:
    string decodeString(string s) {
        // 创建一个栈
        // 遍历s，遇到非右括号则入栈
        // 遇到右括号，则出栈直到遇到左括号
        // 遇到数字k则重复k次括号内
        // isdigit(s[0]); 或者判断 '0' < char < '9'
        vector<char> stk;
        stk.reserve(s.size());
        for (int i = 0; i < s.size(); i++) {
            if (s[i] == ']') {
                string target = "";
                string res = "";
                string digit = "";
                int t = 0;
                int times = 0;
                while (!stk.empty() && times < 2) {
                    char c = stk.back();
                    if (isdigit(c)) {
                        digit += c;
                        stk.pop_back();
                    }
                    else if (c == '[') {
                        times++;
                        if (times < 2) {
                            stk.pop_back();
                        }
                    }
                    else if (!isdigit(c) && digit == "") {
                        target += c;
                        stk.pop_back();
                    }
                    else {
                        break;
                    }
                }
                std::reverse(digit.begin(), digit.end());
                t = std::stoi(digit);
                while (t > 0) {
                    res += target;
                    t--;
                }
                for (int j = res.size() - 1; j >= 0; j--) {
                    stk.push_back(res[j]);
                }
            }
            else {
                stk.push_back(s[i]);
            }
        }
        string res(stk.begin(), stk.end());
        return res;
    }
};
```

### 结果
![image](https://github.com/user-attachments/assets/ca1a7698-b3fb-4f44-825f-3135dbdcde03)


### 分析
时间复杂度O(n), 空间复杂度O(n), n为解码后的字符串长度


