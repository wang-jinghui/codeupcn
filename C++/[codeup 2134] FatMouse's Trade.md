问题 E:2134 FatMouse's Trade
--------------------------

题目描述
FatMouse prepared M pounds of cat food, ready to trade with the cats guarding the warehouse containing his favorite food, JavaBean.
The warehouse has N rooms. The i-th room contains J[i] pounds of JavaBeans and requires F[i] pounds of cat food. FatMouse does not have to trade for all the JavaBeans in the room, instead, he may get J[i]* a% pounds of JavaBeans if he pays F[i]* a% pounds of cat food. Here a is a real number. Now he is assigning this homework to you: tell him the maximum amount of JavaBeans he can obtain. 


输入
The input consists of multiple test cases. Each test case begins with a line containing two non-negative integers M and N. Then N lines follow, each contains two non-negative integers J[i] and F[i] respectively. The last test case is followed by two -1's. All integers are not greater than 1000.


输出
For each test case, print in a single line a real number accurate up to 3 decimal places, which is the maximum amount of JavaBeans that FatMouse can obtain.


样例输入

```
4 2
4 7
1 3
5 5
4 8
3 8
1 2
2 5
2 4
-1 -1
```

样例输出

```
2.286
2.500
```
题目比较简单，计算老鼠能够用猫粮交换到最多的javabeans

```C++
#include <iostream>
#include <algorithm>
using namespace std;
struct L {
    double J, F, p;   // J：房间中的javabeans; F: 交换所需的猫粮，p,单位猫粮能交换的咖啡豆
};
bool cmp(L a, L b) {
    return a.p > b.p;
}
int main() {
    int M, N;
    while(cin >> M >> N) {
        if (M == -1 && N == -1) break;
        L list[N];
        double J, F;
        for (int i = 0; i < N; i++) {
            cin >>J >>F;
            list[i].J = J;
            list[i].F = F;
            list[i].p = J/F;
        }
        sort(list, list+N, cmp);　// P 从大到小排序
        double ans = 0;
        for (int i = 0; i < N; i++) {
            if (M <= list[i].F) {
                ans += M * list[i].p;
                printf("%.3f\n",ans);
                break;
            }
            else {
                ans += list[i].p * list[i].F;
                M -= list[i].F;
            }
        }
    }
    return 0;
}
```