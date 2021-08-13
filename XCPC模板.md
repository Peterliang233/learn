### 数论

#### 逆元

+ 求逆元的方式有很多中，根据不同的情况可以利用不同的方法来进行求解。

+ 如果模数为素数，可以使用费马小定理进行求解。

+ 利用扩展欧几里得算法进行求解。

  ```C++
  void exgcd(int a,int b,int &x,int &y){
  	if(b==0){
  		x=1,y=0;
  		return;
  	}
  	exgcd(b,a%b,y,x);
  	y=y-a/b*x;
  }
  ```

+ 线性求1-n逆元。线性求逆元可以是我们在O(n）的时间复杂度内实现求1-n中每个数关于p的逆元。如果我们用上述的方法求解，很可能会超时。

  + 首先，我们要知道1的逆元是1，。

  + 然后，对于每个$i^{-1}$,我们很显然知道了i之前的每个数j的逆元了。

  + 模板如下：

    ```C++
    inv[1]=1;
    for(int i=2;i<=n;i++){
    	inv[i]=(ll)(p-p/i)*inv[p%i]%p;
    }
    ```

  + 这里使用p-p/i来防止负数的产生。同时要注意的是，这里的i应该与p互质，逆元的定义就是两个互质的数之间存在逆元，如果不互质，这个逆元是不存在的。

+ 线性求n个数之间的逆元

  + 首先，我们求出n个数的前缀积，然后利用快速幂或者扩展欧几里得算法计算$S_n$的逆元，记为$SV_n$

  + 然后，我们将$SV_n$乘上n个数中的每一个的$a_i$,我们就得到了$SV_i$,这个数就是n个数中不包含$a_i$的乘积的逆元了。

  + 最后$a_i^{-1}$就可以用$S_{i-1}SV_i$来进行表示出来。

    ```C++
    s[0]=1;
    for(int i=1;i<=n;i++){
    	s[i]=s[i-1]*a[i]%p;
    }
    s[n]=qpow(s[n],p-2);
    for(int i=n;i>=1;--i){
    	sv[i-1]=sv[i]*a[i]%p;
    }
    for(int i=1;i<=n;i++){
    	inv[i]=sv[i]*s[i-1]%p;
    }
    ```

  + 

#### 蜚蜀定理

+ 蜚蜀定理时关于最大公因数的定理，定义

  若gcd(a,b)=d,那么对于任意的整数x,y,ax+by都一定时d的倍数，特别的，一定存在整数x,y，使得ax+by=d,也就是ax+by=gcd(a,b)成立。

+ n个整数间的蜚蜀定理

  设a1,a2,a3...an为n个整数，**d时他们的最大公约数**，那么存在整数x1,x2,x3..xn使得x1a1+x2a2+..+xnan=d,特殊的，如果这n个数字都是互质的，那么就存在x1a1+x2a2+..+xnan=1.

  ```C++
  ll gcd(ll a,ll b){
      if(b==0) return a;
      return gcd(b,a%b);
  }
  ```

  

#### 概率论

+ 给出n个物体，让我们每次从里面取出一个，问刚好取出n个不同的物品的期望。
  $$
  E(x)=n\sum^{n}_{i=1}{\frac{1}{i}}=n(\frac{1}{1}+\frac{1}{2}+\frac{1}{3}+...+\frac{1}{n})
  $$

#### 线性代数

+ 矩阵的乘法

  + 快速求非波纳西数列的第n项

  给出一个非波纳西数列的最初始的矩阵为| 1 1 | ，然后，对于非波纳西数列的第n项
  $$
  (f(n),f(n-1))=(1,1)
  {\begin{bmatrix}
  1&1\\ 
  1&0\\
  \end{bmatrix}^{n-2}}
  $$
  模板

  ```C++
  #include<iostream>
  using namespace std;
  double a[20][20],b[20][20],c[20];
  int main(){
      int n;
      cin>>n;
      for(int i=1;i<=n;i++){   //输入矩阵的系数行列式
          for(int j=1;j<=n;j++){
              cin>>a[i][i];
          }
      }
      for(int i=1;i<=n;i++){  //输入方程右边
          cin>>b[i];
      }
      //将行列式化为上三角行列式
      for(int i=1;i<=n;i++){  //按照列的顺序进行处理
          for(int j=i;j<=n;j++){
              if(fabs(a[i][j])>1e-8){
                  for(int k=1;k<=n;k++){
                      swap(a[i][k],a[j][k]);
                  }
                  swap(b[i],b[j]);
              }
          }
          for(int j=1;j<=n;j++){  //对行进行遍历
              if(i==j) continue;  //对角线自动忽略
              double rate=a[j][i]/a[i][i];
              for(int k=i;k<=n;k++){
                  a[j][k]-=a[i][k]*rate;
              }
              b[j]-=b[i]*rate;
          }
      }
      //最后化为只有对角线的系数不为0的行列式
      for(int i=1;i<=n;i++){
          cout<<b[i]/a[i][i]<<" ";
      }
      cout<<endl;
      return 0;
  }
  ```



####  组合数

+ 卢卡斯定理

  卢卡斯定理的使用范围是对于n，m很大，然后p比较小的情况来的。但是要注意的是这个p要为素数
  $$
  lucas(n,m,p)=C(n\%p,m\%p,p)*lucas(n/p,m/p,p)
  $$

  ```C++
  ll jc[N]  //表示从i-N模p的阶乘
  ll qk(ll a,ll b){
      if(b==0) return 1;
      if(b&1) return a*qk(a,b-1)%p;
      else{
          ll mul=qk(a,b/2);
          return mul*mul%p;
      }
  }
  ll C(ll n,ll m,ll p){
      if(n<m) return 0;
      return jc[n]*qk(jc[n-m],p-2)%p*qk(jc[m],p-2)%p;
  }
  ll lucas(ll n,ll m,ll p){
      if(n<m) return 0;
      if(m==0) return 1;
      if(n==0) return 1;
      return lucas(n/p,m/p,p)*C(n%p,m%p,p)%p
  }
  ```

+ 扩展卢卡斯

  对于p不为素数的情况，我们可以对p进行质因数分解，然后利用中国剩余定理来求解，这里目前对原理还是没有深入地理解，所以先给出模板

  ```C++
  #include<iostream>
  #include<stack>
  #include<cmath>
  #include<vector>
  #include<cstring>
  #include<map>
  #include<algorithm>
  using namespace std;
  typedef long long ll;
  ll n,m,p;
  ll exgcd(ll a,ll b,ll &x,ll &y)
  {
      if(!b){x=1;y=0;return a;}
      ll res=exgcd(b,a%b,x,y),t;
      t=x;x=y;y=t-a/b*y;
      return res;
  }
  ll power(ll a,ll b,ll mod)
  {
      ll sm;
      for(sm=1;b;b>>=1,a=a*a%mod)if(b&1)
          sm=sm*a%mod;
      return sm;
  }
  
  ll fac(ll n,ll pi,ll pk)
  {
      if(!n)return 1;
      ll res=1;
      for(register ll i=2;i<=pk;++i)
          if(i%pi)(res*=i)%=pk;
      res=power(res,n/pk,pk);
      for(register ll i=2;i<=n%pk;++i)
          if(i%pi)(res*=i)%=pk;
      return res*fac(n/pi,pi,pk)%pk;
  }
  ll inv(ll n,ll mod)
  {
      ll x,y;
      exgcd(n,mod,x,y);
      return (x+=mod)>mod?x-mod:x;
  }
  
  ll CRT(ll b,ll mod){return b*inv(p/mod,mod)%p*(p/mod)%p;}
  
  const int MAXN=11;
  
  ll w[MAXN];
  
  ll C(ll n,ll m,ll pi,ll pk)
  {
      ll up=fac(n,pi,pk),d1=fac(m,pi,pk),d2=fac(n-m,pi,pk);
      ll k=0;
      for(register ll i=n;i;i/=pi)k+=i/pi;
      for(register ll i=m;i;i/=pi)k-=i/pi;
      for(register ll i=n-m;i;i/=pi)k-=i/pi;
      return up*inv(d1,pk)%pk*inv(d2,pk)%pk*power(pi,k,pk)%pk;
  }
  
  ll exlucus(ll n,ll m){
      ll res=0,tmp=p,pk;
      int lim=sqrt(p)+5;
      for(int i=2;i<=lim;i++){
          if(tmp%i==0){
              pk=1;
              while(tmp%i==0){
                  pk*=i;
                  tmp/=i;
              }
              (res+=CRT(C(n,m,i,pk),pk))%=p;
          }
      }
      if(tmp>1){
          (res+=CRT(C(n,m,tmp,tmp),tmp))%=p;
      }
      return res;
  }
  int main(){
     // freopen("test.in","r",stdin);
      cin>>n>>m>>p;
      cout<<exlucus(n,m)<<endl;
      fclose(stdout);
      return 0;
  }
  ```
  
+ 对于一个1-n的排列，求集合的排列中没有一个数在它的指定的位置的数的排列的个数$D_n=(n-1)(D_{n-1}+D_{n-2})$

#### 博弈论

+ min游戏，若干堆石头，每次可以从任意一堆取出若干个，问先手是否可以比赢。

  这个要考虑到画博弈图，我们可以发现
  $$
  ans=a_1\bigoplus{a_2}\bigoplus{a_3}...\bigoplus{a_n}
  $$
  若ans=0,则先手比输，否则先手比赢。

+ 巴什博弈，有一堆石头，总个数为n，两名玩家轮流在石头堆里面取石头，每次至少取一次，至多取m个，取走最后一个的玩家为胜者，判定先后手谁赢。

  如果(m+1)|n,则先手必败，否则先手必胜。

+ 威左夫博弈，有两堆石头，两人轮流取石头，每次，可以从任意一堆取，或者从两队同时取若干个，取到最后一个石头的赢。
  $$
  a=int((b-a)*\frac{\sqrt{5}+1}{2})
  $$
  若上面等式成立，那么就是后者胜，否则就是先者胜。

+ 非波纳西博弈

  有一堆个数为n的石头，游戏双方轮流取石头，满足：

  先手不能在第一次就把所有的石头取完;

  子后的每次取的石头数介于1到对手刚取的石头数的两倍之间。

  结论：先手胜当且仅当n不是非波纳西数。

+ 尼姆博弈

  有三堆物品若干个，两个人轮流从某一堆中取任意多的物品，规定每次至少取一个，多的不限，最后取光者得胜。

  将n堆的物品数量进行异或，结果为0则必败，否则必胜。

#### 矩阵

+ 矩阵快速幂是我们平时用于矩阵之间的乘法的一种很常见的方法，他的核心就是利用模拟矩阵的乘法的运算来进行求解，平时我们常见的可能是快速幂的写法，对于矩阵的快速幂其实也是需要掌握的

  ```C++
  struct node {
      ll a[120][120];
  }Node,I;
  node operator *(const node&x,const node&y){
      node z;
      for(int i=1;i<=n;i++){
          for(int j=1;j<=n;j++){
             z.a[i][j]=0;
          }
      }
      for(int i=1;i<=n;i++){
          for(int j=1;j<=n;j++){
              for(int k=1;k<=n;k++){
                  z.a[i][j]=(z.a[i][j]+x.a[i][k]*y.a[k][j]%mod)%mod;
              }
          }
      }
      return z;
  }
  
  ```

+ 另外，这里要说明的一个是，加入我们的指数很大的话，用递归的写法很容易爆内存，所以，建议使用非递归的写法。

+ 对于利用矩阵进行加速的一些题目，我们首要就是通过推导出原始的矩阵，最后的答案一般是某一个矩阵的某一个元素，如果我们不确定是哪一个矩阵的哪个元素，我们可以先进行打表观察。


#### 高斯消元

##### 求逆矩阵

+ 这个板子是利用先构造一个单位矩阵，然后将原矩阵化为单位矩阵，对原矩阵的操作同时施加到对单位矩阵的操作上面，最后得到的那个单位矩阵的情况就是我们要求的矩阵的逆了。

  ```C++
  #include<iostream>
  #include<algorithm>
  #include<cstring>
  #include<vector>
  #include<map>
  #include<stack>
  #include<queue>
  #include<fstream>
  #include<bits/stdc++.h>
  using namespace std;
  typedef long long ll;
  map<int,int> mp;
  const int mod = 1e9+7;
  const int N=200010;
  ll a[450][450<<1];
  ll qk(ll a,ll b){
      ll c=1;
      while(b){
          if(b&1) c=c*a%mod;
          a=a*a%mod;
          b>>=1;
      }
      return c;
  }
  int main(){
      //freopen("test.in","r",stdin);
      int n;
      cin>>n;
      int m=n+n;
      for(int i=1;i<=n;i++){
          for(int j=1;j<=n;j++){
              cin>>a[i][j];
          }
          a[i][i+n]=1;
      }
      for(int i=1;i<=n;i++){
          for(int j=i;j<=n;j++){
              if(a[j][i]){
                  for(int k=1;k<=m;k++){
                      swap(a[i][k],a[j][k]);
                  }
                  break;
              }
          }
  
          //无解的情况
          if(!a[i][i]){
              puts("No Solution");
              return 0;
          }
  
  
          //将对角线上的元素化为1
          ll r=qk(a[i][i],mod-2);
          for(int j=i;j<=m;j++){
              a[i][j]=a[i][j]*r%mod;
          }
  
  
  
          for(int j=1;j<=n;j++){  //对行进行遍历
              if(j!=i){
                  r=a[j][i];
                  for(int k=i;k<=m;k++){
                      a[j][k]=(a[j][k]-r*a[i][k]%mod+mod)%mod;
                  }
              }
          }
      }
      for(int i=1;i<=n;i++){
          for(int j=n+1;j<=m;j++){
              cout<<a[i][j]<<" ";
          }
          cout<<endl;
      }
      fclose(stdout);
      return 0;
  }
  ```

##### 求线性方程的解 

+ 求解线性方程组的解，它的原理是将矩阵化为只有对角线不为0,其他地方全为0的矩阵，我们通过矩阵的行与行之间的消元公式求得。

  ```C++
  #include<iostream>
  #include<algorithm>
  #include<cstring>
  #include<vector>
  #include<map>
  #include<stack>
  #include<queue>
  #include<fstream>
  #include<bits/stdc++.h>
  using namespace std;
  typedef long long ll;
  map<int,int> mp;
  const int mod = 1e9+7;
  const int N=200010;
  double a[120][120];
  int main(){
      //freopen("test.in","r",stdin);
      int n;
      scanf("%d",&n);
      for(int i=1;i<=n;i++){
          for(int j=1;j<=n+1;j++){
              scanf("%lf",&a[i][j]);
          }
      }
      for(int i=1;i<=n;i++){   //枚举列
          int mx=i;
          for(int j=i+1;j<=n;j++){  //找出对应列的行的最大值
              if(fabs(a[j][i])>fabs(a[mx][i])){
                  mx=j;
              }
          }
          for(int j=1;j<=n+1;j++){
              swap(a[i][j],a[mx][j]);
          }
  
  
          //判断是否无解
          if(!a[i][i]){
              puts("No Solution");
              return 0;
          }
  
          //同一列的每一行的数都减去这个数
          for(int j=1;j<=n;j++){
              if(j!=i){
                  double tmp=a[j][i]/a[i][i];
                  for(int k=i+1;k<=n+1;k++){
                      a[j][k]-=a[i][k]*tmp;
                  }
              }
          }
      }
      for(int i=1;i<=n;i++){
          printf("%.2lf\n",a[i][n+1]/a[i][i]);
      }
      fclose(stdout);
      return 0;
  }
  ```


####  卡特兰数

+ 卡特兰数是组合数学里面的一个知识点，它的使用场景为有两种情况，两种情况都是等价的，问最后某一个事物的种类数.它的前几项通常是1,1,2,5,14,42,132...

  以下是关于卡特兰数的一些公式：

+ $$
  \frac{C^{2n}_{n}}{{n+1}}(n>=2)
  $$

+ $$
  \sum^{n}_{i=1}{H_{i-1}}{H_{n-i}}
  $$

#### 线性基

+ 线性基的元素相互异或得到原籍和的元素的所有相互异或的得到的值。

+ 线性基满足性质1的最小的集合。

+ 线性基没有异或和为0的子集。

+ 线性基中每个元素的异或方案唯一。

+ 线性基中每个元素的二进制最高为互不相同。

+ 构造线性基的方法：对原集合的每个数p转为二进制，从高位向低位进行扫描，如果第i为为1,并且ai不存在，那么令ai=p并结束扫描，如果存在，令p=p^ai.

+ 线性基可以用来求一堆数之间相互异或，查询最值和第k大值。

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  typedef long long ll;
  const int mod =1e9+7;
  const int MN=60;
  ll a[62],tmp[62];
  bool flag;
  void ins(ll x){
      for(int i=MN;i;i--){
          if(x&(1ll<<i)){
              if(!a[i]){
                  a[i]=x;
                  return;
              }
              x^=a[i];
          }
      }
      flag=true;
  }
  bool check(ll x){
      for(int i=MN;i;i--){
          if(x&(1ll<<i)){
              if(!a[i]){
                  return false;
              }
              x^=a[i];
          }
      }
      return true;
  }
  //查询最大值
  ll qmx(ll res=0){
      for(int i=MN;i;i--){
          res=max(res,res^a[i]);
      }
      return res;
  }
  //查询最小值
  ll qmi(){
      if(flag) return 0;
      for(int i=0;i<=MN;i++){
          if(a[i]) return a[i];
      }
      return 0;
  }
  // 查询一堆数异或出来的第k大值
  ll query(ll k){
      ll res=0;
      int cnt=0;
      k-=flag;
      if(k){
          return 0;
      }
      for(int i=0;i<=MN;i++){
          for(int j=i-1;j;j--){
              if(a[i]&(1ll<<j)) a[i]^=a[j];
          }
          if(a[i]) tmp[cnt++]=a[i];
      }
      if(k>=(1ll<<cnt)) return -1;
      for(int i=0;i<cnt;i++){
          if(k&(1ll<<i)) res^=tmp[i];
      }
      return res;
  }
  int  main(){
      //freopen("test.in","r",stdin);
      ll n,x;
      cin>>n;
      for(int i=1;i<=n;i++){
          cin>>x;
          ins(x);
      }
      cout<<qmx()<<endl;
      fclose(stdout);
      return 0;
  }
  ```


#### 欧拉函数

+ 欧拉函数一般是用$\varphi(n)$表示小于n的与n互质的数的个数。

+ 欧拉函数的性质

  + 积性函数，$gcd(a,b)=1$时存在$\varphi(ab)=\varphi(a)\varphi(b)$。
  + $n=\sum_{d|n}{\varphi(d)}$,这个可以利用莫比乌斯反演得到。
  + 如果$n=p^k$,那么就有$\varphi(n)=p^k-p^{k-1}$
  + 根据唯一分解定理，我们可以得到

  $$
  \varphi(n)=n\prod^{s}_{i=1}({1-\frac{1}{p_i}})
  $$

+ 如何求解某一个数的欧拉函数

```C++
int euler_phi(int n){  //直接根据定义进行求解
    int ans=n;
    for(int i=2;i*i<=n;i++){
        if(n%i==0){
            ans=ans/i*(i-1);
            while(n%i==0) n/=i;
        }
    }
    if(n>1) ans=ans/n*(n-1);
    return ans;
}
```

+ 求解某一个范围内的所有的欧拉函数,时间复杂度为O(n)

```C++
int prime[5000010];
int is_prime[5000010];
int prime[5000010];
int phi[5000010];
void pre(){
    memset(is_prime,1,sizeof(is_prime));
    int cnt=0;
    is_prime[1]=0;
    phi[i]=1;
    for(int i=2;i<=5000000;i++){
        if(is_prime[i]){
            prime[++cnt]=i;
            phi[i]=i-1;
        }
        for(int j=1;j<=cnt&&i*prime[j]<=5000000;j++){
            is_prime[i*prime[]]=0;
            if(i%prime[j]){
				phi[i*prime[j]]=phi[i]*phi[prime[j]];
            }else{
                phi[i*prime[j]]=phi[i]*prime[j];
                break;
            }
        }
    }
}
```

+ 欧拉定理
  $$
  若gcd(a,m)=1，则a^{\varphi(m)}=1(mod m)
  $$

#### 莫比乌斯函数

+ 这里可能用到的知识是**积性函数**、**Dirichlet 卷积**、**莫比乌斯函数**

+ 莫比乌斯反演，对于某一些函数f(n),如果很难直接求出它的值，而很容易求出其倍数和或者约数和，那么我们就可以通过莫比乌斯反演进行简化计算，最后求得f(n)的值。

+ 对于一个数字n，有一个数字i，求最大的j使得$\lfloor{\frac{n}{i}}\rfloor=\lfloor{\frac{n}{j}}\rfloor$,那么这个j的值就是$j=\lfloor{\frac{n}{\lfloor{\frac{n}{i}}\rfloor}\rfloor}$,这个我们可以通过打表观察到某连续的一块计算下来是相同的数字。

+ 接下来，我们介绍一下莫比乌斯函数

  + n=1,u(n)=1
  + n含有平方因子，u(n)=0
  + n有k个本质不同的质因子，k为奇数就是-1,偶数就是1

+ 那么我们如何求1-n之间每一个数的的莫比乌斯函数

  ```C++
  void GetMu(){
      memset(flag,0,sizeof(flag));  //flag为0表示的是u素数，1除外
      mu[1]=1;
      for(int i=2;i<=n;i++){
  		if(!flag[i]){
              p[++tot]=i,mu[i]=-1;
          }
          for(int j=1;j<=tot&&i*p[j]<=n;j++){
              flag[i*p[j]]=1;
              if(i%p[j]==0){
                  mu[i*p[j]]=0;
                  break;
              }
              mu[i*p[j]]=-
          }
      }
  }
  ```

  

#### 杜教筛模板

$$
\sum_{i=1}^nu(i)和 \sum_{i=1}^n{\varphi(i)}
$$



+ 模板

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  typedef long long ll;
  const int maxn = 2000010;
  ll T,n,pri[maxn],cur,mu[maxn],sum_mu[maxn];
  bool vis[maxn];
  map<ll,ll> mp_mu;
  ll S_mu(ll x){
      if(x<maxn) return sum_mu[x];
      if(mp_mu[x]) return mp_mu[x];
      ll ret=1ll;
      for(ll i=2,j;i<=x;i=j+1){
          j=x/(x/i);
          ret-=S_mu(x/i)*(j-i+1);
      }
      return mp_mu[x]=ret;
  }
  ll S_phi(ll x){
      ll ret=0ll;
      for(ll i=1,j;i<=x;i=j+1){
          j=x/(x/i);
          ret+=(S_mu(j)-S_mu(i-1))*(x/i)*(x/i);
      }
      return ((ret-1)>>1)+1;
  }
  int main(){ 
      //freopen("test.in","r",stdin);
      cin>>T;
      mu[1]=1;
      for(int i=2;i<=maxn;i++){
          if(!vis[i]){
              pri[++cur]=i;
              mu[i]=-1;
          }
          for(int j=1;j<=cur&&i*pri[j]<maxn;j++){
              vis[i*pri[j]]=true;
              if(i%pri[j]){
                  mu[i*pri[j]]=-mu[i];
              }else{
                  mu[i*pri[j]]=0;
                  break;
              }
          }
      }
      for(int i=1;i<maxn;i++){
          sum_mu[i]=sum_mu[i-1]+mu[i];
      }    
      while(T--){
          cin>>n;
          cout<<S_phi(n)<<" "<<S_mu(n)<<endl;
      }
      fclose(stdout);
      return 0;
  }
  
  ```

  

### 字符串

#### 字符串哈希

+ 构造哈希函数

  + 哈希函数的一般构造方法：
    $$
    \sum^{l}_{i=1}{s[i]b^{l-i}}(modM)
    $$
    上述构造方法是通过给每个位置的字符增加一个权值，权值的大小根据字符串的位置，这样可以很大程度上避免冲突的产生。

  + 上述的M通常为一个素数，一般可以去233，2333，23333....，为了防止哈希冲突，可以使用多个不同的M；b可以使用任意的值，但是一般都是选择10的倍数。

+ 使用场景

  + 可以用于求某一个字符串的子串是否存在？这一场景一般可以利用KMP算法来进行实现，但是当我们的字符串的长度很大的时候，KMP算法容易超时。这样，我们就可以使用字符串哈希+二分的方法来进行求解。
  + 给出n个字符串，要求我们求这n个字符串的最长公共的子字符串。我们可以利用二分来求字符串的长度，然后对于每个长度，我们求每个字符该长度的子串，然后将其存入哈希表里面，然后求这n个哈希表的交集，这里取交集的方法我们可以用一个map来进行存储每个哈希值，同时用一个ans时刻更新map的最大值。
  + 求字符串的最长回文子串。我们首先对字符串进行预处理，然后进行枚举该最长回文子串的中心的坐标。

```C++
//回文串哈系模板,一个正哈系和一个反哈系，然后截取相同的进行一个比较就行了
typedef unsigned long long ll;
const int base1=233;
const int base2=23333;
ll power[1000010],has[1000010],has_reverse[1000010];
char s[1000010];
int main(){
    scanf("%s",s+1);
    int len=strlen(s+1);
    power[0]=1;
    has[0]=0,has_reverse[0]=0;
    for(int i=1;i<len+1;i++){
        power[i]=power[i-1]*base1;
    }
    for(int i=1;i<=len;i++){
        has[i]=has[i-1]*base1+s[i-1]-'a'+1;
        has_reaverse[i]=has_reverse[i-1]*base1+s[len-i+1]-a'+1;
    }
    int m;
    scanf("%d",&m);
    while(m--){
        int l,r;
        scanf("%d%d",&l,&r);
        ll tmp1=has[r]-has[l-1]*power[r-l+1];
        ll tmp2=has_reverse[len-i+1]-has_reverse[len-r]*power[r-l+1];
        if(tmp1==tmp2){
            puts("YES");
        }else{
            puts("NO");
        }
    }
    return 0;
}

```

#### 矩阵哈希

+ 对矩阵的哈希，其实可以利用一个二维前缀和

+ $$
  dp[i][j]=s[j]+dp[i-1][j]*base1+dp[i][j-1]*base2-dp[i-1][j-1]*base1*base2;
  $$

  ```C++
  //这是矩阵哈希模板
  typedef unsigned long long ll;
  const int maxn=100010;
  ll p1[maxn],p2[maxn],hash1[maxn],hash2[maxn],a[maxn][maxn];
  const ll pi=131,pj=233;
  void init(){
      p1[0]=1;
      p2[0]=1;
      for(int i=1;i<=10001;i++){
          p1[i]=p1[i-1]*233;
          p2[i]=p2[i-1]*131;
      }
  }
  
  void _hash(){
      for(int i=1;i<=n;i++){
          for(int j=1;j<=m;j++){
              hash1[i][j]=hash1[i-1][j]*pi+a[i][j];
          }
      }
      for(int i=1;i<=n;i++){
          for(int j=1;j<=m;j++){
              hash1[i][j]=hash1[i][j-1]*pj+a[i][j];
          }
      }
  }
  
  int main(){
      init();
      cin>>n>>m;
      for(int i=1;i<=n;i++){
          for(int j=1;j<=m;j++){
              cin>>a[i][j];
          }
      }
      _hash();
      //如果想要或得一个(a,b)大小的矩阵，我们可以
      for(int i=a;i<=n;i++){
          for(int j=b;j<=m;j++){
              ll has=hash1[i][j];
              has-=hash1[i-a][j]*p2[a];
              has-=hash1[i][j-b]*p1[b];
              has+=hash1[i-a][j-b]*p2[a]*p1[b];
              //这样，我们就可以求出每一个大小ab的矩阵的哈希值了
          }
      }
  }
  ```

#### KMP算法

+ next数组

  对于一个字符串，它的每个前缀的next数组就是这个前缀的最长前缀==最长后缀，例如：`abcdeabcd`，它的next数组的值就是4，因为最长相等的前后缀为`abcd`

+ 因此，当我们进行模式串匹配的时候，给出的一个文本串t，和一个模式串s，我们可以构造出一个字符串为s#t，这样我们可以通过KMP求出每个位置的next的值，然后对于每一个值，等于模式串长度的就存在它的子串。

+ next数组的长度不能为这个字符串的自身。

  ```C++
  string s;
  cin>>s;
  int n=s.size();
  for(int i=1;i<n;i++){
       int j=kmp[i-1];
       while(j>0&&s[i]!=s[j]) j=kmp[j-1];   //kmp[i]表示的是最长相等的前后缀的长度
       if(s[i]==s[j]) j++;
       kmp[i]=j;
  }
  ```

#### 扩展KMP算法

+ 扩展kmp算法是z[i]表示的是字符串s[i,i+1,i+2...n-1]与字符串s的最长公共前缀的长度。
+ 利用这个算法可以在线性时间内求出一个字符串在另一个字符串中出现的所有的位置。
+ 算法过程
  + 最与每个z[i]，我们确保了i之前的所有的z都已经求出来了。那么我们用[l,r]表示我们求到的目前的最长的公共前缀。如果i<r,那么z[i]=min(z[i-l],r-i+1)
  + 如果i>r,那么我们直接按照朴素的算法进行求解。
  + 最后，如果z[i]>r，我们可以更新l,r

```C++
vector<int> z_function(string s) {
  int n = (int)s.length();
  vector<int> z(n);
  for (int i = 1, l = 0, r = 0; i < n; ++i) {
    if (i <= r && z[i - l] < r - i + 1) {
      z[i] = z[i - l];
    } else {
      z[i] = max(0, r - i + 1);
      while (i + z[i] < n && s[z[i]] == s[i + z[i]]) ++z[i];
    }
    if (i + z[i] - 1 > r) l = i, r = i + z[i] - 1;
  }
  return z;
}
```

#### 字典树

+ ```C++
  //以0为根节点
  struct trie{
      int nxt[100010][26],cnt;
      bool exist[100010];  //该结点结尾的字符串是否存在
      void insert(char *s,int l){  //插入字符串，l为字符串的长度
          int p=0;
          for(int i=0;i<l;i++){
              int c=s[i]-'a';
              if(!nxt[p][c]) nxt[p][c]=++cnt;
              p=nxt[p][c];
          }
          exist[p]=1;
      }
  
      bool find(char *s,int l){  //l为字符串的长度
          int p=0;
          for(int i=0;i<l;i++){
              int c=s[i]-'a';
              if(!nxt[p][c]) return 0;
              p=nxt[p][c];
          }
          return exist[p];
      }
  }
  ```

+ 模板

  ```C++
  #include<iostream>
  #include<algorithm>
  #include<cstring>
  #include<vector>
  #include<map>
  #include<stack>
  #include<queue>
  #include<fstream>
  #include<bits/stdc++.h>
  using namespace std;
  typedef long long ll;
  const int mod = 1e9+7;
  struct node{
      int nxt[2];
      int end,sum;
  }Node[500010];
  int a[100010];
  int main(){
    //  freopen("test.in","r",stdin);
      int n,m,js=1;
      memset(Node,0,sizeof(Node));
      cin>>m>>n;
      for(int i=1;i<=m;i++){
          int len;
          cin>>len;
          for(int j=1;j<=len;j++){
              cin>>a[j];
          }
          int now=1;
          for(int j=1;j<=len;j++){
              if(Node[now].nxt[a[j]]!=0) now=Node[now].nxt[a[j]];
              else{
                  js++;
                  Node[now].nxt[a[j]]=js;
                  Node[js].nxt[1]=Node[js].nxt[0]=0;
                  now=js;
              }
              Node[now].sum++;
          }
          Node[now].end++;
      }
      for(int i=1;i<=n;i++){
          int len;
          cin>>len;
          for(int j=1;j<=len;j++){
              cin>>a[j];
          }
          int now=1,ans=0;
          bool flag=true;
          for(int j=1;j<=len;j++){
              if(Node[now].nxt[a[j]]!=0) now=Node[now].nxt[a[j]];
              else{
                  flag=false;
                  break;
              }
              ans+=Node[now].end;
          }
          if(!flag) cout<<ans<<endl;
          else cout<<ans+Node[now].sum-Node[now].end<<endl;
      }
      fclose(stdout);
      return 0;
  }
  ```

+ 字典树就是我们把一个字符串构建成一颗树的形式，我们可以对每一个节点进行标号，然后我们可以从根节点进行便利过去，如果遇到某一个节点的下一个不存在的话，那么就构建一个新的节点出来。最后我们所有的字符串就构建成了一棵树的形式，我们同时可以给每个节点加上一些其他的属性，比如表示经过这个节点的字符串的个数或者是以这个节点结束的字符的个数等等。

#### Manacher算法

+ 马拉车算法是用来求最大的回文字符串的长度的一个算法，它的时间复杂度为O(n).这种算法是采用一边优化，一边暴力的算法进行求解的。核心思想是每次记录下当前的最长的回文串的最左边和最右边的长度，然后，我们枚举每个中心，通过与左右边界的位置来进行判断它的边界。这里奇数和偶数是分开来计算的。我们可以在现在原字符串的左右两边插入一个肯定不会出现的字符，然后在每个字符中间插入一个‘#’,最后我们只需要按奇数长度的字符来进行处理就行了。

+ 计算最长回文半径的模板

  ```C++
  scanf("%s",c);
  int len=strlen(c);
  int cnt=0;
  s[++cnt]='^',s[++cnt]='#';
  for(int i=0;i<len;i++){
  	s[++cnt]=c[i];
  	s[++cnt]='#';
  }
  s[++cnt]='!';
  int mx=0;
  int id=0;
  for(int i=1;i<=cnt;i++){
  	if(i<mx) p[i]=min(p[id*2-i],mx-i);
  	else p[i]=1;
  	while(s[i-p[i]]==s[i+p[i]]) p[i]++;
  	if(mx<i+p[i]) id=i,mx=i+p[i];
  }
  ```

+ 计算最长回文长度的模板

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  int d[23000000];
  int main(){
  	string s;
  	cin>>s;
  	int len=s.size();
  	string c="";
  	for(int i=0;i<len;i++){
  		c+="#";
  		c+=s[i];
  	}
  	c+="#";
  	len=c.size();
  	int ans=0;
  	for(int i=0,l=0,r=-1;i<len;i++){
  		int k=(i>r)?1:min(d[l+r-i],r-i);
  		while(0<=i-k&&i+k<len&&c[i-k]==c[i+k]){
  			k++;
  		}
  		d[i]=k--;
  		ans=max(ans,d[i]-1);
  		if(i+k>r){
  			l=i-k;
  			r=i+k;
  		}
  	}
  	cout<<ans;
  	return 0;
  }
  ```


#### ac自动机

+ ac自动机是KMP+Tire两种算法的综合，它解决的问题是如何查询一个文本串可以与多少个模式串的匹配。

+ ac自动机是将所有的模式串构造成一棵Trie，我们称之为Trie树。

+ 同时，我们要引入一个失配指针fail的概念，失配指针是指一个字符串中匹配当前状态的最长后缀。可以将它和KMP算法的next数组比较起来理解。fail指针就是一个模式串的后缀集合

+ ac自动机主要有两个方面，一个是构建fail指针，另一个就是构建自动机。

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=1e6+6;  //字符串的长度
  int n;
  int tr[N][26],tot;
  int e[N],fail[N];
  void insert(char *s){
      int u=0;
      for(int i=1;s[i];i++){
          if(!tr[u][s[i]-'a']) tr[u][s[i]-'a']=++tot;
          u=tr[u][s[i]-'a'];
      }
      e[u]++;
  }
  queue<int> q;
  void build(){
      for(int i=0;i<26;i++){
          if(tr[0][i]){
              q.push(tr[0][i]);
          }
      }
      while(q.size()){
          int u=q.front();
          q.pop();
          for(int i=0;i<26;i++){
              if(tr[u][i]){
                  fail[tr[u][i]]=tr[fail[u]][i];
                  q.push(tr[u][i]);
              }else{
                  tr[u][i]=tr[fail[u]][i];
              }
          }
      }
  }
  //进行询问的文本串
  int query(char *t){
      int u=0,res=0;
      for(int i=1;t[i];i++){
          u=tr[u][t[i]-'a'];
          for(int j=u;j&&e[j]!=-1;j=fail[j]){
              res+=e[j];
              e[j]=-1;
          }
      }
      return res;
  }
  char s[N];
  int main(){
      //freopen("test.in","r",stdin);
      scanf("%d",&n);
      for(int i=1;i<=n;i++){
          scanf("%s",s+1);
          insert(s);
      }
      scanf("%s",s+1);
      build();
      printf("%d\n",query(s));
      fclose(stdout);
      return 0;
  }
  ```

#####  常用模板

+ 这里存一个模板，对于n个字符串，求在母串里面出现的次数，这个模板包括相交的和不相交的，询问0表示相交的，询问1表示不相交的子串的个数。用ac自动机可以在很好的时间复杂度内解决这个问题。

  ```C++
  #include <cstdio>
  #include <cstring>
  #include <algorithm>
  #include <queue>
  using namespace std;
  const int N = 800000;
  int Next[N][26], fail[N], val[N], sz, n, op[N], pos[N], last[N], res[N][2];
  char str[10], str2[N];
  
  void init() {
      memset(Next[0], 0, sizeof(Next[0]));
      val[0] = 0;
      sz = 1;
  }
  
  int insert(char *s) {
      int u = 0, len = strlen(s);
      for (int i = 0; i < len; i++) {
          int k = s[i] - 'a';
          if (!Next[u][k]) {
              memset(Next[sz], 0, sizeof(Next[sz]));
              val[sz] = i + 1;
              Next[u][k] = sz++;
          }
          u = Next[u][k];
      }
      return u;
  }
  
  void getFail() {
      queue<int> Q;
      fail[0] = 0;
      for (int i = 0; i < 26; i++)
          if (Next[0][i]) {
              fail[Next[0][i]] = 0;
              Q.push(Next[0][i]);
          }
      while (!Q.empty()) {
          int u = Q.front();
          Q.pop();
          for (int i = 0; i < 26; i++) {
              if (!Next[u][i])
                  Next[u][i] = Next[fail[u]][i];
              else {
                  fail[Next[u][i]] = Next[fail[u]][i];
                  Q.push(Next[u][i]);
              }
          }
      }
  }
  
  void query(char *s) {
      int u = 0, len = strlen(s);
      for (int i = 0; i < len; i++) {
          u = Next[u][s[i] - 'a'];
          int temp = u;
          while (temp != 0) {
              res[temp][0]++;
              if (i - last[temp] >= val[temp]) {
                  res[temp][1]++; 
                  last[temp] = i;
              }
              temp = fail[temp];
          }
      }
  }
  
  int main() {
      int cas = 1;
      while (scanf("%s", str2) == 1) {
          init();
          memset(res, 0, sizeof(res));
          memset(last, -1, sizeof(last));
          scanf("%d", &n);
          printf("Case %d\n", cas++);
          for (int i = 0; i < n; i++) {
              // 输入n个要询问的字符串
              scanf("%d%s", &op[i], str);
              pos[i] = insert(str);
          }
          // 构建一个ac自动机
          getFail();
          // 输入一个母串
          query(str2);
          for (int i = 0; i < n; i++)
              printf("%d\n", res[pos[i]][op[i]]);
          printf("\n");
      }
      return 0;
  }
  ```

  

#### 高精度

+ 下面主要是用C++模拟的一些大数加法，减法，乘法和除法

  ```C++
  
  vector<int> add(vector<int> &A,vector<int> &B){
      vector<int> C;
      int t=0;
      for(int i=0;i<A.size()||i<B.size();i++){
          if(i<A.size())  t+=A[i];
          if(i<B.size())  t+=B[i];
          C.push_back(t%10);
          t/=10;
      }
      if(t)   C.push_back(t);
      return C;
  }
  
  vector<int> mul(vector<int> &A,int b){
      vector<int> C;
      int t=0;
      for(int i=0;i<A.size()||t;i++){
          if(i<A.size())  t+=A[i]*b;//模拟
          C.push_back(t%10);
          t/=10;
      }
      while(C.size()>1&&C.back()==0)  C.pop_back();//排除前导0,b为0时会出现多余前导0
      return C;
  }
  
  vector<int> div(vector<int> &A,int b,int &r){
      vector<int> C;
      for(int i=A.size()-1;i>=0;i--){
          r=r*10+A[i];
          C.push_back(r/b);
          r%=b;
      }
      reverse(C.begin(),C.end());//翻转是为了方便去除前导0
      while(C.size()>1&&C.back()==0)  C.pop_back();
      return C;
  }
  void print(vector<int> a){
  	for(int i=a.size()-1;i>=0;i--){
  		cout<<a[i];
  	}
  	cout<<endl;
  }
  ```


### 计算几何

#### 距离

+ 欧式距离

  + 欧式距离指的是n维的两个点之间的距离。

+ 曼哈顿距离

  + 曼哈顿距离是n维坐标中的每一维之间的绝对值的和。

+ 切比雪夫距离

  + 切比雪夫距离指的是两个点的坐标绝对值的差的最大值。

+ 曼哈顿距离与切比雪夫距离之间可以相互转化。

  + $$
    d(A,B)=|x_1-x_2|+|y_1-y_2|=max(|(x_1+y_1)-(x_2+y_2)|,|(x_1-y_1)-(x_2-y_2)|)
    $$

  + $$
    d(A,B)=max(|x1-x2|,|y1-y2|)=max(|\frac{x1+y1}{2}-\frac{x2+y2}{2}|+|\frac{x1-y1}{2}-\frac{x2-y2}{2}|)
    $$

    

+ $L_m$距离

  + 这个距离是指n维中的每一维绝对值之差的m次方的和，然后开$\frac{1}{m}$次方后的值

  + $$
    d(L_m)=(|x_1-x_2|^m+|y_1-y_2|^m)^{\frac{1}{m}}
    $$

+ 汉明距离

  + 汉明距离针对的是两个长度相等的字符串对应位字符不一样的数量。
  + 解决的办法是通过将两个串进行异或运算，如果结果对应位的值为1的话说明就不是相等的。

#### 凸包

+ 在平面上能包含所有给定点的最小的凸多边形叫做凸包。

+ 对于给定的集合，所有包含x的凸集的交集称为x的凸包。

+ 凸包用一个最小的周长围住了给定的所有的点，如果一个凹多边形围住了所有的点，它的周长一定不是最小。

+ Andrew算法。时间复杂度为O($nlogn$)。

  + 我们把x坐标作为第一关键字，把y坐标作为第二关键字。
  + 显然，排序最小的元素和最大的元素一定是在凸包上面的，我们从某一个点逆时针出发，我们发现我们一定是往左拐的，一定出现右拐的情况，就说明我们目前走的是凹包，那么显然不会是最短的周长解了。
  + 我们用一个单调栈来维护上下凸壳。先用升序枚举出下凸壳，然后降序枚举出上凸壳。
  + 求凸壳的时候，一旦发现即将进栈的点和栈顶的两个点是向右旋转的，也就是叉积小于等于0,那么就弹出栈顶，回退到上一步，知道遇到叉积大于0的情况。

  ```C++
  // stk[] 是整型，存的是下标
  // p[] 存储向量或点
  tp = 0;                       // 初始化栈
  std::sort(p + 1, p + 1 + n);  // 对点进行排序
  stk[++tp] = 1;
  //栈内添加第一个元素，且不更新 used，使得 1 在最后封闭凸包时也对单调栈更新
  for (int i = 2; i <= n; ++i) {
    while (tp >= 2  // 下一行 * 操作符被重载为叉积
           && (p[stk[tp]] - p[stk[tp - 1]]) * (p[i] - p[stk[tp]]) <= 0)
      used[stk[tp--]] = 0;
    used[i] = 1;  // used 表示在凸壳上
    stk[++tp] = i;
  }
  int tmp = tp;  // tmp 表示下凸壳大小
  for (int i = n - 1; i > 0; --i)
    if (!used[i]) {
      //      ↓求上凸壳时不影响下凸壳
      while (tp > tmp && (p[stk[tp]] - p[stk[tp - 1]]) * (p[i] - p[stk[tp]]) <= 0)
        used[stk[tp--]] = 0;
      used[i] = 1;
      stk[++tp] = i;
    }
  for (int i = 1; i <= tp; ++i)  // 复制到新数组中去
    h[i] = p[stk[i]];
  int ans = tp - 1;
  ```

+ Graham算法

  + 该算法是通过找出y坐标最小，x坐标最小的那个点。然后利用单调栈的思想来进行求出凸包上面的点。重点是在于排序函数

    ```C++
    #include<bits/stdc++.h>
    using namespace std;
    typedef long long ll;
    struct node{
        double x,y;
    }Node[100010],s[100010];
    //逆时针方向走，检查两个向量(a,b)之间的叉乘，以此来判断是否产生凸包
    double check(node a1,node a2,node b1,node b2){
        return (a2.x-a1.x)*(b2.y-b1.y)-(b2.x-b1.x)*(a2.y-a1.y);  //x1y2-x2y1
    }
    
    //计算两个点之间的距离
    double d(node a,node b){
        return sqrt((a.x-b.x)*(a.x-b.x)+(a.y-b.y)*(a.y-b.y));
    }
    
    
    bool cmp(node a,node b){
        double tmp=check(Node[1],a,Node[1],b);
        if(tmp>0){
            return true;
        }
        if(tmp==0&&d(Node[1],a)<d(Node[1],b)) {
            return true;
        }
        return false;
    }
    int main(){
        //freopen("test.in","r",stdin);
        int n;
        scanf("%d",&n);
        for(int i=1;i<=n;i++){
            scanf("%lf%lf",&Node[i].x,&Node[i].y);
            double mid;
    
            //找出y坐标最小的那个点, 如果有多个y最小的点，那么就找x最小的点
            if(i!=1){
                if(Node[i].y<Node[1].y){
                    mid=Node[i].y,Node[i].y=Node[1].y,Node[1].y=mid;
                    mid=Node[i].x,Node[i].x=Node[1].x,Node[1].x=mid;
                }else if(Node[i].y==Node[1].y){
                    if(Node[i].x<Node[1].x){
                        mid=Node[i].y,Node[i].y=Node[1].y,Node[1].y=mid;
                        mid=Node[i].x,Node[i].x=Node[1].x,Node[1].x=mid;
                    }
                }
                
            }
        }
        sort(Node+2,Node+1+n,cmp);
        
    
        //s来模拟栈
        s[1]=Node[1];
        int cnt=1;
        for(int i=2;i<=n;i++){
    
    
            //栈顶两个点的向量和栈顶与当前点之间形成的向量，这是一个单调栈的思想
            while(cnt>1&&check(s[cnt-1],s[cnt],s[cnt],Node[i])<=0){
                cnt--;
            }
            cnt++;
            s[cnt]=Node[i];
        }
        s[cnt+1]=Node[1]; //合成一个封闭的凸包
        double ans=0;
        for(int i=1;i<=cnt;i++){
            ans+=d(s[i],s[i+1]);
        }
        printf("%.2lf\n",ans);
        fclose(stdout);
        return 0;
    }
    ```

    

#### 平面最近点对

+ 背景：请平面内n个点之间最近的点对。使用的是nlogn的分治算法。
+ 首先，我们对于每一个坐标的x和y，我们以x为第一关键字，以y为第二关键字进行排序。我们以中点为m=n/2。中心点的坐标为xm,ym
+ 然后，我们将两个点集拆分成两个大小相同的集合S1和S2，然后，我们假设两个集合的最近的点对的距离分别为h1和h2，我们取最小值为h。
+ 当我们合并的时候，我们试图找到这样的一个点对，一个在集合A1，另一个在集合A2,两者的距离都是小于h，然后我们将所有横坐标到xm的差小于h的放入一个集合B。得到了之后我们将b中的点按照y进行排序。
+ 第一次排序是在分治之前进行一次，第二次排序是将排序过的点集进行归并。

+ 非递归的写法

```C++
#include <algorithm>
#include <cmath>
#include <cstdio>
#include <set>
const int N = 200005;
int n;
double ans = 1e20;
struct point {
    double x, y;
    point(double x = 0, double y = 0) : x(x), y(y) {}
};

struct cmp_x {
    bool operator()(const point &a, const point &b) const {
        return a.x < b.x || (a.x == b.x && a.y < b.y);
    }
};

struct cmp_y {
    bool operator()(const point &a, const point &b) const { return a.y < b.y; }
};

inline void upd_ans(const point &a, const point &b) {
    double dist = sqrt(pow((a.x - b.x), 2) + pow((a.y - b.y), 2));
    if (ans > dist) ans = dist;
}

point a[N];
std::multiset<point, cmp_y> s;

int main() {
    //freopen("/home/lyp/code/algorithm/test.in","r",stdin);
    scanf("%d", &n);
    for (int i = 0; i < n; i++) scanf("%lf%lf", &a[i].x, &a[i].y);
    std::sort(a, a + n, cmp_x());
    for (int i = 0, l = 0; i < n; i++) {
        while (l < i && a[i].x - a[l].x >= ans) s.erase(s.find(a[l++]));
        for (auto it = s.lower_bound(point(a[i].x, a[i].y - ans));
             it != s.end() && it->y - a[i].y < ans; it++)
            upd_ans(*it, a[i]);
        s.insert(a[i]);
    }
    printf("%.4lf", ans);
    fclose(stdout);
    return 0;
}

```

+ 对于求平面最进或者最远的点对的问题，我们可以先对点进行x的从小到大的排序，然后我们可以发现，如果求最近的点，那么他们之间肯定是靠得比较近的，所以，我们可以指定一个常数s，当我们遍历到某一个点的时候，我们找一下这个点与他后面的s个点的距离，同时不断更新最小值对于找最大值的情况，我们需要做的是遍历到每一个点i的时候，我们计算最后s个点与这个点的距离，同时不断更新最大值即可。

```C++
#include<iostream>
#include <algorithm>
#include <cmath>
#include <cstdio>
#include <vector>
using namespace std;
const int s=13;

struct node{
    double x,y;
    friend bool operator <(const node &x1,const node &x2){
        return x1.x<x2.x;
    }
}a[200010];

int main() {
    //freopen("/home/lyp/code/algorithm/test.in","r",stdin);
    double mi=1e20,mx=0;
    int n;
    scanf("%d",&n);
    for(int i=0;i<n;i++){
        scanf("%lf %lf",&a[i].x,&a[i].y);
    }
    sort(a,a+n);
    for(int i=0;i<n;i++){

        //对后面的s个进行比较
        for(int j=i+1;j<n&&j<i+s;j++){
            mi=min(mi,(a[i].x-a[j].x)*(a[i].x-a[j].x)+(a[i].y-a[j].y)*(a[i].y-a[j].y));
        }

        //从最后面找出s个去最大值
        for(int j=n-1;j>=i&&j>=n-s;j--){
            mx=max(mx,(a[i].x-a[j].x)*(a[i].x-a[j].x)+(a[i].y-a[j].y)*(a[i].y-a[j].y));
        }
    }

    printf("%.4lf %.4lf\n",sqrt(mi), sqrt(mx));
    fclose(stdout);
    return 0;
}

```

#### 最小圆覆盖问题

+ 假设找到了圆的前i-1个点的最小覆盖圆，当我们加入第i个点的时候，如果这个点在圆上，我们什么都不做，如果不在圆上，我们就找到新的最小覆盖圆，这个圆肯定要经过第i个点。我们以第i个点为基础，重复以上过程加入第j个点，如果第j个点在圆外，那么最小覆盖圆必经过第j个点。

+ 模板求包含n个点的最小的圆的半径和圆心坐标

  ```C++
  #include <cmath>
  #include <cstdio>
  #include <cstdlib>
  #include <cstring>
  #include <iostream>
  
  using namespace std;
  
  int n;
  double r;
  
  struct point {
      double x, y;
  } p[100005], o;
  
  inline double sqr(double x) { return x * x; }
  
  inline double dis(point a, point b) {
      return sqrt(sqr(a.x - b.x) + sqr(a.y - b.y));
  }
  
  inline bool cmp(double a, double b) { return fabs(a - b) < 1e-8; }
  
  point geto(point a, point b, point c) {
      double a1, a2, b1, b2, c1, c2;
      point ans;
      a1 = 2 * (b.x - a.x), b1 = 2 * (b.y - a.y),
      c1 = sqr(b.x) - sqr(a.x) + sqr(b.y) - sqr(a.y);
      a2 = 2 * (c.x - a.x), b2 = 2 * (c.y - a.y),
      c2 = sqr(c.x) - sqr(a.x) + sqr(c.y) - sqr(a.y);
      if (cmp(a1, 0)) {
          ans.y = c1 / b1;
          ans.x = (c2 - ans.y * b2) / a2;
      } else if (cmp(b1, 0)) {
          ans.x = c1 / a1;
          ans.y = (c2 - ans.x * a2) / b2;
      } else {
          ans.x = (c2 * b1 - c1 * b2) / (a2 * b1 - a1 * b2);
          ans.y = (c2 * a1 - c1 * a2) / (b2 * a1 - b1 * a2);
      }
      return ans;
  }
  
  int main() {
      //freopen("/home/lyp/code/algorithm/test.in","r",stdin);
      scanf("%d", &n);
      for (int i = 1; i <= n; i++) scanf("%lf%lf", &p[i].x, &p[i].y);
      for (int i = 1; i <= n; i++) swap(p[rand() % n + 1], p[rand() % n + 1]);
      o = p[1];
      for (int i = 1; i <= n; i++) {
          if (dis(o, p[i]) < r || cmp(dis(o, p[i]), r)) continue;
          o.x = (p[i].x + p[1].x) / 2;
          o.y = (p[i].y + p[1].y) / 2;
          r = dis(p[i], p[1]) / 2;
          for (int j = 2; j < i; j++) {
              if (dis(o, p[j]) < r || cmp(dis(o, p[j]), r)) continue;
              o.x = (p[i].x + p[j].x) / 2;
              o.y = (p[i].y + p[j].y) / 2;
              r = dis(p[i], p[j]) / 2;
              for (int k = 1; k < j; k++) {
                  if (dis(o, p[k]) < r || cmp(dis(o, p[k]), r)) continue;
                  o = geto(p[i], p[j], p[k]);
                  r = dis(o, p[i]);
              }
          }
      }
      printf("%.10lf\n%.10lf %.10lf", r, o.x, o.y);
      fclose(stdout);
      return 0;
  }
  
  
  ```

#### 常用模板

+ ```C++
  #include<bits/stdc++.h>
  #include<cmath>
  using namespace std;
  
  
  const double eps = 1e-8;
  const double pi = acos(-1.0);
  const int maxp = 1010;
  //`Compares a double to zero`
  int sgn(double x){
  	if(fabs(x) < eps)return 0;
  	if(x < 0)return -1;
  	else return 1;
  }
  //square of a double
  inline double sqr(double x){return x*x;}
  /*
   * Point
   * Point()               - Empty constructor
   * Point(double _x,double _y)  - constructor
   * input()             - double input
   * output()            - %.2f output
   * operator ==         - compares x and y
   * operator <          - compares first by x, then by y
   * operator -          - return new Point after subtracting curresponging x and y
   * operator ^          - cross product of 2d points
   * operator *          - dot product
   * len()               - gives length from origin
   * len2()              - gives square of length from origin
   * distance(Point p)   - gives distance from p
   * operator + Point b  - returns new Point after adding curresponging x and y
   * operator * double k - returns new Point after multiplieing x and y by k
   * operator / double k - returns new Point after divideing x and y by k
   * rad(Point a,Point b)- returns the angle of Point a and Point b from this Point
   * trunc(double r)     - return Point that if truncated the distance from center to r
   * rotleft()           - returns 90 degree ccw rotated point
   * rotright()          - returns 90 degree cw rotated point
   * rotate(Point p,double angle) - returns Point after rotateing the Point centering at p by angle radian ccw
   */
  struct Point{
  	double x,y;
  	Point(){}
  	Point(double _x,double _y){
  		x = _x;
  		y = _y;
  	}
  	void input(){
  		scanf("%lf%lf",&x,&y);
  	}
  	void output(){
  		printf("%.2f %.2f\n",x,y);
  	}
  	bool operator == (Point b)const{
  		return sgn(x-b.x) == 0 && sgn(y-b.y) == 0;
  	}
  	bool operator < (Point b)const{
  		return sgn(x-b.x)== 0?sgn(y-b.y)<0:x<b.x;
  	}
  	Point operator -(const Point &b)const{
  		return Point(x-b.x,y-b.y);
  	}
  	//叉积
  	double operator ^(const Point &b)const{
  		return x*b.y - y*b.x;
  	}
  	//点积
  	double operator *(const Point &b)const{
  		return x*b.x + y*b.y;
  	}
  	//返回长度
  	double len(){
  		return hypot(x,y);//库函数
  	}
  	//返回长度的平方
  	double len2(){
  		return x*x + y*y;
  	}
  	//返回两点的距离
  	double distance(Point p){
  		return hypot(x-p.x,y-p.y);
  	}
  	Point operator +(const Point &b)const{
  		return Point(x+b.x,y+b.y);
  	}
  	Point operator *(const double &k)const{
  		return Point(x*k,y*k);
  	}
  	Point operator /(const double &k)const{
  		return Point(x/k,y/k);
  	}
  	//`计算pa  和  pb 的夹角`
  	//`就是求这个点看a,b 所成的夹角`
  	//`测试 LightOJ1203`
  	double rad(Point a,Point b){
  		Point p = *this;
  		return fabs(atan2( fabs((a-p)^(b-p)),(a-p)*(b-p) ));
  	}
  	//`化为长度为r的向量`
  	Point trunc(double r){
  		double l = len();
  		if(!sgn(l))return *this;
  		r /= l;
  		return Point(x*r,y*r);
  	}
  	//`逆时针旋转90度`
  	Point rotleft(){
  		return Point(-y,x);
  	}
  	//`顺时针旋转90度`
  	Point rotright(){
  		return Point(y,-x);
  	}
  	//`绕着p点逆时针旋转angle`
  	Point rotate(Point p,double angle){
  		Point v = (*this) - p;
  		double c = cos(angle), s = sin(angle);
  		return Point(p.x + v.x*c - v.y*s,p.y + v.x*s + v.y*c);
  	}
  };
  /*
   * Stores two points
   * Line()                         - Empty constructor
   * Line(Point _s,Point _e)        - Line through _s and _e
   * operator ==                    - checks if two points are same
   * Line(Point p,double angle)     - one end p , another end at angle degree
   * Line(double a,double b,double c) - Line of equation ax + by + c = 0
   * input()                        - inputs s and e
   * adjust()                       - orders in such a way that s < e
   * length()                       - distance of se
   * angle()                        - return 0 <= angle < pi
   * relation(Point p)              - 3 if point is on line
   *                                  1 if point on the left of line
   *                                  2 if point on the right of line
   * pointonseg(double p)           - return true if point on segment
   * parallel(Line v)               - return true if they are parallel
   * segcrossseg(Line v)            - returns 0 if does not intersect
   *                                  returns 1 if non-standard intersection
   *                                  returns 2 if intersects
   * linecrossseg(Line v)           - line and seg
   * linecrossline(Line v)          - 0 if parallel
   *                                  1 if coincides
   *                                  2 if intersects
   * crosspoint(Line v)             - returns intersection point
   * dispointtoline(Point p)        - distance from point p to the line
   * dispointtoseg(Point p)         - distance from p to the segment
   * dissegtoseg(Line v)            - distance of two segment
   * lineprog(Point p)              - returns projected point p on se line
   * symmetrypoint(Point p)         - returns reflection point of p over se
   *
   */
  struct Line{
  	Point s,e;
  	Line(){}
  	Line(Point _s,Point _e){
  		s = _s;
  		e = _e;
  	}
  	bool operator ==(Line v){
  		return (s == v.s)&&(e == v.e);
  	}
  	//`根据一个点和倾斜角angle确定直线,0<=angle<pi`
  	Line(Point p,double angle){
  		s = p;
  		if(sgn(angle-pi/2) == 0){
  			e = (s + Point(0,1));
  		}
  		else{
  			e = (s + Point(1,tan(angle)));
  		}
  	}
  	//ax+by+c=0
  	Line(double a,double b,double c){
  		if(sgn(a) == 0){
  			s = Point(0,-c/b);
  			e = Point(1,-c/b);
  		}
  		else if(sgn(b) == 0){
  			s = Point(-c/a,0);
  			e = Point(-c/a,1);
  		}
  		else{
  			s = Point(0,-c/b);
  			e = Point(1,(-c-a)/b);
  		}
  	}
  	void input(){
  		s.input();
  		e.input();
  	}
  	void adjust(){
  		if(e < s)swap(s,e);
  	}
  	//求线段长度
  	double length(){
  		return s.distance(e);
  	}
  	//`返回直线倾斜角 0<=angle<pi`
  	double angle(){
  		double k = atan2(e.y-s.y,e.x-s.x);
  		if(sgn(k) < 0)k += pi;
  		if(sgn(k-pi) == 0)k -= pi;
  		return k;
  	}
  	//`点和直线关系`
  	//`1  在左侧`
  	//`2  在右侧`
  	//`3  在直线上`
  	int relation(Point p){
  		int c = sgn((p-s)^(e-s));
  		if(c < 0)return 1;
  		else if(c > 0)return 2;
  		else return 3;
  	}
  	// 点在线段上的判断
  	bool pointonseg(Point p){
  		return sgn((p-s)^(e-s)) == 0 && sgn((p-s)*(p-e)) <= 0;
  	}
  	//`两向量平行(对应直线平行或重合)`
  	bool parallel(Line v){
  		return sgn((e-s)^(v.e-v.s)) == 0;
  	}
  	//`两线段相交判断`
  	//`2 规范相交`
  	//`1 非规范相交`
  	//`0 不相交`
  	int segcrossseg(Line v){
  		int d1 = sgn((e-s)^(v.s-s));
  		int d2 = sgn((e-s)^(v.e-s));
  		int d3 = sgn((v.e-v.s)^(s-v.s));
  		int d4 = sgn((v.e-v.s)^(e-v.s));
  		if( (d1^d2)==-2 && (d3^d4)==-2 )return 2;
  		return (d1==0 && sgn((v.s-s)*(v.s-e))<=0) ||
  			(d2==0 && sgn((v.e-s)*(v.e-e))<=0) ||
  			(d3==0 && sgn((s-v.s)*(s-v.e))<=0) ||
  			(d4==0 && sgn((e-v.s)*(e-v.e))<=0);
  	}
  	//`直线和线段相交判断`
  	//`-*this line   -v seg`
  	//`2 规范相交`
  	//`1 非规范相交`
  	//`0 不相交`
  	int linecrossseg(Line v){
  		int d1 = sgn((e-s)^(v.s-s));
  		int d2 = sgn((e-s)^(v.e-s));
  		if((d1^d2)==-2) return 2;
  		return (d1==0||d2==0);
  	}
  	//`两直线关系`
  	//`0 平行`
  	//`1 重合`
  	//`2 相交`
  	int linecrossline(Line v){
  		if((*this).parallel(v))
  			return v.relation(s)==3;
  		return 2;
  	}
  	//`求两直线的交点`
  	//`要保证两直线不平行或重合`
  	Point crosspoint(Line v){
  		double a1 = (v.e-v.s)^(s-v.s);
  		double a2 = (v.e-v.s)^(e-v.s);
  		return Point((s.x*a2-e.x*a1)/(a2-a1),(s.y*a2-e.y*a1)/(a2-a1));
  	}
  	//点到直线的距离
  	double dispointtoline(Point p){
  		return fabs((p-s)^(e-s))/length();
  	}
  	//点到线段的距离
  	double dispointtoseg(Point p){
  		if(sgn((p-s)*(e-s))<0 || sgn((p-e)*(s-e))<0)
  			return min(p.distance(s),p.distance(e));
  		return dispointtoline(p);
  	}
  	//`返回线段到线段的距离`
  	//`前提是两线段不相交，相交距离就是0了`
  	double dissegtoseg(Line v){
  		return min(min(dispointtoseg(v.s),dispointtoseg(v.e)),min(v.dispointtoseg(s),v.dispointtoseg(e)));
  	}
  	//`返回点p在直线上的投影`
  	Point lineprog(Point p){
  		return s + ( ((e-s)*((e-s)*(p-s)))/((e-s).len2()) );
  	}
  	//`返回点p关于直线的对称点`
  	Point symmetrypoint(Point p){
  		Point q = lineprog(p);
  		return Point(2*q.x-p.x,2*q.y-p.y);
  	}
  };
  struct circle{
  	Point p;//圆心
  	double r;//半径
  	circle(){}
  	circle(Point _p,double _r){
  		p = _p;
  		r = _r;
  	}
  	circle(double x,double y,double _r){
  		p = Point(x,y);
  		r = _r;
  	}
  	//`三角形的外接圆`
  	//`需要Point的+ /  rotate()  以及Line的crosspoint()`
  	//`利用两条边的中垂线得到圆心`
  	//`测试：UVA12304`
  	circle(Point a,Point b,Point c){
  		Line u = Line((a+b)/2,((a+b)/2)+((b-a).rotleft()));
  		Line v = Line((b+c)/2,((b+c)/2)+((c-b).rotleft()));
  		p = u.crosspoint(v);
  		r = p.distance(a);
  	}
  	//`三角形的内切圆`
  	//`参数bool t没有作用，只是为了和上面外接圆函数区别`
  	//`测试：UVA12304`
  	circle(Point a,Point b,Point c,bool t){
  		Line u,v;
  		double m = atan2(b.y-a.y,b.x-a.x), n = atan2(c.y-a.y,c.x-a.x);
  		u.s = a;
  		u.e = u.s + Point(cos((n+m)/2),sin((n+m)/2));
  		v.s = b;
  		m = atan2(a.y-b.y,a.x-b.x) , n = atan2(c.y-b.y,c.x-b.x);
  		v.e = v.s + Point(cos((n+m)/2),sin((n+m)/2));
  		p = u.crosspoint(v);
  		r = Line(a,b).dispointtoseg(p);
  	}
  	//输入
  	void input(){
  		p.input();
  		scanf("%lf",&r);
  	}
  	//输出
  	void output(){
  		printf("%.2lf %.2lf %.2lf\n",p.x,p.y,r);
  	}
  	bool operator == (circle v){
  		return (p==v.p) && sgn(r-v.r)==0;
  	}
  	bool operator < (circle v)const{
  		return ((p<v.p)||((p==v.p)&&sgn(r-v.r)<0));
  	}
  	//面积
  	double area(){
  		return pi*r*r;
  	}
  	//周长
  	double circumference(){
  		return 2*pi*r;
  	}
  	//`点和圆的关系`
  	//`0 圆外`
  	//`1 圆上`
  	//`2 圆内`
  	int relation(Point b){
  		double dst = b.distance(p);
  		if(sgn(dst-r) < 0)return 2;
  		else if(sgn(dst-r)==0)return 1;
  		return 0;
  	}
  	//`线段和圆的关系`
  	//`比较的是圆心到线段的距离和半径的关系`
  	int relationseg(Line v){
  		double dst = v.dispointtoseg(p);
  		if(sgn(dst-r) < 0)return 2;
  		else if(sgn(dst-r) == 0)return 1;
  		return 0;
  	}
  	//`直线和圆的关系`
  	//`比较的是圆心到直线的距离和半径的关系`
  	int relationline(Line v){
  		double dst = v.dispointtoline(p);
  		if(sgn(dst-r) < 0)return 2;
  		else if(sgn(dst-r) == 0)return 1;
  		return 0;
  	}
  	//`两圆的关系`
  	//`5 相离`
  	//`4 外切`
  	//`3 相交`
  	//`2 内切`
  	//`1 内含`
  	//`需要Point的distance`
  	//`测试：UVA12304`
  	int relationcircle(circle v){
  		double d = p.distance(v.p);
  		if(sgn(d-r-v.r) > 0)return 5;
  		if(sgn(d-r-v.r) == 0)return 4;
  		double l = fabs(r-v.r);
  		if(sgn(d-r-v.r)<0 && sgn(d-l)>0)return 3;
  		if(sgn(d-l)==0)return 2;
  		if(sgn(d-l)<0)return 1;
  	}
  	//`求两个圆的交点，返回0表示没有交点，返回1是一个交点，2是两个交点`
  	//`需要relationcircle`
  	//`测试：UVA12304`
  	int pointcrosscircle(circle v,Point &p1,Point &p2){
  		int rel = relationcircle(v);
  		if(rel == 1 || rel == 5)return 0;
  		double d = p.distance(v.p);
  		double l = (d*d+r*r-v.r*v.r)/(2*d);
  		double h = sqrt(r*r-l*l);
  		Point tmp = p + (v.p-p).trunc(l);
  		p1 = tmp + ((v.p-p).rotleft().trunc(h));
  		p2 = tmp + ((v.p-p).rotright().trunc(h));
  		if(rel == 2 || rel == 4)
  			return 1;
  		return 2;
  	}
  	//`求直线和圆的交点，返回交点个数`
  	int pointcrossline(Line v,Point &p1,Point &p2){
  		if(!(*this).relationline(v))return 0;
  		Point a = v.lineprog(p);
  		double d = v.dispointtoline(p);
  		d = sqrt(r*r-d*d);
  		if(sgn(d) == 0){
  			p1 = a;
  			p2 = a;
  			return 1;
  		}
  		p1 = a + (v.e-v.s).trunc(d);
  		p2 = a - (v.e-v.s).trunc(d);
  		return 2;
  	}
  	//`得到过a,b两点，半径为r1的两个圆`
  	int gercircle(Point a,Point b,double r1,circle &c1,circle &c2){
  		circle x(a,r1),y(b,r1);
  		int t = x.pointcrosscircle(y,c1.p,c2.p);
  		if(!t)return 0;
  		c1.r = c2.r = r1;
  		return t;
  	}
  	//`得到与直线u相切，过点q,半径为r1的圆`
  	//`测试：UVA12304`
  	int getcircle(Line u,Point q,double r1,circle &c1,circle &c2){
  		double dis = u.dispointtoline(q);
  		if(sgn(dis-r1*2)>0)return 0;
  		if(sgn(dis) == 0){
  			c1.p = q + ((u.e-u.s).rotleft().trunc(r1));
  			c2.p = q + ((u.e-u.s).rotright().trunc(r1));
  			c1.r = c2.r = r1;
  			return 2;
  		}
  		Line u1 = Line((u.s + (u.e-u.s).rotleft().trunc(r1)),(u.e + (u.e-u.s).rotleft().trunc(r1)));
  		Line u2 = Line((u.s + (u.e-u.s).rotright().trunc(r1)),(u.e + (u.e-u.s).rotright().trunc(r1)));
  		circle cc = circle(q,r1);
  		Point p1,p2;
  		if(!cc.pointcrossline(u1,p1,p2))cc.pointcrossline(u2,p1,p2);
  		c1 = circle(p1,r1);
  		if(p1 == p2){
  			c2 = c1;
  			return 1;
  		}
  		c2 = circle(p2,r1);
  		return 2;
  	}
  	//`同时与直线u,v相切，半径为r1的圆`
  	//`测试：UVA12304`
  	int getcircle(Line u,Line v,double r1,circle &c1,circle &c2,circle &c3,circle &c4){
  		if(u.parallel(v))return 0;//两直线平行
  		Line u1 = Line(u.s + (u.e-u.s).rotleft().trunc(r1),u.e + (u.e-u.s).rotleft().trunc(r1));
  		Line u2 = Line(u.s + (u.e-u.s).rotright().trunc(r1),u.e + (u.e-u.s).rotright().trunc(r1));
  		Line v1 = Line(v.s + (v.e-v.s).rotleft().trunc(r1),v.e + (v.e-v.s).rotleft().trunc(r1));
  		Line v2 = Line(v.s + (v.e-v.s).rotright().trunc(r1),v.e + (v.e-v.s).rotright().trunc(r1));
  		c1.r = c2.r = c3.r = c4.r = r1;
  		c1.p = u1.crosspoint(v1);
  		c2.p = u1.crosspoint(v2);
  		c3.p = u2.crosspoint(v1);
  		c4.p = u2.crosspoint(v2);
  		return 4;
  	}
  	//`同时与不相交圆cx,cy相切，半径为r1的圆`
  	//`测试：UVA12304`
  	int getcircle(circle cx,circle cy,double r1,circle &c1,circle &c2){
  		circle x(cx.p,r1+cx.r),y(cy.p,r1+cy.r);
  		int t = x.pointcrosscircle(y,c1.p,c2.p);
  		if(!t)return 0;
  		c1.r = c2.r = r1;
  		return t;
  	}
   
  	//`过一点作圆的切线(先判断点和圆的关系)`
  	//`测试：UVA12304`
  	int tangentline(Point q,Line &u,Line &v){
  		int x = relation(q);
  		if(x == 2)return 0;
  		if(x == 1){
  			u = Line(q,q + (q-p).rotleft());
  			v = u;
  			return 1;
  		}
  		double d = p.distance(q);
  		double l = r*r/d;
  		double h = sqrt(r*r-l*l);
  		u = Line(q,p + ((q-p).trunc(l) + (q-p).rotleft().trunc(h)));
  		v = Line(q,p + ((q-p).trunc(l) + (q-p).rotright().trunc(h)));
  		return 2;
  	}
  	//`求两圆相交的面积`
  	double areacircle(circle v){
  		int rel = relationcircle(v);
  		if(rel >= 4)return 0.0;
  		if(rel <= 2)return min(area(),v.area());
  		double d = p.distance(v.p);
  		double hf = (r+v.r+d)/2.0;
  		double ss = 2*sqrt(hf*(hf-r)*(hf-v.r)*(hf-d));
  		double a1 = acos((r*r+d*d-v.r*v.r)/(2.0*r*d));
  		a1 = a1*r*r;
  		double a2 = acos((v.r*v.r+d*d-r*r)/(2.0*v.r*d));
  		a2 = a2*v.r*v.r;
  		return a1+a2-ss;
  	}
  	//`求两圆相交的面积(精度更高)(需要long double)`
  	double areacircle2(circle v)
  	{
  		double a=hypot(p.x-v.p.x,p.y-v.p.y),b=r,c=v.r;
  		double s1=pi*r*r,s2=pi*v.r*v.r;
  		if(sgn(a-b-c)>=0)
  			return 0;
  		if(sgn(a+min(b,c)-max(b,c))<=0)
  			return min(s1,s2);
  		else
  		{
  			double cta1=2*acos((a*a+b*b-c*c)/(2*a*b));
  			double cta2=2*acos((a*a+c*c-b*b)/(2*a*c));
  			return cta1/(2*pi)*s1-0.5*sin(cta1)*b*b+cta2/(2*pi)*s2-0.5*sin(cta2)*c*c;
  		}
  	}
  	//`求圆和三角形pab的相交面积`
  	//`测试：POJ3675 HDU3982 HDU2892`
  	double areatriangle(Point a,Point b){
  		if(sgn((p-a)^(p-b)) == 0)return 0.0;
  		Point q[5];
  		int len = 0;
  		q[len++] = a;
  		Line l(a,b);
  		Point p1,p2;
  		if(pointcrossline(l,q[1],q[2])==2){
  			if(sgn((a-q[1])*(b-q[1]))<0)q[len++] = q[1];
  			if(sgn((a-q[2])*(b-q[2]))<0)q[len++] = q[2];
  		}
  		q[len++] = b;
  		if(len == 4 && sgn((q[0]-q[1])*(q[2]-q[1]))>0)swap(q[1],q[2]);
  		double res = 0;
  		for(int i = 0;i < len-1;i++){
  			if(relation(q[i])==0||relation(q[i+1])==0){
  				double arg = p.rad(q[i],q[i+1]);
  				res += r*r*arg/2.0;
  			}
  			else{
  				res += fabs((q[i]-p)^(q[i+1]-p))/2.0;
  			}
  		}
  		return res;
  	}
  };
  int main() {
  	int t;
  	cin >> t;
  	while(t--) {
  		double r1, x1, y1, r2, x2, y2, a;
  		cin >> r1 >> x1 >> y1;
  		cin >> r2 >> x2 >> y2;
  		cin >> a;
  		double rr1 = sqrt(r1 * r1 - a * a / 4) - a / 2.0, rr2 = sqrt(r2 * r2 - a * a / 4) - a / 2.0;
  		circle c1(x1, y1, rr1);
  		circle c2(x2, y2, rr2);
  		
  		double s1 = c1.areacircle(c2);
  		double s2 = pi * rr1 * rr1;
  		printf("%.6lf\n", s1 / s2);
  	}
  
  	return 0;
  }
  ```

+ 求两个圆相交的面积

  ![image-20210813170954505](C:\Users\ncuya\AppData\Roaming\Typora\typora-user-images\image-20210813170954505.png)



+ ```C++
  #include<iostream>
  #include<cmath>
  using namespace std;
   
  #define pi acos(-1.0)
   
  typedef struct node
  {
  	int x;
  	int y;
  }point;
   
  double AREA(point a, double r1, point b, double r2)
  {
  	double d = sqrt((a.x-b.x)*(a.x-b.x) + (a.y-b.y)*(a.y-b.y));
  	if (d >= r1+r2)
  		return 0;
  	if (r1>r2)
  	{
  		double tmp = r1;
  		r1 = r2;
  		r2 = tmp;
  	}
  	if(r2 - r1 >= d)
  		return pi*r1*r1;
  	double ang1=acos((r1*r1+d*d-r2*r2)/(2*r1*d));
  	double ang2=acos((r2*r2+d*d-r1*r1)/(2*r2*d));
  	return ang1*r1*r1 + ang2*r2*r2 - r1*d*sin(ang1);
  }
   
  int main()
  {
  	point a, b;
  	a.x=2, a.y=2;
  	b.x=7, b.y=2;
  	double result = AREA(a, 3, b, 5);
  	printf("%lf\n", result);
  	return 0;
  }
  ```




### 数据结构

#### 单调栈

+ 单调栈的使用是插入一个数的时候，保持栈的单调性的情况下弹出栈里面最少的元素。
+ 当我们往单调栈插入数据的时候，我们可以保持这个栈按照某一单调性，方法就是将这个数和栈顶的元素之间进行比较。

```C++
typedef long long ll;
struct node{
    int val;
    int index;
}Node[3000010];
stack<node> st;
ll id[3000010];
int main() {
    //freopen("test.in","r",stdin);
    int n;
    scanf("%d",&n);
    for(int i=1;i<=n;i++){
        scanf("%d",&Node[i].val);
        Node[i].index=i;
        id[i]=0;
    }
    for(int i=1;i<=n;i++){
        if(st.empty()){  //判断栈是否为空
            st.push(Node[i]);
        }else{
            if(st.top().val>Node[i].val){
                st.push(Node[i]);
            }else{
                while(st.size()&&st.top().val<Node[i].val){
                    id[st.top().index]=i;
                    st.pop();
                }
                st.push(Node[i]);
            }
        }
    }
    for(int i=1;i<=n;i++){
        printf("%d ",id[i]);
    }
    puts("");
    fclose(stdout);
    return 0;
}
```

#### 单调队列

+ 单调队列是维护区间内连续的k个值的最值的问题。

+ 比如，我们要维护一个区间内的最大值的时候，我们维护的其实就是一个单调递减的队列，所以，每次我们只需要从这个序列里面拿出队首的那个元素就是当前的最值了。而且，对于每次符合队列的元素，我们将这些元素都放到队尾进行一个维护，因为这些元素后面也可能成为对手元素。

+ 维护最小值的也是同样的想法，我们维护一个单调递增的队列，每次我们只需要找出队首的那个元素就行了。

  ```C++
  const int maxn=1000010;
  int a[maxn],q[maxn];
  int n,k;
  void getmx(){
      int head=0,tail=0;
      for(int i=1;i<=k;i++){
          while(head<=tail&&a[q[tail]]<=a[i]) tail--;
          q[++tail]=i;
      }
      for(int i=k;i<=n;i++){
          while(head<=tail&&a[q[tail]]<=a[i]) tail--;
          q[++tail]=i;
          while(q[head]+k<=i) head++;
          printf("%d ",a[q[head]]);
      }
      printf("\n");
  }
  
  void getmi(){
      int head=0,tail=0;
      for(int i=1;i<k;i++){
          while(head<=tail&&a[q[tail]]>=a[i]) tail--;
          q[++tail]=i;
      }
      for(int i=k;i<=n;i++){
          while(head<=tail&&a[q[tail]]>=a[i]) tail--;
          q[++tail]=i;
          while(q[head]+k<=i) head++;
          printf("%d ",a[q[head]]);
      }
      printf("\n");
  }
  int main() {
      //freopen("test.in","r",stdin);
      scanf("%d%d",&n,&k);
      for(int i=1;i<=n;i++){
          scanf("%d",&a[i]);
      }
      getmi();
      getmx();
      fclose(stdout);
      return 0;
  }
  
  ```

#### ST表(倍增表)

+ ST表可以解决RMD算法。也就是维护区间内某一个属性的值，如gcd，最值等，缺点是不能进行区间的修改。

+ ST表是将一个int范围内的数进行二进制划分，我们可以发现，最多也就20或者30个区间,f[i] [j]表示的是区间[i,i+2^j-1]内维护的某一个值。那么，我们发现，如果要求查询某一个区间的时候，我们可以将区间进行划分，我们利用两个区间重叠之后覆盖整个区间的特点尽可能的去覆盖两个区间。
+ 首先，我们要进行的是预处理出每个点倍增一定的长度的值，然后就可以很容易地进行状态方程的转移了。

```C++
inline int read() {
  char c = getchar();
  int x = 0, f = 1;
  while (c < '0' || c > '9') {
    if (c == '-') f = -1;
    c = getchar();
  }
  while (c >= '0' && c <= '9') {
    x = x * 10 + c - '0';
    c = getchar();
  }
  return x * f;
}

const int logn=21;
const int maxn=2000010;
int f[maxn][30],lg[maxn+1];
void pre(){
    lg[1]=0;
    lg[2]=1;
    for(int i=3;i<=maxn;i++){
        lg[i]=lg[i/2]+1;
    }
}

int main() {
    //freopen("test.in","r",stdin);
    int n=read(),m=read();
    for(int i=1;i<=n;i++){
        f[i][0]=read();
    }
    pre();
    for(int j=1;j<=logn;j++){
        for(int i=1;i+(1<<j)-1<=n;i++){
            f[i][j]=max(f[i][j-1],f[i+(1<<(j-1))][j-1]);
        }
    }
    while(m--){
        int x=read(),y=read();
        int s=lg[y-x+1];
        printf("%d\n", max(f[x][s],f[y-(1<<s)+1][s]));
    }
```

##### 倍增+二分模板

+ 倍增一般是用于预处理的时候，但是我们发现这个一般只用来进行预处理操作，对于快读查询一个大的区间，我们可以使用枚举左端点，更新右端点的方法进行处理，下面给出一个倍增+二分的方法查询区间的GCD。

  ```C++
  #include<bits/stdc++.h>
  
  using namespace std;
  typedef long long ll;
  
  ll a[200010],d[200010],f[200010][30];
  int n;
  
  ll gcd(ll x,ll y){
  	if(y==0) return x;
  	return gcd(y,x%y);
  }
  
  void ST(){
  	for(int i=1;i<=n;i++){
  		f[i][0]=d[i];
  	}
  	
  	int t=log(n)/log(2)+1;
  	
  	for(int j=1;j<t;j++){
  		for(int i=1;i<=n-(1<<j)+1;i++){
  			f[i][j]=gcd(f[i][j-1],f[i+(1<<(j-1))][j-1]);
  		}
  	}
  }
  
  ll query(int l,int r){
  	int k=log(r-l+1)/log(2);
  	
  	return gcd(f[l][k],f[r-(1<<k)+1][k]);
  }
  
  bool check(int l,int mid){
  	ll g=query(l,mid);
  	if(g>1) return true;
  	return false;
  }
  
  void solve(){
  	scanf("%d",&n);
  	for(int i=1;i<=n;i++){
  		scanf("%lld",&a[i]);
  	}
  		
  	for(int i=2;i<=n;i++){
  		d[i-1]=abs(a[i]-a[i-1]);
  	}
  	
  	n--;
  	
  	ST();
  
  	int ans=0;
      // 二分模板，枚举左端点，e
  	for(int i=1;i<=n;i++){
  		if(d[i]==1) continue;
  		int l=i,r=n;
  		while(l<r){
  			int mid=(l+r+1)>>1;
  			if(check(i,mid)==false)  r=mid-1;
  			else l=mid;
  		}
  		
  		ans=max(ans,l-i+1);
  	}
  	
  	printf("%d\n",ans+1);
  }
  
  int main(){
  	int t;
  	scanf("%d",&t);
  	while(t--){
  		solve();
  	}
  	return 0;
  }
  ```

  



#### 树状数组

+ 树状数组的代码想对于线段树来说更加精简，但是树状数组一般支持的是单点修改，在我看来，树状数组其实和倍增的思想很一致，就是将在一个范围内的区间进行二进制划分，然后我们每次对某一个点的修改，我们只需要更新涉及这个位置的相关的区间就行了，这样我们就可以将时间复杂度降低为log级别的了。

+ 首先，我们介绍一个lowbit函数，这个函数是找出一个数的二进制表示下的最低位的1。

  ```C++
  int lowbit(int x){
  	return x*(-x);
  }
  ```

+ 然后，如果想要对某一个位置的数进行修改的话，那么我们就只需要后进行二进制区间的更新;如果想要对某一个区间范围进行查询的话，我们只需要利用前缀和的思想进行一个处理。往前面进行查找。

#### 线段树

+ 线段树的操作大体分为建树，更新，查询，懒标记等等。建树和更新是比较常见的，对于懒标记，根据具体的题目进行处理，比如进行区间反转等等，可以使用懒惰标记进行标记，根据懒惰进行区间的更新。
+ 如果出现一个变量比较难维护的情况，可以使用多个变量进行维护，但是要确保这些变量都是可以进行区间的合并的，即有区间的合并性。
+ 懒标记是我们经常需要使用的一个应用，为了防止没必要的更新子节点的信息，我们先使用懒标记进行处理，如果真的需要用到这个结点再进行使用。

##### 扫描线

+ 线段树有一个比较常见的问题就是扫描线问题，求矩形的并等

+ ```C++
  #include<bits/stdc++.h> 
  
  
  using namespace std;
  typedef long long ll;
  
  const int N=10010;
  
  struct node{
  	int l,r;
  	int cnt;
  	double len;
  }Node[N<<3];
  
  struct point{
  	double x,y1,y2;
  	int flag;
  }p[N<<2];
  
  double raw[N<<1];
  map<double,int> val;
  
  bool cmp(point a,point b){
  	return a.x<b.x;
  }
  
  void build(int u,int l,int r){
  	Node[u].l=l,Node[u].r=r,Node[u].cnt=0,Node[u].len=0;
  	if(l==r) return;
  	int mid=(l+r)>>1;
  	build(u<<1,l,mid);
  	build(u<<1|1,mid+1,r);
  }
  
  
  void change(int u,int l,int r,int k){
  	if(l<=Node[u].l&&r>=Node[u].r){
  		Node[u].len=((Node[u].cnt+=k)?raw[Node[u].r+1]-raw[Node[u].l]:(Node[u].l==Node[u].r?0:Node[u<<1].len+Node[u<<1|1].len));
  		return;
  	}
  	
  	int mid=(Node[u].l+Node[u].r)>>1;
  	if(l<=mid) change(u<<1,l,r,k);
  	if(r>mid)  change(u<<1|1,l,r,k);
  	Node[u].len=(Node[u].cnt?raw[Node[u].r+1]-raw[Node[u].l]:Node[u<<1].len+Node[u<<1|1].len);
  }
  
  int main(){
  	int n;
  	int k=0;
  	while(cin>>n&&n){
  		val.clear();
  		k++;
  		printf("Test case #%d\n",k);
  		
  		
  		
  		for(int i=1;i<=n;i++){
  			double x1,y1,x2,y2;
  			scanf("%lf %lf %lf %lf",&x1,&y1,&x2,&y2);
  				
  			p[i]={x1,y1,y2,1};
  			p[i+n]={x2,y1,y2,-1};
  			// 收集每个y坐标
  			raw[i]=y1;  
  			raw[i+n]=y2;
  		}
  		
  		n+=n;
  		
          // 对y坐标进行离散化
  		sort(raw+1,raw+1+n);
  		
  		int m=unique(raw+1,raw+1+n)-(raw+1);
          // 将每个y坐标映射到对于的一个唯一的数字
  		for(int i=1;i<=m;i++){
  			val[raw[i]]=i;
  		}
  		sort(p+1,p+1+n,cmp);
  		
          
          // 对y坐标建立线段树进行维护
  		build(1,1,m-1);
  		
  		double res=0;
  		for(int i=1;i<n;i++){
  			int y=val[p[i].y1],z=val[p[i].y2]-1;
  			change(1,y,z,p[i].flag);
              // 计算面积
  			res+=Node[1].len*(p[i+1].x-p[i].x);
  		}
  		
  		printf("Total explored area: %.2lf\n",res);
  		puts("");
  		
  	}
  	return 0;
  }
  ```

#### 分块

+ 分块可以很好的解决线段树或者树状数组的相关问题，但是这种算法需要找到一个很好的分块的方法，不然很容易造成时间复杂度过高等问题。

+ 一般的分块的方法是将整个区间分为$\sqrt{n}$块进行维护，需要查询的时候，我们可以将这部分的区间进行分块维护。

+ 分块模板

  ```c++
  #include<iostream>
  
  using namespace std;
  const int N=500010;
  int a[N],pos[N],L[N],R[N];
  
  int main(){
      int n;
      cin>>n;
      for(int i=1;i<=n;i++){
          cin>>a[i];
      }
      int t=sqrt(n);
      for(int i=1;i<=t;i++){
          L[i]=(i-1)*t+1;
          R[i]=i*t;
      }
     	if(R[i]<n){
          t++;
          L[t]=R[t-1]+1;
          R[t]=n;
      }
      
      // 对需要维护的进行预处理
      for(int i=1;i<=t;i++){
          for(int j=L[i];j<=R[i];j++){
              pos[j]=i;
              
              // 这里维护的是区间的和
              sum[i]+=a[j];
          }
      }
      
      
      // 进行后续的操作
      return 0;
  }
  ```





### 图论

#### 基本概念

+ 树的直径：最远的两个点之间的距离称为树的直径，连接两个点的路径被称为树的最长链。
+ 图的连通性：对于有向图而言，存在强连通和非强连通的区别，对于无向图而言，分为连通和非连通的区别。强连通是指任意两个点之间都可以互相达到。
+ 无向图的割边和割点：割边指的是如果把这条边删除，那么这个图的联通分量的个数会增加，割点指的是如果把这个点删除，这个图的连通分量的个数也会增加。
+ 树的dfn(时间戳)序列：指的是对树进行一次dfs得到的每个点第一次被访问到的时间戳。
+ 树的low(追溯值)序列，指的是以x为根的子树里面，这些子节点（包括自身的时间戳的最小值）

#### 常用模板

+ Tarjan算法求lca

  ```C++
  void tarjan(int x){
      v[x]=1;
      for(int i=head[x];i;i=nxt[i]){
          int y=ver[i];
          if(v[y]) continue;
          tarjan(y);
          fa[y]=x;
      }
      // 进行离线操作
      for(int i=0;i<query[x].size();i++){
          int y=query[x][i],id=query_id[x][i];
          if(v[y]==2){
              int lca=get(y); // lca就是x和y的最近公共祖先
          }
      }
      v[x]=2;
  }
  ```
+ Tarjan算法求割边

  ```C++
  void tarjan(int x,int in_edge){
      dfn[x]=low[x]=++num;
      for(int i=head[x];i;i=nxt[i]){
          int y=ver[i];
          if(!dfn[y]){
              tarjan(y,i);
              low[x]=min(low[x],low[y]);
              if(low[x]>dfn[y]){
                  bridge[i]=bridge[i^1]=true;
              }
          }else if(i!=(in_edge^1)){
              low[x]=min(low[x],dfn[y]);
          }
      }
  }
  
  
  // 调用
  for(int i=1;i<=n;i++){
      if(!dfn[i]){
          tarjan(i,0);
      }
  }
  ```

+ Tarjan算法求割点

  ```C++
  void tarjan(int x){
      dfn[x]=low[x]=++num;
      int flag=0; // 记录子节点的个数
      for(int i=head[x];i;i=nxt[i]){
          int y=ver[i];
          if(!dfn[y]){
              low[x]=min(low[x],low[y]);
      	    if(low[x]>=dfn[y]){
  	            flag++;
              	if(x!=root||flag>1) cut[x]=true;
          	}
          }else{
              low[x]=min(low[x],dfn[y]);
          }
      }
  }
  
  
  // 调用
  for(int i=1;i<=n;i++){
      if(!dfn[i]){
          root=i;
          tarjan(i);
      }
  }
  ```

  

#### 欧拉回路问题

+ 欧拉路：如果存在一条路径从s到t，恰好不重不漏地经过每一条边一次，那么这个从s到t的路径就被称作欧拉路。
+ 欧拉回路：欧拉回路就是在欧拉路的基础上，从起点出发可以回到终点。

+ 欧拉图的判定
	+ 一张无向图为欧拉图，当且仅当无向图连通，并且每个点的度数都是偶数。
+ 欧拉路的存在性判定
	+ 在一张无向图中存在欧拉路，当且仅当无向图连通，并且图中存在恰好两个结点的度数为奇数，其他结点的度数都是偶数，这两个度数为奇数的结点就是欧拉回路的起点和终点。
	
	+ 找出欧拉回路
	
	  ```C++
	  void dfs(int x){
	      对于从x出发的每条边(x,y)
	          如果这条边没有被访问过
	          	把这条边标记为已经访问
	          	dfs(y)
	          	把y入栈
	  }
	  
	  在主函数中
	      dfs(1)
	      倒序输出栈里面的所有的结点
	  ```
	
	+ 欧拉回路的代码
	
	  ```C++
	  void euler(){
	      stack[++top]=1;
	      while(top>0){
	          int x=stack[top],i=head[i];
	          while(i&&vis[i]) i=nxt[i];  // 找到第一条没有被访问的边
	          if(i){  // 沿着这条边模拟递归的过程，标记改边，b
	              stack[++top]=ver[i];
	              vis[i]=vis[i^1]=true;
	              head[x]=nxt[i];
	          }else{
	              top--;
	              ans[++t]=x;
	          }
	      }
	  }
	  
	  int main(){
	      cin>>n>>m;
	      tot=1;
	      for(int i=1;i<=m;i++){
	          int x,y;
	          cin>>x>>y;
	          add(x,y);
	          add(y,x);
	      }
	      euler();
	      for(int i=t;i;i--){
	          cout<<ans[i]<<endl;
	      }
	  }
	  ```
	
	  





