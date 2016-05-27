title: 判断是否存在欧拉回路、欧拉路，输出欧拉路
date: 2016-05-27 10:09:24
tags: [算法, 积累, 搜索, 笔试]
---
前两天做到的题目。

# 题目描述
给定若干个长度为4的字符串，用空格分隔，判断这些字符串能否排成首尾相连的字符串。字母区分大小写。

若不能，则输出`ERROR`；
若可以形成首尾相连的圈，则输出`CIRCLE`；
否则，依次输出排序后的所有字符串。
<!--more-->
# 考虑思路
此题关键在于字符串的首位两个字符，是个典型的欧拉路问题，需要判断能否形成欧拉路、能否形成欧拉回路，若存在欧拉路，需要输出该路径。

将每个字符串抽象为首位字符代表的两个点，两点之间由首字符向尾字符连一条有向边。相同的字符为同一个点。这样构图就完成了。

## 判断欧拉回路的条件
无向图:  图连通，所有点都是偶数度

有向图:  图连通，所有点出度=入度

混合图: 构造网络流模型：
    1. 对所有的无向边随便取个方向，然后计算每个点的｜出度－入度｜，若为奇数，则不存在；
    2. 对每个点求 ｜出度－入度｜/2，记为x；
    3. 有向边按原图连边，容量为1；无向边 若出度>入度，则与源点相连，否则与汇点相连，容量为x；
    4. 若网络流满流，则存在欧拉回路。

## 判断欧拉通路的条件
无向图:  图连通，所有点都是偶数度(回路) || 只有两个点是奇数度。当所有点是偶数度时欧拉路起点可以是任意点；当有两个奇数度点时起点必须是奇数度点。

有向图:  图连通，所有点出度=入度(回路) || 有一个点入度-出度=1，有一个点出度-入度=1。同样，当所有点出度=入度时任意点可作为起点；而后者必须以出度-入度=1的点做起点，入度-出度=1的点做终点。


## 具体到本题
先利用并查集判断是否连通；
在连通的情况下，计算点的度，判断是否存在欧拉通路或欧拉回路；
若存在欧拉通路，利用套圈法求解。

注意到这里有个小trick，看题目描述，不能被区分大小写这句突兀的话误导（汗其实自己想当然了。。＝ ＝），题目说的是字符串，可以是任何可见字符，不一定是大小写字母。所以点的数量不只26*2个。

# 关键代码
```c++
/**  数据结构  **/
const int MAXN = 500;
//存放所有字符串，由于相同开头结尾的字符串可能有多个，因此不可以直接用矩阵存，而需要邻接表
struct Str
{
    char str[5];
    int next;
    bool used;
    Str() {next = -1; used = 0;}
}word[1000005];  //字符串信息
int word_list[MAXN][MAXN];//邻接表头，word_list[a][b]表示以a开头b结尾的字符串

// 用于存放一整行字符串
char line[4000005];
// 用空格分割后的字符串
char *p;

// 算法所用到的数据结构
int mat[MAXN][MAXN];    //网络流
int path[1000005];      //路径
int in[MAXN];  //入度
int out[MAXN];  //出度
int father[MAXN];  //并查集
bool vis[MAXN];     //并查集

void find_path_d(int now, int& step)
{
    for (int i = MAXN-1; i >= 0; i--)
    {
        while (mat[now][i])
        {
            mat[now][i]--;
            find_path_d(i, step);
        }
    }
    path[step++] = now;
}
int euclid_path(int start) //套圈法
{
    int ret=0; //步长
    memset(path, -1, sizeof(path));
    find_path_d(start,ret);
    return ret;
}

int Find(int x)  
{  
    while(x!=father[x])  
        x = father[x];  
    return x;  
}  

void Union(int r1,int r2)  
{  
    int a = Find(r1);  
    int b = Find(r2);  
    if(a!=b)  
        father[a] = b;  
}  

bool check(int a, int b)  
{  
    int r1 = in[a] - out[a];  
    int r2 = in[b] - out[b];  
    if((r1==1 && r2==-1) || (r1==-1 && r2==1))  
        return true;  
    return false;  
}

int solve(int x)
{
    int diff = 0;
    int odd_a = 0;
    int odd_b = 0;
    int line = 0;
    x = Find(x);  
    for(int i = 0; i < MAXN; i++)  
    {  
        if(vis[i])  
        {  
            if(out[i] != in[i])  
            {  
                diff++;  
                if(!odd_a)  odd_a = i;  
                else if(!odd_b) odd_b = i;  
                else//如果有三个数以上出入度不同，直接跳出  
                {  
                    line = 1;  
                    break;  
                }  
            }  
            if(Find(i)!=x)  
            {//如果出现过的点的父亲节点不同，跳出  
                line = 1;  
                break;  
            }  
        }  
    }
    if(line==0 && diff==0)
        return 1;
    if(line==0 && diff == 2 && check(odd_a,odd_b))
        return 2;
    return -1;
}

void init()
{
    memset(mat, 0, sizeof(mat));
    memset(vis, 0, sizeof(vis));  
    memset(in, 0, sizeof(in));  
    memset(out, 0, sizeof(out));
    for (int i  = 0 ; i < MAXN; i++)
        father[i] = i;
}

int main()
{
    while (gets(line)!=NULL)
    {
        p = strtok(line, " ");
        init();
        int num = 0;
        bool table[60];
        memset(table, 0, sizeof(table));
        //建图
        int x;
        int cnt = 0;
        while(p)
        {
            int st, ed;
            st = p[0];
            ed = p[strlen(p)-1];
            if (!table[st])
            {
                num++;
                table[st] = 1;
            }
            if (!table[ed])
            {
                num++;
                table[ed] = 1;
            }
            //保存该字符串
            strcpy(word[cnt].str, p);
            word[cnt].next = word_list[st][ed];
            word[cnt].used = 0;
            word_list[st][ed] = cnt++;
            p = strtok(NULL, " ");
            // 连边并统计 度
            mat[st][ed] += 1;
            in[ed]++;
            out[st]++;
            vis[st] = vis[ed] = 1;
            Union(st, ed);
            x = st;
        }
        int ans = solve(x);
        if (ans == 1)
        {
            puts("CIRCLE");
        }
        else if (ans == -1)
        {
            puts("ERROR");
        }
        else
        {
            //选初始点
            int i;
            for (i = 0; i < MAXN; i++)
            {
                if (out[i]-in[i]==1)
                    break;
            }
            int len = euclid_path(i);
            for (int i = len-2; i >= 0; i--)
            {
                int word_id = word_list[path[i+1]][path[i]];
                while (word[word_id].used && word[word_id].next!=-1)
                {
                    word_id = word[word_id].next;
                }
                if (word_id!=-1)
                {
                    word[word_id].used = 1;
                    printf("%s ", word[word_id].str);
                }
            }
            puts("");
        }
    }
    return 0;
}
```
