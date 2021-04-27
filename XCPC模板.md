### 数论

#### 蜚蜀定理

+ 蜚蜀定理时关于最大公因数的定理，定义。

  若gcd(a,b)=d,那么对于任意的整数x,y,ax+by都一定时d的倍数，特别的，一定存在整数x,y，使得ax+by=d成立。

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

#### KMP算法

+ next数组

  对于一个字符串，它的每个前缀的next数组就是这个前缀的最长前缀==最长后缀，例如：`abcdeabcd`，它的next数组的值就是4，因为最长相等的前后缀为`abcd`

+ 因此，当我们进行模式串匹配的时候，给出的一个文本串t，和一个模式串s，我们可以构造出一个字符串为s#t，这样我们可以通过KMP求出每个位置的next的值，然后对于每一个值，等于模式串长度的就存在它的子串。

+ next数组的长度不能为这个字符串的自身。

#### 字典树

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

+ 