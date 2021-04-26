### 数论

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

  