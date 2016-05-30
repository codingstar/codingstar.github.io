title: 围棋 判断是否需要吃子，吃掉多少
date: 2016-05-30 14:09:24
tags: [算法, 积累, 搜索, 笔试]
---
前两天做到的题目。

# 题目描述
给定一个10*10的棋盘，0代表空，1代表白子，2代表黑子。
双方可以连续下子。
一个上下左右连续的棋子块，其上下左右的空白位置就是它的气，当某次下子使对手的棋子的气为0时，就要吃掉那些气为0的棋子。
有几种情况：
    1. 当落子仅使自己的气为0(a)，或下到非空白处时(b)，输出`2147483647`；
    2. 当落子正确且没有吃子时，输出`0`；
    3. 当落子将对手的气为0时，输出吃掉的棋子个数，正数为白子，负数为黑子；
    4. 当落子使双方气都为0时，只能吃掉对手的棋，输出棋子个数（同3）。
<!--more-->
# 考虑思路
这题纯模拟，只要仔细一点，把题目理解清楚，然后按这几种情况实现即可，主要用到的是bfs。1A。
注意到这里重要的是数每一个棋子块（上下左右连通的棋子）的气的个数，然后去吃相应颜色的棋子。

数完棋盘上每个棋子块的气后，要根据条件1.a、3、4判断这手棋是否正确并且要吃哪种棋。
吃棋的时候，用bfs再数一遍那个颜色的棋子的气，遇到气为0的，就把bfs队列里的棋子吃掉。

ps，由于上一次落子会影响下一次，因此需要注意控制出错时要把当前棋子从棋盘上去掉。

棋盘大小10*10，因此用bfs遍历，时间复杂度可以接受。

还是贴一下代码吧～一定要耐心仔细～

# 关键代码
```c++
#include <iostream>
#include <stdio.h>
#include <stdlib.h>
#include <algorithm>

using namespace std;

const int ERROR = 2147483647;

int g[15][15];    //棋盘
int cnt;    //当前棋子个数
int p[105][3]; //当前棋子

int vis[15][15];

int dir[4][2] = {0,-1,1,0,0,1,-1,0};

bool ok(int x, int y)
{
    if (x >= 0 && x < 10 && y >= 0 && y < 10)
        return true;
    return false;
}

int bfs(int stx, int sty, int x, int y, int c)
{
    int beg, end;
    beg = end = 0;
    int que[105][2];
    int sched[15][15];
    memset(sched, 0, sizeof(sched));
    que[end][0] = stx;
    que[end][1] = sty;
    end++;
    sched[stx][sty] = 1;
    int qi = 0;
    while (beg < end)
    {
        int nx = que[beg][0];
        int ny = que[beg][1];
        int nc = g[que[beg][0]][que[beg][1]];
        vis[nx][ny] = 1;
        for (int i = 0; i < 4; i++)
        {
            int tx = nx + dir[i][0];
            int ty = ny + dir[i][1];
            if (ok(tx,ty) && !sched[tx][ty])
            {
                sched[tx][ty] = 1;
                if (g[tx][ty] == nc) //same piece
                {
                    que[end][0] = tx;
                    que[end][1] = ty;
                    end++;
                }
                else if (g[tx][ty] == 0)
                {
                    qi++;
                }
            }
        }
        beg++;
    }
    return qi;
}

int eat(int color)
{
    int sum = 0;
    int sched[15][15];
    int que[105][2];
    int beg, end;

    memset(sched, 0, sizeof(sched));
    for (int nx = 0; nx < 10; nx++)
    {
        for (int ny = 0; ny < 10; ny++)
        {
            if (g[nx][ny] != color || sched[nx][ny])
                continue;
            //start to bfs same pieces && count qi
            beg = end = 0;
            que[end][0] = nx;
            que[end][1] = ny;
            end++;
            sched[nx][ny] = 1;
            int qi = 0;
            while (beg < end)
            {
                int nowx = que[beg][0];
                int nowy = que[beg][1];
                for (int i = 0; i < 4; i++)
                {
                    int tx = nowx + dir[i][0];
                    int ty = nowy + dir[i][1];
                    if (ok(tx,ty) && !sched[tx][ty])
                    {
                        if (g[tx][ty] == color) //same piece
                        {
                            que[end][0] = tx;
                            que[end][1] = ty;
                            end++;
                            sched[tx][ty] = 1;
                        }
                        else if (g[tx][ty] == 0)
                        {
                            qi++;
                        }
                    }
                }
                beg++;
            }
            if (qi == 0) //eat
            {
                sum += end; //吃掉棋子的个数
                for (int j = 0; j < end; j++)
                {
                    int lossx = que[j][0];
                    int lossy = que[j][1];
                    g[lossx][lossy] = 0;
                }
            }
        }
    }
    return sum;
}

int solve(int x, int y, int c)
{
    memset(vis, 0, sizeof(vis));
    int flag[2];
    flag[0] = flag[1] = 0; //分别代表白棋、黑棋气为0的块数
    for (int i = 0; i < 10; i++)
    {
        for (int j = 0; j < 10; j++)
        {
            if (g[i][j] == 0 || vis[i][j])
                continue;
            int qi = bfs(i, j, x, y, c);//选择没遍历过的起点
            if (qi == 0)
                flag[g[i][j]-1]++;
        }
    }
    int tobe;
    int res;
    if (flag[0] && flag[1]) //eat the other
    {
        tobe = (c == 1 ? 2 : 1);
        res = eat(tobe);
        if (tobe == 1)
            return res;
        return 0-res;
    }
    if (flag[0] > 0 && c == 1) //憋死自己了
        return ERROR;
    if (flag[1] > 0 && c == 2) //憋死自己了
        return ERROR;
    tobe = (c == 1 ? 2 : 1);
    res = eat(tobe);
    if (tobe == 1)
        return res;
    return 0-res;
}

int main()
{
    cnt = 0;
    int px, py, pc;
    for (int i = 0; i < 10; i++)
    {
        for (int j = 0; j < 10; j++)
        {
            scanf("%d", &g[i][j]); //读取棋盘初始状态
        }
    }
    while (~scanf("%d%d%d", &px, &py, &pc)) //落子
    {
        if (g[px][py] != 0)
        {
            printf("%d\n", ERROR);
            continue;
        }
        g[px][py] = pc;
        int ans = solve(px, py, pc); //判断落子是否正确，判断吃几个子
        printf("%d\n", ans);
        if (ans == ERROR)
        {
            g[px][py] = 0; //出错重置
        }
    }
    return 0;
}
```
