### `wstring` 与 `string` 的互相转换

例题为[牛客-华为机试-HJ95-人民币转换](https://www.nowcoder.com/practice/00ffd656b9604d1998e966d555005a4b?tpId=37&tqId=21318&rp=1&ru=/exam/oj/ta&qru=/exam/oj/ta&sourceUrl=%2Fexam%2Foj%2Fta%3Fdifficulty%3D4%26page%3D1%26pageSize%3D50%26search%3D%26tpId%3D37%26type%3D37&difficulty=4&judgeStatus=undefined&tags=&title=)
```C++
#include<iostream>
#include<vector>
#include<string>
#include<algorithm>
#include<iomanip>
#include<codecvt>
using namespace std;

vector<wstring> CN_number {L"零", L"壹", L"贰", L"叁", L"肆", L"伍", L"陆", L"柒", L"捌", L"玖"};
vector<wstring> CN_danwei1 {L"", L"拾", L"佰", L"仟"};
vector<wstring> CN_danwei2 {L"万", L"亿"};

wstring toChinese(char c) {
    return CN_number.at(c - '0');
}

wstring rmb(double money) {
    stringstream ss;
    ss << std::fixed << std::setprecision(2) << money; // 最小为x分钱，所以精度设为2
    wstring res; // 处理中文要用wstring
    string m = ss.str(); // string化的money

    auto it = m.rbegin(); // 本算法从最小位计算
    int loc = 0, loc1 = 0, loc2 = 0; // 整数部分位数，每四位中和第几位，第几个四位
    
    // 先判断小数部分
    if (*it == '0' && *(it+1) == '0')
        res = L"整元";
    else {
        if (*it != '0') 
            res = L"分" + toChinese(*it);
        
        if (*(it + 1) != '0') 
            res += L"角" + toChinese(*(it+1));    
    }
        
    it += 3; // 一定有整数部分，所以安全
    
    if (it + 1 != m.rend() || *it != '0') // 如果超过10块钱 或 个位数不为0
        res += L"元";
    
    int zero = 0; // 每四位中‘0’的个数
    for (; it != m.rend(); it++) {
        if (*it == '0') {
            zero++;
        }
        
        if (*it == '1' && loc1 == 1) { // xx1x，即没有“壹拾”的说法，此时省略“壹”字
            res += CN_danwei1.at(loc1);
        } else if (loc < 6 && loc1 > 0 && loc1 < 3 && zero && // 十位与百位出现‘0’时
            res.substr(res.size() - 1, 1) != L"零") // 只能有一个“零”字
        {
            res += toChinese(*it);
        } else if (*it != '0') { // 如果当前值不为0，才要加上该数及单位
            res += CN_danwei1.at(loc1) + toChinese(*it);
        }
        
        // 进入下一次循环的索引变化
        loc++; 
        loc1 = (loc1 + 1) % 4;
        if (!loc1 && (it + 1) != m.rend()) {
            res += CN_danwei2.at(loc2); // 加上 “万”或“亿”字
            loc2 = (loc2 + 1) % 2;
            zero = 0; // 重置计数
        }   
    }
    
    res += L"币民人";
    reverse(res.begin(), res.end()); // 将反向的字符串正置过来
    
    return res;
}

int main(){
    double money;
    cin >> money;
    wstring ws = rmb(money);
    wstring_convert<codecvt_utf8<wchar_t>> cvt; // #include <codecvt>
    string s = cvt.to_bytes(ws); // wstring 转换为 string 输出
    cout << s << endl;
    
    return 0;
}
```