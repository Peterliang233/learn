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

