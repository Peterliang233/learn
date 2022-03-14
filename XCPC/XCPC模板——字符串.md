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
        has[i]=has[i-1]*base1+s[i-]-'a'+1;
        has_reaverse[i]=has_reverse[i-1]*base1+s[len-i+1]-'a'+1;
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

+ 扩展kmp算法是z[i]表示的是字符串s[i,i+1,i+2...n-1]与字符串s的最长公共前缀的长度。特别的，z[0]=0
  + 如  $z[aaabaab]= {0,2,1,0,2,1,0}$ 
+  利用这个算法可以**在线性时间内求出一个字符串在另一个字符串中出现的所有的位置。**
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
      // 这部分属于暴力进行匹配
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

#### $Manache$r算法

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

#### 后缀自动机

##### 后缀数组

+ 后缀数组是两个数组，$sa$和$rk$，**$sa[i]$表示将所有的后缀进行排序后的第i小的后缀的编号**，**$rk[i]$ 表示的是后缀i的排名**。这两个数组满足性质$sa[rk[i]]=rk[sa[i]]=i$​

+ 这个排序不是先比较长短，而是先逐个字符进行比较。

+ 给一张图进行理解

  ![image-20210824165514729](C:\Users\ncuya\AppData\Roaming\Typora\typora-user-images\image-20210824165514729.png)

+ 计算的方法有一下几种，一种是直接暴力取出所有的字符，然后进行排序。

+ 另一种是使用倍增进行求解。首先，对长度为1的后缀字符串进行排序，然后对后缀长度为2的字符串进行陪需，然后对后缀字符串长度为4的进行排序。依次类推，最后得到的所有的长度。

+ 倍增方法的代码如下，时间复杂度为$O(nlog^{2}n)$

  ```C++
  #include<bits/stdc++.h>
  using namespace std;
  const int N=100010;
  
  char s[N];
  
  int n,sa[N],rk[N<<1],oldrk[N<<1];
  int main(){
      scanf("%s",s+1);
      n=strlen(s+1);
      
      for(int i=1;i<=n;i++){
          sa[i]=i;
          rk[i]=s[i];
      }
      
      for(int w=1;w<n;w<<=1){
          // 按照字符串的首位字符进行排序,
          // 此时sa是按照字符大小相对顺序进行排列
          sort(sa+1,sa+1+n,[](int x,int y){
              // 先比较最开始的字符，然后比较最后的那个字符
             return rk[x]==rk[y]?rk[x+w]<rk[y+w]:rk[x]<rk[y];
          });
          
          memcpy(oldrk,rk,sizeof(rk));
          
          for(int p=0,i=1;i<=n;i++){
              if(oldrk[sa[i]+w]==oldrk[sa[i-1]]&&oldrk[sa[i]+w]==oldrk[sa[i-1]+w]) {
                  rk[sa[i]]=p; // 如果出现字符相等，那么进行去重
              }else{
                  // 否则进行复制排位
                  rk[sa[i]]=++p;
              }
          }
      }
      
      for(int i=1;i<=n;i++){
          printf("%d ",sa[i]);
      }
      puts("");
      return 0;
  }
  ```

+ 接下来，为了更优的时间复杂度，可以使用基数排序进行处理。

##### 基数排序

+ 基数排序是利用将一个数进行拆解成多个关键字，然后利用关键字进行处理。先对第1关键字进行排序，然后对第2关键字进行排序，类推，最后得到全部排序。这种的算法对空间的耗费比较大。但是对时间比较友好。

+ ```C++
  const int N = 100010;
  const int W = 100010;
  const int K = 100;
  
  int n, w[K], k, cnt[W];
  
  struct Element {
    int key[K];
    bool operator<(const Element& y) const {
      // 两个元素的比较流程
      for (int i = 1; i <= k; ++i) {
        if (key[i] == y.key[i]) continue;
        return key[i] < y.key[i];
      }
      return false;
    }
  } a[N], b[N];
  
  void counting_sort(int p) {
    memset(cnt, 0, sizeof(cnt));
    for (int i = 1; i <= n; ++i) ++cnt[a[i].key[p]];
    for (int i = 1; i <= w[p]; ++i) cnt[i] += cnt[i - 1];
    // 为保证排序的稳定性，此处循环i应从n到1
    // 即当两元素关键字的值相同时，原先排在后面的元素在排序后仍应排在后面
    for (int i = n; i >= 1; --i) b[cnt[a[i].key[p]]--] = a[i];
    memcpy(a, b, sizeof(a));
  }
  
  void radix_sort() {
    for (int i = k; i >= 1; --i) {
      // 借助计数排序完成对关键字的排序
      counting_sort(i);
    }
  }
  ```

