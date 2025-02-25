问题 C:2031 To Fill or Not to Fill
--------------------------------

题目描述
With highways available, driving a car from Hangzhou to any other city is easy. But since the tank capacity of a car is limited, we have to find gas stations on the way from time to time. Different gas station may give different price. You are asked to carefully design the cheapest route to go.

输入
Each input file contains one test case. For each case, the first line contains 4 positive numbers: Cmax (<= 100), the maximum capacity of the tank; D (<=30000), the distance between Hangzhou and the destination city; Davg (<=20), the average distance per unit gas that the car can run; and N (<= 500), the total number of gas stations. Then N lines follow, each contains a pair of non-negative numbers: Pi, the unit gas price, and Di (<=D), the distance between this station and Hangzhou, for i=1,...N. All the numbers in a line are separated by a space.

输出
For each test case, print the cheapest price in a line, accurate up to 2 decimal places. It is assumed that the tank is empty at the beginning. If it is impossible to reach the destination, print "The maximum travel distance = X" where X is the maximum possible distance the car can run, accurate up to 2 decimal places.

输入：
---

```
首行，四个参数分别是：
油箱容量C<= 100;
目的地据出发点的距离D<=30000;
使用每单位的汽油行驶平均距离Davg<=20;
加油站的数量：N<=500;
接下来输入N行，每行包含两个非负数
Pi:该加油站的汽油价格，Di,该站据出发点的距离
```

**样例输入**
给定若干加油站信息，问能否驾驶汽车行驶一定的距离（起点油箱为空）
如果能够行驶完全程，则计算最小花费。若不能行驶完全程，则最远能够行驶多长距离。

```
59 525 19 2
3.00 314
3.00 0
```

样例输出

```
82.89
```

**提示**

拿到这一题，首先判断汽车是否能够行驶到终点。什么情况下汽车无法行驶到终点呢？两种情况：起点根本就没有加油站，汽车无法启动；或者中途两个加油站之间的距离大于加满油后汽车能够行驶的最大距离。前者汽车行驶的最大距离为0.00，而后者最大距离为当前加油站的距离加上在这个加油站加满油后能够行驶的最大距离。在这里，需要将加油站按到杭州的距离从小到大排序。
１．先判断能否到达目的地
２．如果可以走完全程，遍历排序后的每个站点
３．每个站点，sta[i].d~~sta[i]+maxl,范围内寻找价格低于当前站点的加油站．maxl = 汽车加满油行驶距离

- 如果找到，检查邮箱中此时剩余的油(nowTank)能否到达该站点,如果能到达该站点，就继续前进
    - 如果nowTank中的油不能到达该站点，则在当前站点加适量的油，刚好行驶到该站点
    - 到达后，继续在当前站点nowSta,寻找价格便宜的加油站
- 如果在sta[i].d ~ sta[i].d+maxl,内没有比当前更便宜的，则在但前站点加满油，行驶到下个站点后继续寻找

结构体存储各个站点信息
```
struct Gastation{　　
    double p, d, s;　　　//p : 价格，d　：据出发点距离，s　：离上个站点的间距
};

bool cmp(Gastation a, Gastation b) {
    return a.d < b.d;　　//站点按距离从小到大排序
}
```
计算站点之间的间距，判断能否走完全程
```
for (int i = 1; i <= N; i++) {  
        sta[i].s = sta[i].d - sta[i-1].d;       
    }
```

单点测试
----

