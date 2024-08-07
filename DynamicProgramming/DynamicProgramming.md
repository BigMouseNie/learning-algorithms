# Dynamic Programming

所谓动态规划是一种灵活的算法，因此就不存在所谓的模板(一些模版只不过是一种无意义的泛)。

但是也有比较典的一些问题，入门还是可以的 ，背包问题就是典中典！



[TOC]



### 一、01背包

 

```c++
/*
* 题述: 
* 对于一个背包可以盛放 X kg的物品
* 现有n个物体第n个物体的质量和价值分别为 W[n] 和 V[n]
* 求背包的可以装的最大价值?
*/

/*
* 思路：
* 问题就是对于一个物品拿或不拿的区别
* 建立dp[i][j],意思为当背包容量为j时,背包装前i个物品的最大价值
* dp[i][j]=max(dp[i-1][j],dp[i-1][j-W[i]]+V[i]);满足j>=W[i] 
*/

// Note: W[0] 和 V[0] 表示第0个物品的质量和价值，即索引0在此有意义
//          背包容量  物品个数   物品质量        物品价值
int Func1_1(int cap,int nums,vector<int>& W,vector<int>& V){
    // 创建dp数组 
    vector<vector<int>> dp(nums,vector<int>(cap+1));
    // 先遍历第一行，为了不让后面的dp[i-1][j]类的操作做多余判断
    for(int j=1;j<cap+1;++j){
        if(j>=W[0]){dp[0][j]=V[0];}
    }
    // 全遍历
    for(int i=1;i<nums;++i){
        for(int j=1;j<cap+1;++j){
            if(j<W[i]){ // 当前背包容量无法容下第i个物品
                dp[i][j]=dp[i-1][j];
            }
            
            // 当前背包可以容下第i个物品,腾出W[i]和上一行作比较
            // 若拿:  dp[i][j]=dp[i-1][j-W[i]]+V[i];
            // 若不拿:dp[i][j]=dp[i-1][j];
            else{dp[i][j]=max(dp[i-1][j],dp[i-1][j-W[i]]+V[i]);}
        }
    }
    return dp[nums-1][cap];// 返回前num-1个物品在背包容量为cap的最大价值
}

/*
* 优化1：
* 发现对于在i>1时,dp[i][j]的确定只需要知道dp[i-1]这一行的状态
* 所以可以申请两个一维数组,进行交替使用,以节省空间
* 优化2：
* 再进一步发现,dp[i][j]的确定只需要dp[i-1][j-W[i]]的值即可
* 所以我们可以申请一个一维数组以最大限度的节省空间,只不过需要一个小技巧
* 我们需要从后往前遍历,这是保证在一维dp的操作下dp[j]=dp[j-W[i]]+V[i]中
* dp[j]的修改不会影响这一行其它遍历操作
* 换而言之如果我们正向遍历那么遍历过得每一个值都是经过遍历的，当dp[j]=dp[j-W[i]]+V[i]
* 我们很难保证dp[j-W[i]]没有经过修改
*/

/* 状态压缩 */

int Func1_2(int cap,int nums,vector<int>& W,vector<int>& V){
    // 建立dp数组
    vector<int> dp(cap+1);
    // 无需对第一个物品进行单独遍历,因为没有出现类 i-1 形式导致数组越界情况
    for(int i=0;i<nums;++i){
        for(int j=cap;j>0;--j){
            if(j<W[i]){break;} 
            else{dp[j]=max(dp[j],dp[j-W[i]]+V[i]);}
        }
    }
    return dp[cap];
}

/*
* Note:
* 以上代码并没有测试，但是思路正确。
*/

```



### 二、完全背包

````C++
/*
* 提述：
* 对于一个背包可以盛放 X kg的物品
* 现有n种物体第n种物体的质量和价值分别为 W[n] 和 V[n]
* 每种物品都可有无限个,求背包的可以装的最大价值?
*/

/*
* 思路1：
* 一种简单的思路就是沿用01背包问题解法,在此基础上再加一层循环进行对该种物品应拿几件进行判断
* dp[i][j]就表示前i种物品在j的限制下可以装的最大价值
*/

// Note: W[0] 和 V[0] 表示第0种物品的质量和价值，即索引0在此有意义

