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

  + 首先，我们要知道1的逆元是1。

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