```C++
#include <iostream>
#include <algorithm>
using namespace std;
struct Gastation{
    double p, d, s;
};
bool cmp(Gastation a, Gastation b) {
    return a.d < b.d;
}
int main() {
    double C, D, Davg;
    int N;
    cin >> C >> D >> Davg >>N;
    Gastation sta[N+1];
    double maxl = C*Davg;　　　　// 加满油最大行程
    for (int i = 0; i < N; i++) {
        cin>>sta[i].p;
        cin>>sta[i].d;
    }
    sta[N].p = 0;
    sta[N].d = D;　　　　　　　　//　把终点当作最后一个站点
    sort(sta, sta+N+1, cmp);
    for (int i = 1; i <= N; i++) {
        sta[i].s = sta[i].d - sta[i-1].d;       // 相邻站点间的间距
    }
    int flag = 1;　　　　          //　表示可以走完全程
    if (sta[0].d > 0) {　　　      // 如果第一个加油站不在起点
        flag = 0;
        printf("The maximum travel distance = 0.00\n");
        return 0;
    }
    else {
        for (int i = 1; i <= N; i++) {
            if (sta[i].s > maxl) {　　　// 如果某连个站点的距离大于maxl
                flag = 0;
                printf("The maximum travel distance = %.2f\n",sta[i-1].d+maxl);
                return 0;
            }
        }
    }
    double cost = 0, nowTank = 0;   // cost: 总花费，nowTank:邮箱剩余油量
    int nowSta = 0, signal = 1;　　　// nowSat:当前站点，signal = 1在范围内找到加油站
    if (flag) {
        for (int i = 0; i < N; i++) {
            if (i != nowSta) continue;　　　　// 某些站点被跳过
            for (int j = i + 1; j <= N && (sta[j].d - sta[i].d) <= maxl; j++) {
                if (sta[j].p < sta[nowSta].p) {
                　　　// 当前剩余油量，不能走到下个站点
                    if (nowTank < (sta[j].d - sta[nowSta].d)/Davg) {
                        cost += sta[nowSta].p * ((sta[j].d - sta[nowSta].d) / Davg - nowTank);
                        nowTank = 0;　　　//因为加的油刚刚够，所以到达下个站点邮箱空
                    }
                    else nowTank -= (sta[j].d - sta[nowSta].d)/Davg;
                    nowSta = j;　　　　　　// 更新站点位置
                    signal = 1;
                    break;　　　　　　　　　// 推出循环从新的站点继续寻找
                } else signal = 0;
            }
            if (!signal) {　　　　　　　　　// 没有找到更便宜的，在但前站点加满
                cost += sta[i].p * (C - nowTank);
                nowTank = C - (sta[i + 1].s / Davg);　　//更新到达下个站点，剩余油量
                nowSta++;　　　　　　　　　// 站点位置更新
            }
        }
        printf("%.2f\n", cost);
        return 0;
    }
}
```

 

多组测试
----

```C++
#include <iostream>
#include <algorithm>
using namespace std;
struct Gastation{
    double p, d, s;
};
bool cmp(Gastation a, Gastation b) {
    return a.d < b.d;
}
int main() {
    double C, D, Davg;
    int N;
    while(cin >> C >> D >> Davg >>N) {
        Gastation sta[N+1];
        double maxl = C*Davg;
        for (int i = 0; i < N; i++) {
            cin>>sta[i].p;
            cin>>sta[i].d;
        }
        sta[N].p = 0;
        sta[N].d = D;
        sort(sta, sta+N+1, cmp);
        for (int i = 1; i <= N; i++) {
            sta[i].s = sta[i].d - sta[i-1].d;
        }
        int flag = 1;
        if (sta[0].d > 0) {
            flag = 0;
            printf("The maximum travel distance = 0.00\n");
        }
        else {
            for (int i = 1; i <= N; i++) {
                if (sta[i].s > maxl) {
                    flag = 0;
                    printf("The maximum travel distance = %.2f\n",sta[i-1].d+maxl);
                }
            }
        }
        double cost = 0, nowTank = 0;
        int nowSta = 0, signal = 1;
        if (flag) {
            for (int i = 0; i < N; i++) {
                if (i != nowSta) continue;
                for (int j = i + 1; j <= N && (sta[j].d - sta[i].d) <= maxl; j++) {
                    if (sta[j].p < sta[nowSta].p) {
                        if (nowTank < (sta[j].d - sta[nowSta].d)/Davg) {
                            cost += sta[nowSta].p * ((sta[j].d - sta[nowSta].d) / Davg - nowTank);
                            nowTank = 0;
                        }
                        else nowTank -= (sta[j].d - sta[nowSta].d)/Davg;
                        nowSta = j;
                        signal = 1;
                        break;
                    } else signal = 0;
                }
                if (!signal) {
                    cost += sta[i].p * (C - nowTank);
                    nowTank = C - (sta[i + 1].s / Davg);
                    nowSta++;
                }
            }
            printf("%.2f\n", cost);
        }
    }
    return 0;
}
```