// 直接在01背包代码上进行改动
//       背包容量  物品种类   物品质量        物品价值
int Func2_1(int cap,int nums,vector<int>& W,vector<int>& V){
    // 建立dp数组
    vector<int> dp(cap+1);
    for(int i=0;i<nums;++i){
        for(int j=cap;j>0;--j){
            if(j<W[i]){break;} 
            for(int k=1;k<=j/W[i];++k){ // 对第i种进行逐个放置,找最大值
            	dp[j]=max(dp[j],dp[j-k*W[i]]+k*V[i];
            }
        }
    }
    return dp[cap];
}

                          
/*
* 思路2：
* 将该dp重新展开成二维进行思考
* k<=j/W[i],令 n=j/W[i]
* 由上可知 dp[i][j]= for(k)循环 + max(dp[i-1][j],dp[i-1][j-k*W[i]]+k*V[i]) 来确定
* dp[i][j]=max{ dp[i-1][j],
*               dp[i-1][j-W[i]]+V[i],            
*               dp[i-1][j-2*W[i]]+2*V[i]
*               |           |          |
*               dp[i-1][j-n*W[i]]+n*V[i] }
* 对于dp[i][j-W[i]]易知 k<=n-1;
* dp[i][j-W[i]]=max{ dp[i-1][j-W[i]],
*                    dp[i-1][j-W[i]-W[i]]+V[i],
*                    dp[i-1][j-W[i]-2*W[i]]+2*V[i],
*                    |           |          |
*                    dp[i-1][j-W[i]-(n-1)*W[i]]+(n-1)*V[i] }
*
* 易知 dp[i][j-W[i]]+V[i]
* dp[i][j-W[i]]+V[i]=max{ dp[i-1][j-W[i]]+V[i],
*                    dp[i-1][j-2*W[i]]+2*V[i],
*                    dp[i-1][j-3*W[i]]+3*V[i],
*                    |           |          |
*                    dp[i-1][j-n*W[i]]+n*V[i] }
* 易知 dp[i][j-W[i]]+V[i] 与 dp[i][j]的确定只相差 dp[i-1][j]
* 并且当我们正向遍历时对于dp[i][j]而言dp[i][j-W[i]]已经确定
* 所以 dp[i][j]={dp[i-1][j],dp[i][j-W[i]]+V[i]}
* 当然依旧可以进行状态压缩，而且对于j为正向遍历
*/
                          
int Func2_2(int cap,int nums,vector<int>& W,vector<int>& V){
    // 建立dp数组
    vector<int> dp(cap+1);
    for(int i=0;i<nums;++i){
        for(int j=W[i];j<cap+1;++j){
            dp[j]=max(dp[j],dp[j-W[i]]+V[i]);
        }
    }
    return dp[cap];
}                          
                          
/* 
* Note:
* 通过对比我们不难发现在经过状态压缩后 01背包和完全背包的递推式相同
* 区别就是一个逆向用旧数据,一个正向用新数据!
*/

````



### 三、多重背包

```C++
/*
* 提述：
* 对于一个背包可以盛放 X kg的物品
* 现有n种物体，第n种物体的数量和质量和价值分别为 C[n] 和 W[n] 和 V[n]
* 求背包的可以装的最大价值?
*/

/*
* 思路1：
* 很简单的一个思路就是把它化成完全背包问题来解决，也是非常简单
*/


//         背包容量  物品种类   物品数量        物品质量        物品价值
int Func3_1(int cap,int nums,vector<int>& C,vector<int>& W,vector<int>& V){
    // 建立dp数组
    vector<int> dp(cap+1);
    for(int i=0;i<nums;++i){
        for(int j=cap;j>0;--j){
            if(j<W[i]){break;} 
            int x=min(j/W[i],C[i]); // 取二者最小即可
            for(int k=1;k<=x;++k){ // 对第i种进行逐个放置,找最大值
            	dp[j]=max(dp[j],dp[j-k*W[i]]+k*V[i];
            }
        }
    }
    return dp[cap];
}

/*
* 思路2：
* 如果一种物品比较多，那么第三层遍历就会很耗时，可以进行二进制拆分
* 比如第i种物品有200个可以分成: 1、2、4、8、16、32、64、73。这么多数可以组成200以内的任意数
* 也就是说对于第i种的遍历可以看成一次01背包问题,物品W=W[i]*2^k,V=V[i]*2^2
* 并且每个数只能拿一个
* 可以优化为 O(n^2*log n)
*/

int Func3_2(int cap,int nums,vector<int>& C,vector<int>& W,vector<int>& V){
    // 建立dp数组
    vector<int> dp(cap+1);
    for(int i=0;i<nums;++i){
        int x=C[i];
        for(int k=1;k<=x;x-=k,k*=2){  // 二进制拆分，并将其转换为01背包问题
            for(int j=cap;j>=0;--j){
                if(j<k*W[i]){break;}
                dp[j]=max(dp[j],dp[j-k*W[i]]+dp*V[i]);
            }
        }
        if(x>0){  // 对于第i种物品的剩余进行一次01背包遍历
            for(int j=V;j>=x*W[i];--j){
                dp[j]=max(dp[j],dp[j-x]+x*V[i])
            }
        }
    }
    return dp[cap];
}        


```



### 四、二维费用背包

#### 	1、最大值问题

```c++
/*
* 提述1：
* 对于一个背包可以盛放 X kg,容量为 Y m^3
* 现有n个物体，第n个物体的质量和体积和价值分别为 W[n] 和 V[n] 和 value[n]
* 每个物品都是一个,求背包的可以装的最大价值?
*/

/*
* 思路：
* 很容易该问题是一个01背包问题,选与不选的关系
* 不同的是多了一层循环
* dp[i][j][k]表示前i个物品在背包重量限度和容量限度为j和k的情况下可装最大价值
* dp[i][j][k]=max(dp[i-1][j][k],dp[i-1][j-W[i]][k-V[i]]);
* 当然我们直接把它进行状态压缩,对j和k分别逆向遍历
*/

int Func4_1(int MLimit,int VLimit,int num,vector<int>& M,vector<int>& V,
vector<int>& value){
    vector<vector<int>> dp(MLimit+1,vector<int>(VLimit));// 创建dp数组
	for(int i=0;i<nums;++i){
    	for(int j=MLimit;j>0;--j){
        	if(j<M[i]){break;}
        	for(int k=VLimit;k>0;--k){
            	if(k<V[i]){break;}
        		dp[j][k]=max(dp[j][k],dp[j-W[i]][k-V[i]]+value[i]);
        	}
    	}
	}
    return dp[MLimit][VLimit];
}

```

#### 	2、最小值问题

```C++
/*
* 提述2：
* 现在有nums个气缸,每个气缸都装有氧气和氮气
* 第i个气缸有 O[i]升的氧气,N[i]升的氮气,M[i]kg的质量
* 现在需要X升的氧气和Y升的氮气,气缸可以随意拿多个,求满足情况的最小质量？
*/

/*
* 思路：
* 直接考虑的话不是很容易考虑,我们并没有考虑过一维最小问题
* 那么如果该问题是一个一维问题，我们只考虑氧气
* dp[i][j]表示前i个气缸当所需氧气为j升时的最小质量
* 我们可以随机几个值进行打表模拟一下
*/
气缸    氧气  质量
0		3    120
1		7    129
2  		5    250
3       1    130
4       4    118
 
```

这个表格就是在5个气缸中找到所需氧气为8升的最小质量 

|      | 0    | 1    | 2    | 3    | 4      | 5      | 6      | 7      | 8      |
| ---- | ---- | ---- | ---- | ---- | ------ | ------ | ------ | ------ | ------ |
| 0    | 0    | 120  | 120  | 120  | 0x3f3f | 0x3f3f | 0x3f3f | 0x3f3f | 0x3f3f |
| 1    | 0    | 120  | 120  | 120  | 129    | 129    | 129    | 129    | 249    |
| 2    | 0    | 120  | 120  | 120  | 129    | 129    | 129    | 129    | 249    |
| 3    | 0    | 120  | 120  | 120  | 129    | 129    | 129    | 129    | 249    |
| 4    | 0    | 118  | 118  | 118  | 118    | 129    | 129    | 129    | 247    |

````C++
/*
* 由上面的表可知
* 是由左下角向右上角非严格递增
* 当j<=O[i]时： dp[i][j]=min(dp[i-1][j],W[i])
* 当j> O[i]时： dp[i][j]=min(dp[i-1][j],dp[i-1][j-O[i]]+W[i])
* 当前i个气缸无法满足时该需求时将其设置为一个较大的数(0x3f3f只是16进制下的一个较大int形)
*/

/*
* 思路续：
* 很容易想到 dp[i][j][k]表示前i个气缸在需要j的氧气和k的氮气情况下的最小质量
* 当O[i]>=j&&N[i]>=k时：dp[i][j][k]=min(dp[i-1][j][k],W[i]);
* 当O[i]>=j&&N[i]< k时：dp[i][j][k]=min(dp[i-1][j][k],dp[i-1][0][k-N[i]]+W[i])
* 当O[i]< j&&N[i]>=k时：dp[i][j][k]=min(dp[i-1][j][k],dp[i-1][j-O[i]][0]+W[i])
* 当O[i]< j&&N[i]< k时：dp[i][j][k]=min(dp[i-1][j][k],dp[i-1][j-O[i]][k-N[i]]+W[i])
* 当然我们直接进行状态压缩,并逆向遍历
*/


int Func4_2(int X,int Y,int nums,vector<int>& O,vector<int>& N,vector<int>& M){
    vector<int> dp(X+1,vector<int>(Y+1,0x3f3f));// 初始化dp数组
    dp[0][0]=0;
    for(int i=0;i<nums;++i){
        for(int j=X;j>=0;--j){
            for(int k=Y;k>=0;--k){
                if(O[i]>=j&&N[i]>=k){
                    dp[j][k]=min(dp[j][k],W[i]);
                }
                else{
                    if(O[i]>=j){dp[j][k]=min(dp[j][k],dp[0][k-N[i]]+W[i]);}
                    else if(N[i]>=k){dp[j][k]=min(dp[j][k],dp[j-O[i]][0]+W[i]);}
                    else{dp[j][k]=min(dp[j][k],dp[j-O[i]][k-N[i]]);}
                }
            }
        }
    }
    return dp[X][Y];
}








````



