+ 利用基数排序可以把里面的sort排序优化成O(n），故时间复杂度降为$O(nlogn)$

+ ```C++
  // 常数太大
  #include <algorithm>
  #include <cstdio>
  #include <cstring>
  #include <iostream>
  
  using namespace std;
  
  const int N = 1000010;
  
  char s[N];
  int n, sa[N], rk[N << 1], oldrk[N << 1], id[N], cnt[N];
  
  int main() {
    int i, m, p, w;
  
    scanf("%s", s + 1);
    n = strlen(s + 1);
    m = max(n, 300);
    for (i = 1; i <= n; ++i) ++cnt[rk[i] = s[i]];
    for (i = 1; i <= m; ++i) cnt[i] += cnt[i - 1];
    for (i = n; i >= 1; --i) sa[cnt[rk[i]]--] = i;
  
    for (w = 1; w < n; w <<= 1) {
      memset(cnt, 0, sizeof(cnt));
      for (i = 1; i <= n; ++i) id[i] = sa[i];
      for (i = 1; i <= n; ++i) ++cnt[rk[id[i] + w]];
      for (i = 1; i <= m; ++i) cnt[i] += cnt[i - 1];
      for (i = n; i >= 1; --i) sa[cnt[rk[id[i] + w]]--] = id[i];
      memset(cnt, 0, sizeof(cnt));
      for (i = 1; i <= n; ++i) id[i] = sa[i];
      for (i = 1; i <= n; ++i) ++cnt[rk[id[i]]];
      for (i = 1; i <= m; ++i) cnt[i] += cnt[i - 1];
      for (i = n; i >= 1; --i) sa[cnt[rk[id[i]]]--] = id[i];
      memcpy(oldrk, rk, sizeof(rk));
      for (p = 0, i = 1; i <= n; ++i) {
        if (oldrk[sa[i]] == oldrk[sa[i - 1]] &&
            oldrk[sa[i] + w] == oldrk[sa[i - 1] + w]) {
          rk[sa[i]] = p;
        } else {
          rk[sa[i]] = ++p;
        }
      }
    }
  
    for (i = 1; i <= n; ++i) printf("%d ", sa[i]);
  
    return 0;
  }
  ```

+ ```C++
  // 常数小
  #include <algorithm>
  #include <cstdio>
  #include <cstring>
  #include <iostream>
  
  using namespace std;
  
  const int N = 1000010;
  
  char s[N];
  int n, sa[N], rk[N], oldrk[N << 1], id[N], px[N], cnt[N];
  // px[i] = rk[id[i]]（用于排序的数组所以叫 px）
  
  bool cmp(int x, int y, int w) {
    return oldrk[x] == oldrk[y] && oldrk[x + w] == oldrk[y + w];
  }
  
  int main() {
    int i, m = 300, p, w;
  
    scanf("%s", s + 1);
    n = strlen(s + 1);
    for (i = 1; i <= n; ++i) ++cnt[rk[i] = s[i]];
    for (i = 1; i <= m; ++i) cnt[i] += cnt[i - 1];
    for (i = n; i >= 1; --i) sa[cnt[rk[i]]--] = i;
  
    for (w = 1;; w <<= 1, m = p) {  // m=p 就是优化计数排序值域
      for (p = 0, i = n; i > n - w; --i) id[++p] = i;
      for (i = 1; i <= n; ++i)
        if (sa[i] > w) id[++p] = sa[i] - w;
      memset(cnt, 0, sizeof(cnt));
      for (i = 1; i <= n; ++i) ++cnt[px[i] = rk[id[i]]];
      for (i = 1; i <= m; ++i) cnt[i] += cnt[i - 1];
      for (i = n; i >= 1; --i) sa[cnt[px[i]]--] = id[i];
      memcpy(oldrk, rk, sizeof(rk));
      for (p = 0, i = 1; i <= n; ++i)
        rk[sa[i]] = cmp(sa[i], sa[i - 1], w) ? p : ++p;
      if (p == n) {
        for (int i = 1; i <= n; ++i) sa[rk[i]] = i;
        break;
      }
    }
  
    for (i = 1; i <= n; ++i) printf("%d ", sa[i]);
  
    return 0;
  }
  ```

+ 求$sa$数组和$height$数组的模板

```C++
int  n,k,m;
char s[maxn];
int w[maxn],sum[maxn];
int rk1[maxn],sa[maxn],tp1[maxn],tax[maxn],height[maxn];
int *rk=rk1, *tp=tp1;

void bucket()  
{
	for(int i=0; i<=m; ++i) tax[i]=0;
	for(int i=1; i<=n; ++i) tax[rk[i]]++;
	for(int i=1; i<=m; ++i) tax[i]+=tax[i-1];
	for(int i=n; i>=1; i--) sa[tax[rk[tp[i]]]--]=tp[i];
}

void get_sa() 
{
	m=128;
	for(int i=1; i<=n; i++) rk[i]=s[i],tp[i]=i;
	bucket();
	for(int w=1,p=0; p<n; m=p,w<<=1)
	{
		p=0;
		for(int i=1; i<=w; i++) tp[++p]=n-w+i;
		for(int i=1; i<=n; i++) 
			if(sa[i]>w) tp[++p]=sa[i]-w;
		bucket();
		swap(tp,rk);
		rk[sa[1]]=p=1;
		for(int i=2;i<=n;++i)
			rk[sa[i]]=(tp[sa[i-1]]==tp[sa[i]] && tp[sa[i-1]+w]==tp[sa[i]+w])?p:++p;
	}
}

void get_height()
{
	int k=0;
	for(int i=1; i<=n; i++)
	{
		if(k) k--;
		int j=sa[rk[i]-1];
		while(s[i+k]==s[j+k]) ++k;
		height[rk[i]]=k;
	}
}
```

##### Height数组

+ $LCP$就是两个字符串的最长公共前缀，我们定义$lcp(i,j)$表示后缀字符串i和后缀字符串j的最长公共前缀的长度。
+ 定义：$height[i]=lcp(sa[i],sa[i-1])$,也就是第i名后缀和它前一名的后缀的最长公共前缀。其中$height[1]=0$
+ 引理，$height[rk[i]]>=height[rk[i-1]]-1$
+ $O(n)$求$height$数组代码实现
```C++
for(i=1,k=0;i<=n;i++){
	if(k) --k;
	while(s[i+k]==s[sa[rk[i]-1]+k])  ++k;
	ht[rk[i]]=k;
}
```

+ 相关应用
  + 两个字符串的最长公共前缀
  
  + 比较一个字符串的两个子串的大小
  
  + 求出多个字符串的最长公共子串
  
    + 将n个字符串拼接起来，跑一个$sa$和$height$数组，然后二分这个公共子串的长度，接着利用$height$数组判断是否右连续的大于二分的$mid$的值，并且是来自$n$个不同的字符串，维护答案即可。
    + 模板如下
  
    ```C++
    #include<cstdio>
    #include<cstring>
    #include<iostream>
    #define rint register int
    const int N=1e6+10;
    using namespace std;
    int s[N];
    int y[N],x[N],c[N],sa[N],rk[N],height[N];
    int n, num, belong[N], pos, vis[N];
    char t[N];
    inline void get_SA() {
        for(int i=0;i<=n;++i){
            c[i]=x[i]=y[i]=sa[i]=rk[i]=height[i]=0;
        }
        int m = 5000; //ascll('z')=122
        for(int i=0;i<=m;++i) c[i] = 0;
     
    	for (int i=1; i<=n; ++i) ++c[x[i]=s[i]];//桶
    	for (int i=2; i<=m; ++i) c[i]+=c[i-1];//求前缀和
    	for (int i=n; i>=1; --i) sa[c[x[i]]--]=i;//逆着标序 得到sa
    	for (int k=1; k<=n; k<<=1){ //倍增合并
    		int num=0;
    		for (int i=n-k+1; i<=n; ++i) y[++num]=i;//没有第二关键字，排名在最前
    		for (int i=1; i<=n; ++i) if (sa[i]>k) y[++num]=sa[i]-k;
     
    		for (int i=1; i<=m; ++i) c[i]=0;       //初始化桶
    		for (int i=1; i<=n; ++i) ++c[x[i]];    //桶
    		for (int i=2; i<=m; ++i) c[i]+=c[i-1]; //求前缀和
    		for (int i=n; i>=1; --i) sa[c[x[y[i]]]--]=y[i],y[i]=0;//第二关键字真正的排了序
            for(int i=1;i<=n;++i) y[i] = x[i];
    		//swap(x,y);
    		x[sa[1]] = num = 1;
    		for (int i=2; i<=n; ++i) //对第二关键字进行了简单的桶分类保存至x数组中
    			x[sa[i]]=(y[sa[i]]==y[sa[i-1]] && y[sa[i]+k]==y[sa[i-1]+k]) ? num : ++num;
     
    		if (num==n) break;
    		m=num;
    	}
    	//for (int i=1; i<=n; ++i) printf("%d ", sa[i]);
    }
    int check(int x,int n)
    {
        for(int i=1;i<=n-1;i++)
        {
            if(height[i]<x) continue;
            int cnt=0;
            for(int j=0;j<=num;j++) vis[j]=0;
            while(height[i]>=x&&i<=n-1)
            {
                if(!vis[belong[sa[i-1]]])
                {
                    vis[belong[sa[i-1]]]=1;
                    cnt++;
                }
                i++;
            }
            if(!vis[belong[sa[i-1]]])
            {
                vis[belong[sa[i-1]]]=1;
                cnt++;
            }
            if(cnt>=num)
            {
                pos=sa[i-1];
                return true;
            }
        }
        return false;
    }
    inline void get_height() {
    	int k=0;
    	for (int i=1; i<=n; ++i) rk[sa[i]]=i;
    	for (int i=1; i<=n; ++i){
    		if (rk[i]==1) continue;//第一名height为0
    		if (k) --k;   //h[i]>=h[i-1]-1;
    		int j=sa[rk[i]-1];
    		while (j+k<=n && i+k<=n && s[i+k]==s[j+k]) ++k;
    		height[rk[i]]=k;//h[i]=height[rk[i]];
    	}
    //	puts("");
    //	for(int i = 1; i <= n; ++i) printf("%d ",height[i]);
    }
    inline bool run(int mid)
    {
     
        for(int i = 1; i <= n; ++i){
     
            if(height[i] < mid) continue;
            for(int j = 0; j <= num; ++j) vis[j] = 0;
            int cnt  = 0;
            while(i <= n && height[i] >= mid){
     
                if(!vis[belong[sa[i-1]]]){
                    vis[belong[sa[i-1]]] = 1;
                    cnt++;
                }
                ++i;
            }
            if(!vis[belong[sa[i-1]]]){
                vis[belong[sa[i-1]]] = 1;
                cnt++;
            }
            if(cnt >= num){
                pos = sa[i-1];
                return 1;
            }
     
        }
        return 0;
    }
    int main() {
        while(~scanf("%d", &num)){
            if(num == 0) break;
            n = 0;
            int tar = 123;
            for(int i = 1; i <= num; ++i){
                scanf("%s", t + 1);
                int len = strlen(t + 1);
                // 将num个字符串拼接起来
                for(int j = 1; j <= len; ++j){
                    s[++n] = t[j] - 'a' + 1;
                    belong[n] = i;
                }
                s[++n]=++tar;
                belong[n] = 0;
            }
     
            s[n+1]=0;
            //cout<<s+1<<endl;
            // 求后缀数组和height数组
            get_SA();
            get_height();
            int  l = 1, r = 200, ans = 0;
            pos = 0;
            while(l <= r){
                int mid = l + r >> 1;
                if(run(mid)) ans = mid, l = mid + 1;
                else r = mid - 1;
            }
            if(!ans) puts("IDENTITY LOST");
            else{
                for(int i = pos; i < pos + ans; ++i) printf("%c", s[i] - 1 +'a');
                puts("");
            }
        }
    }
    /*
    3
    aabbaabb
    abbababb
    bbbbbabb
    */
    ```
  
    
  
  + 不同的子串的数目
  
    + 按照后缀排序之后，每次新增的子串就是除了与上一个后缀的$LCP$剩下的前缀。这些前缀一定是新增的。$\frac{{n}{(n+1)}}{2}-\sum_{i=2}^n{height[i]}$
    + 求一下后缀数组和height数组即可。
  
  + 出现第$k$次的子串的最大长度
  
    + 出现k次意味着按照后缀排序之后存在至少k个连续的后缀的$LCP$是这个子串，所以先求出连续的k-1个$height$的最小值，然后这些最小值中的最大值就是答案。使用单调队列维护最大值即可。
  
    + 代码如下
  
    ```C++
    #include <cstdio>
    #include <cstring>
    #include <iostream>
    #include <set>
    
    using namespace std;
    
    const int N = 40010;
    
    int n, k, a[N], sa[N], rk[N], oldrk[N], id[N], px[N], cnt[1000010], ht[N], ans;
    multiset<int> t;  // multiset 是最好写的实现方式
    
    bool cmp(int x, int y, int w) {
      return oldrk[x] == oldrk[y] && oldrk[x + w] == oldrk[y + w];
    }
    
    int main() {
      int i, j, w, p, m = 1000000;
    
      scanf("%d%d", &n, &k);
      --k;
    
      for (i = 1; i <= n; ++i) scanf("%d", a + i);  //求后缀数组
      for (i = 1; i <= n; ++i) ++cnt[rk[i] = a[i]];
      for (i = 1; i <= m; ++i) cnt[i] += cnt[i - 1];
      for (i = n; i >= 1; --i) sa[cnt[rk[i]]--] = i;
    
      for (w = 1; w < n; w <<= 1, m = p) {
        for (p = 0, i = n; i > n - w; --i) id[++p] = i;
        for (i = 1; i <= n; ++i)
          if (sa[i] > w) id[++p] = sa[i] - w;
        memset(cnt, 0, sizeof(cnt));
        for (i = 1; i <= n; ++i) ++cnt[px[i] = rk[id[i]]];
        for (i = 1; i <= m; ++i) cnt[i] += cnt[i - 1];
        for (i = n; i >= 1; --i) sa[cnt[px[i]]--] = id[i];
        memcpy(oldrk, rk, sizeof(rk));
        for (p = 0, i = 1; i <= n; ++i)
          rk[sa[i]] = cmp(sa[i], sa[i - 1], w) ? p : ++p;
      }
    
      for (i = 1, j = 0; i <= n; ++i) {  //求 height
        if (j) --j;
        while (a[i + j] == a[sa[rk[i] - 1] + j]) ++j;
        ht[rk[i]] = j;
      }
    
      for (i = 1; i <= n; ++i) {  //求所有最小值的最大值
        t.insert(ht[i]);
        if (i > k) t.erase(t.find(ht[i - k]));
        ans = max(ans, *t.begin());
      }
    
      cout << ans;
    
      return 0;
    }
    ```
  
  + 是否有某字符串在文本串中至少不重叠出现了两次
  
  + 连续若干个相同的子串

##### 后缀自动机

+ 主要解决的我呢提是一个字符串中搜索另一个字符串的所有的位置。

+ 计算给定的字符串中有多少个不同的子串。

+ 首先，我们需要明确几个定义

  + 结束位置$endpos$，就是某一个字符串的所有的结束的位置。这里可以看作一个集合。SAM中的每个状态对应的是一个或者多个$endpos$相同的子串。就是SAM的状态数等于所有子串的等价类的个数，在加上初始的状态。

  + 给出几个引理

    + 字符串s的两个非空子串u和w的$endpos$相同，当且仅当字符串u在s中的每次出现，都是以w后缀的形式出现的。
    + 如果字符串s的两个子串u和v。如果两个字符串的$endpos$相交为空的话，要么是包含关系，要么相交为空的关系。
    + 考虑一个$endpos$的等价类，将类中的所有子串按长度非递增的顺序排列。每个子串都不会比他的前一个子串长。与此同时每个子串也是它前一个子串的后缀。换句话说，**对于统一等价类的任意两子串，较短者为较长者的后缀**。且该等价类中的子串长度恰好覆盖整个区间$[x,y]$

  + 后缀链接link

    + 一个后缀链接连接到对应于w的最长后缀的另一个$endpos$等价类的状态。
    + 给出几个引理
      + 所有的后缀连接构成了一棵根节点为$t0$的树
      + 通过$endpos$结合构造的树，与通过后缀连接构造的树相同。

  + 相关模板

    + 检查字符串是否出现(给一个文本串和多个匹配串，需要多次判断是否是子串)

    + 不同子串的个数(每次新添加一个字符进入自动机)

    ```C++
    #include<bits/stdc++.h>
    
    using namespace std;
    
    #define int long long
    const int N=1e5+10;
    
    int n;
    
    struct SAM{
    	int len,fa;
    	unordered_map<int,int> nex;
    }tree[N<<2];
    
    int tot,last;
    int ans=0;
    
    void insert(int x){
    	int now=++tot;
    	tree[now].len=tree[last].len+1;
    	int p=last;
    	
    	while(p!=-1&&!tree[p].nex.count(x)){
    		tree[p].nex[x]=now;
    		p=tree[p].fa;
    	}
    	
    	if(p==-1) tree[now].fa=0;
    	else{
    		int q=tree[p].nex[x];
    		if(tree[p].len+1==tree[q].len){
    			tree[now].fa=q;
    		}else{
    			int cur=++tot;
    			tree[cur].len=tree[p].len+1;
    			tree[cur].fa=tree[q].fa;
    			tree[cur].nex=tree[q].nex;
    			while(p!=-1&&tree[p].nex[x]==q){
    				tree[p].nex[x]=cur;
    				p=tree[p].fa;
    			}
    			
    			tree[q].fa=tree[now].fa=cur;
    		}
    	}
    	
    	last=now;
    	
    	ans+=tree[now].len-tree[tree[now].fa].len;
    }
    
    void init(){
    	tree[0].len=0,tree[0].fa=-1;
    	last=0;
    }
    
    signed main(){
    	scanf("%lld",&n);
    	init();
    	while(n--){
    		int x;
    		scanf("%lld",&x);
    		insert(x);
    		
    		printf("%lld\n",ans);
    	}
    	return 0;
    }
    ```

    

    + 所有不同子串的长度
    + 字典序第k大子串
    + 最小循环移位
    + 出现次数
    + 第一次出现位置
    + 所有出现位置
    + 最短的没有出现的字符串
    + 两个字符串的最长公共子串

    ```C++
    #include <stdio.h>
    #include <string.h>
    #define N 250010
    int len=0,ans=0,n=0,last,root,son[N<<1][26],fa[N<<1],mx[N<<1];  
    char str1[N],str2[N];
    void ins(int ch){
        int p=last,np=++n;last=np;mx[np]=mx[p]+1;
        while(p && !son[p][ch]) son[p][ch]=np,p=fa[p];
        if(!p) fa[np]=root;
        else{
            int q=son[p][ch];
            if(mx[q]==mx[p]+1) fa[np]=q;
            else{
                int nq=++n;mx[nq]=mx[p]+1;
                memcpy(son[nq],son[q],sizeof(son[q]));
                fa[nq]=fa[q];fa[q]=fa[np]=nq;
                while(son[p][ch]==q) son[p][ch]=nq,p=fa[p];
            }
        }
    } 
    int main(){
        // 输入两个待匹配的字符串
        scanf("%s%s",str1+1,str2+1);
        int len1=strlen(str1+1),len2=strlen(str2+1);root=last=++n;
        for(int i=1;i<=len1;i++) ins(str1[i]-'a');
        int p=root;
        for(int i=1;i<=len2;i++){
            if(son[p][str2[i]-'a']) len++,p=son[p][str2[i]-'a'];
            else{
                while(p && !son[p][str2[i]-'a']) p=fa[p];
                if(!p) len=0,p=root;
                else len=mx[p]+1,p=son[p][str2[i]-'a'];
            }if(len>ans) ans=len;
        }printf("%d\n",ans);
        return 0;
    } 
    ```

    + **多个字符串之间的最长公共子串**

    ```C++
    #include <stdio.h>
    #include <string.h>
    #define N 100010
    #define inf 0x7fffffff
    char str[N];
    int n,last,root,cnt=0,ans=0,len[N<<1],son[N<<1][26],fa[N<<1],mx[N<<1],mn[N<<1],a[N<<1],c[N<<1];
    inline int max(int x,int y){
        if(x>y) return x;
        return y;
    }
    inline int min(int x,int y){
        if(x<y) return x;
        return y;
    }
    inline void ins(int ch){
        int p=last,np=++cnt;last=np;len[np]=len[p]+1;
        while(p && !son[p][ch]) son[p][ch]=np,p=fa[p];
        if(!p) fa[np]=root;
        else{
            int q=son[p][ch];
            if(len[q]==len[p]+1) fa[np]=q;
            else{
                int nq=++cnt;len[nq]=len[p]+1;
                memcpy(son[nq],son[q],sizeof(son[nq]));
                fa[nq]=fa[q];fa[q]=fa[np]=nq;
                while(son[p][ch]==q) son[p][ch]=nq,p=fa[p];
            }
        }
    }
    int main(){
        // 输入n个字符串，要求这些字符串的最长公共子串
        scanf("%s",str+1);last=root=++cnt;n=strlen(str+1);
        for(int i=1;i<=n;i++) ins(str[i]-'a');
        for(int i=1;i<=cnt;i++) c[len[i]]++;
        for(int i=1;i<=cnt;i++) c[i]+=c[i-1];
        for(int i=cnt;i>=1;i--) a[c[len[i]]--]=i;
        for(int i=1;i<=cnt;i++) mn[i]=inf;
        memset(mx,0,sizeof(mx));
        while(scanf("%s",str+1)>0){
            n=strlen(str+1);int p=root,sz=0;
            for(int i=1;i<=n;i++){
                if(son[p][str[i]-'a']) sz++,p=son[p][str[i]-'a'];
                else{
                    while(p&&!son[p][str[i]-'a']) p=fa[p];
                    if(!p) sz=0,p=root;
                    else sz=len[p]+1,p=son[p][str[i]-'a'];
                }mx[p]=max(mx[p],sz);
            }for(int i=1;i<=cnt;i++){
                int p=a[i];
                mn[p]=min(mn[p],mx[p]);
                mx[fa[p]]=max(mx[fa[p]],mx[p]);
                mx[p]=0;
            }
        }for(int i=1;i<=cnt;i++) if(mn[i]!=inf) ans=max(mn[i],ans);
        printf("%d\n",ans);
        return 0;
    } 
    ```

    

#### ac自动机

+ ac自动机是KMP+Tire两种算法的综合，它解决的问题是如何查询一个文本串可以与多少个模式串的匹配。

+ ac自动机是将所有的模式串构造成一棵Trie，我们称之为Trie树。然后利用文本串在ac自动机上面行走进行查找匹配。

+ 同时，我们要引入一个失配指针fail的概念，失配指针是指一个字符串中匹配当前状态的最长后缀。可以将它和KMP算法的next数组比较起来理解。fail指针就是一个模式串的后缀集合，也就是如果i->fail=j，那么word[j]的最长后缀指向的是word[i]

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
              if(tr[u][i]){  // 如果这个结点存在，那么直接根据它的父节点的fail指针找到它的fail指针
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
          scanf("%s",s+1); // 待匹配的子串
          insert(s);
      }
      scanf("%s",s+1);  // 文本串
      build();
      printf("%d\n",query(s));
      fclose(stdout);
      return 0;
  }
  ```

#####  常用模板

+ 这里存一个模板，**对于n个字符串，求在母串里面出现的次数，这个模板包括相交的和不相交的，询问0表示相交的，询问1表示不相交的子串的个数**。用ac自动机可以在很好的时间复杂度内解决这个问题。


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



#### FFT

+ FFT是用来求多项式系数的一个数学变换方法，原理比较复杂，故只做了解，存几个模板

+ 给出f(x)和g(x)的系数，如何求f(x)*g(x)的系数

  + 递归版本

  ```C++
  // luogu-judger-enable-o2
  // luogu-judger-enable-o2
  #include<iostream>
  #include<cstdio>
  #include<cmath>
  using namespace std;
  const int MAXN = 4 * 1e6 + 10;
  inline int read() {
      char c = getchar(); int x = 0, f = 1;
      while (c < '0' || c > '9') {if (c == '-')f = -1; c = getchar();}
      while (c >= '0' && c <= '9') {x = x * 10 + c - '0'; c = getchar();}
      return x * f;
  }
  const double Pi = acos(-1.0);
  struct complex {
      double x, y;
      complex (double xx = 0, double yy = 0) {x = xx, y = yy;}
  } a[MAXN], b[MAXN];
  complex operator + (complex a, complex b) { return complex(a.x + b.x , a.y + b.y);}
  complex operator - (complex a, complex b) { return complex(a.x - b.x , a.y - b.y);}
  complex operator * (complex a, complex b) { return complex(a.x * b.x - a.y * b.y , a.x * b.y + a.y * b.x);} //不懂的看复数的运算那部分
  void fast_fast_tle(int limit, complex *a, int type) {
      if (limit == 1) return ; //只有一个常数项
      complex a1[limit >> 1], a2[limit >> 1];
      for (int i = 0; i <= limit; i += 2) //根据下标的奇偶性分类
          a1[i >> 1] = a[i], a2[i >> 1] = a[i + 1];
      fast_fast_tle(limit >> 1, a1, type);
      fast_fast_tle(limit >> 1, a2, type);
      complex Wn = complex(cos(2.0 * Pi / limit) , type * sin(2.0 * Pi / limit)), w = complex(1, 0);
      //Wn为单位根，w表示幂
      for (int i = 0; i < (limit >> 1); i++, w = w * Wn) //这里的w相当于公式中的k
          a[i] = a1[i] + w * a2[i],
                 a[i + (limit >> 1)] = a1[i] - w * a2[i]; //利用单位根的性质，O(1)得到另一部分
  }
  int main() {
      int N = read(), M = read();
      for (int i = 0; i <= N; i++) a[i].x = read();
      for (int i = 0; i <= M; i++) b[i].x = read();
      int limit = 1; while (limit <= N + M) limit <<= 1;
      fast_fast_tle(limit, a, 1);
      fast_fast_tle(limit, b, 1);
      //后面的1表示要进行的变换是什么类型
      //1表示从系数变为点值
      //-1表示从点值变为系数
      //至于为什么这样是对的，可以参考一下c向量的推导过程，
      for (int i = 0; i <= limit; i++)
          a[i] = a[i] * b[i];
      fast_fast_tle(limit, a, -1);
      for (int i = 0; i <= N + M; i++) printf("%d ", (int)(a[i].x / limit + 0.5)); //按照我们推倒的公式，这里还要除以n
      return 0;
  }
  
  递归版
  ```

  + 迭代版本

  ```C++
  // 这是迭代写法，时间复杂度较优
  #include<bits/stdc++.h>
  #define eps 1e-6
  using namespace std;
  const int N=4e6+10;
  const double pi=acos(-1);
  struct Complex{
      double x,y;
      Complex(double x=0,double y=0):x(x),y(y){}
  };
  Complex operator+(Complex a,Complex b){return Complex(a.x+b.x,a.y+b.y);}
  Complex operator-(Complex a,Complex b){return Complex(a.x-b.x,a.y-b.y);}
  Complex operator*(Complex a,Complex b){return Complex(a.x*b.x-a.y*b.y,a.x*b.y+a.y*b.x);}
  int limit,l;
  int r[N];
  void FFT(Complex*f,int op){
      for(int i=0;i<limit;++i){
          if(i<r[i])swap(f[i],f[r[i]]); 
      }
      for(int mid=2;mid<=limit;mid*=2){
          Complex wn=Complex(cos(2*pi/mid),op*sin(2*pi/mid));
          for(int j=0;j<limit;j+=mid){
              Complex w=Complex(1,0);
              for(int k=j;k<j+mid/2;++k,w=w*wn){
                  Complex x=f[k],y=w*f[k+mid/2];
                  f[k]=x+y;
                  f[k+mid/2]=x-y;
              }
          }
      }
      if(op==-1){
          for(int i=0;i<limit;++i){
              f[i].x/=limit;
          }
      }
  }
  int n,m;
  Complex a[N],b[N]; 
  int main(){
      cin>>n>>m;
      for(int i=0;i<=n;++i)scanf("%lf",&a[i].x);
      for(int i=0;i<=m;++i)scanf("%lf",&b[i].x);
      for(limit=1,l=0;limit<=n+m;limit*=2,l++);
      for(int i=0;i<limit;++i)r[i]=(r[i>>1]>>1)|((i&1)<<(l-1));
      FFT(a,1),FFT(b,1);
      for(int i=0;i<limit;++i)a[i]=a[i]*b[i];
      FFT(a,-1);
      for(int i=0;i<=n+m;++i){
      	if(fabs(a[i].x)<eps)printf("0 ");
          else printf("%.0lf ",a[i].x);
      }
  }
  ```

  

