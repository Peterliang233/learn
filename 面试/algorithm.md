+ 介绍几种面试常见的算法

  + 冒泡排序法

    + 这种算法就是利用两个循环，通过枚举数组里面的两个不同位置的元素，比较这两个数字的大小，如果前面的大于后面的，那么就交换这两个元素的位置。

    + 利用了两重循环，所以时间复杂度为$O(n^2)$.

    + 代码如下

      ```cpp
      #include<bits/stdc++.h>
      
      using namespace std;
      
      
      void BubbleSort(int a[],int left,int right){
      	for(int i=left;i<=right;i++){ // 枚举当前需要放元素的位置
      		for(int j=i+1;j<=right;j++){ // 枚举这个元素的右边的元素，如果比它小就进行交换
      			if(a[i]>a[j]){
      				swap(a[i],a[j]);
      			}
      		}
      	}
      }
      
      
      int main(){
      	int a[10]={0,3,1,2,5,4,7,8,9,6};
      	BubbleSort(a,0,9);
      	for(int i=0;i<10;i++){
      		cout<<a[i]<<" ";
      	}
      	cout<<endl;
      	return 0;
      }
      ```

  + 选择排序法

    + 这种算法就是从左到右依次找到一个标兵，然后，我们找到后面比这个标兵小的数字，用一个变量记录这个标志，然后等里面这层循环结束之后我们交换这个变量和标兵指向的元素即可。

    + 枚举标兵使用了一层循环，寻找比这个标兵小的也需要一个循环，所以总的时间复杂度为$O(n^2)$

    + 代码如下

      ```cpp
      #include<bits/stdc++.h>
      
      using namespace std;
      
      
      void selectSort(int a[],int left,int right){
      	for(int i=left;i<=right;i++){
      		int pos=i; // 假设当前这个位置的数是最小的
      		for(int j=i+1;j<=right;j++){
      			if(a[pos]>a[j]){ // 如果比他更小，那么我们更新这个pos，使得pos始终指向最小的元素的位置
      				pos=j;
      			}
      		}
      		swap(a[pos],a[i]); // 最后将开始假设的位置和pos的位置的元素交换值
      	}
      }
      
      
      int main(){
      	int a[10]={0,3,1,2,5,4,7,8,9,6};
      	selectSort(a,0,9);
      	for(int i=0;i<10;i++){
      		cout<<a[i]<<" ";
      	}
      	cout<<endl;
      	return 0;
      }
      ```

  + 快速排序法

    + 这种算法是利用递归的思想实现的。同样，我们引入一个哨兵的概念，我们选取一个哨兵，也就是数组此刻这个区间里面的任意的一个元素，然后，我们对当前的区间进行处理，我们把这个写成一个函数，这个函数的作用就是通过移动当前这个区间的元素，使得哨兵的左边的元素都比这个哨兵小，哨兵右边的元素都比这个哨兵大。这样，我们就将这个区间分成了局部有序的区间，然后，我们返回这个哨兵的位置，作为下一层递归的左右边界。这样，我们就只需要将区间分$logN$次就行了($N$是区间的长度)。

    + 但是其实这个哨兵的选择还是有讲究的。按理来说我们每次找出当前区间的最左边的元素作为哨兵就行了。但是对于这个区间的所有元素接近有序的情况，显然是会使这个算法递归的次数变为n,这样，时间复杂度又退化成了$O(N^2)$.

    + 对于上面这种情况，我们可以通过对哨兵的选取随机化来进行处理。只要这个数组足够随机，那么我们的算法还是可以达到$O(nlogn)$的

    + 代码如下

      ```cpp
      #include<bits/stdc++.h>
      
      using namespace std;
      
      
      int Partition(int a[],int left,int right){
      	int tmp=a[left];  // 先用一个变量记录初始选定的哨兵的位置
      	while(left<right){
      		while(left<right&&a[right]>tmp) right--; // 一旦出现右边的元素都比这个哨兵小，说明需要将它移到左边，保证我们左边的元素都比哨兵小
      		a[left]=a[right]; // 将找到的比哨兵小的元素放到左边
      		while(left<right&&a[left]<=tmp) left++;  // 一旦出现左边的元素比这个哨兵大，说明需要将它移到右边，保证我们右边的元素都比这个哨兵大
      		a[right]=a[left]; // 将找到的比哨兵大的元素放到右边
      	}
      	a[left]=tmp; // 记得把初始选定的哨兵的数值放到哨兵的位置
      	return left; // 返回这个哨兵的位置
      }
      
      void quickSort(int a[],int left,int right){
      	if(left<right){ // 如果区间的左边小于区间的右边，那么说明可以继续递归下去
      		int pos=Partition(a,left,right);  // 寻找到哨兵的位置，将这个大的区间分为两个小的区间
      		quickSort(a,left,pos-1);  // 对这个元素都比哨兵小的区间继续进行排序
      		quickSort(a,pos+1,right);  // 对这个元素比哨兵大的区间继续进行排序
      	}
      }
      
      
      int main(){
      	int a[10]={0,3,1,2,5,4,7,8,9,6};
      	quickSort(a,0,9);
      	for(int i=0;i<10;i++){
      		cout<<a[i]<<" ";
      	}
      	cout<<endl;
      	return 0;
      }
      
      ```

  + 归并排序法

    + 这种算法最核心的思想是分而治之。我们通过每次将区间进行二等分，这样，我们需要分$logN$次，$N$是区间的长度,然后通过先对小区间进行排序，然后，归并到大的区间进行排序，这样，我们就可以通过小区间的有序实现大区间的有序了。所以总的时间复杂度为$O(nlogn)$

    + 其实这种归并的思想对求逆序对的个数有帮助。

    + 代码如下

      ```cpp
      #include<bits/stdc++.h>
      
      using namespace std;
      
      const int N=12;
      
      void MergeSort(int a[],int left1,int right1,int left2,int right2){
      	// 进入这个区间的时候，我们已经通过之前的操作是得[left1,right1]和[left2,right2]是一个有序的区间了
      	int b[N];
      	int id=0;
      	int i=left1,j=left2;
      	// 一次比较两个区间当前的指针，小的先放
      	while(i<=right1&&j<=right2){
      		if(a[i]<a[j]){
      			b[id++]=a[i];
      			++i;
      		}else{
      			b[id++]=a[j];
      			++j;
      		}
      	}
      	// 将剩余的直接放，因此此刻两个区间一定只有一个区间还有元素
      	while(i<=right1){
      		b[id++]=a[i++];
      	}
      	while(j<=right2){
      		b[id++]=a[j++];
      	}
      	// 然后重新放到a数组里面的对应的位置
      	for(int k=0;k<id;k++){
      		a[left1+k]=b[k];
      	}
      }
      
      void Merge(int a[],int left,int right){
      	if(left<right){
      		// 先将区间进行二分
      		int mid=(left+right)>>1;
      		Merge(a,left,mid);
      		Merge(a,mid+1,right);
      		MergeSort(a,left,mid,mid+1,right);
      	}
      }
      
      
      int main(){
      	int a[10]={0,3,1,2,5,4,7,8,9,6};
      	Merge(a,0,9);
      	for(int i=0;i<10;i++){
      		cout<<a[i]<<" ";
      	}
      	cout<<endl;
      	return 0;
      }
      
      ```

  + 堆排序

    + 首先，我们先根据初始的序列从上到下，从左到右开始创建一个二叉树。显然，这个堆不一定是一个合法的大根堆或者小根堆。这里我们按照创建大根堆的方法来说明。

    + 然后，我们从小往上，从右到左开始遍历每个节点，对于每个节点，我们需要保证他的大小要大于它的所有的子节点的值。如果不满足，就需要进行堆调整。

    + 构建好一个大根堆之后，满足了所有的节点一定大于他们的子节点的大小。

    + 最后，我们倒序枚举这个堆的元素，我们将遍历的节点的值和堆顶的元素交换，此刻，很有可能不满足大根堆的条件了，所以我们需要对这个堆继续进行调整。那么我们下次和根节点交换的时候一定是没有遍历过的节点中的最大的节点了，交换一下即可。重复执行这个操作直到到了堆顶部。

    + 其实这个思想就是一个从大到小的顺序将各元素放到对应的位置上，每次调整的时间复杂度就是树的高度。所以总的时间复杂度为$O(nlogn)$

    + 代码如下

      ```cpp
      #include<bits/stdc++.h>
      
      using namespace std;
      
      // heapBuild 构造一个大根堆
      void heapBuild(int a[],int root,int len){
      	int lchild=root*2+1;
      	if(lchild<len){
      		// 说明有左子节点
      		int flag=lchild;
      		int rchild=lchild+1;
      		if(rchild<len){
      			// 说明有右子节点
      			if(a[rchild]>a[flag]){
      				flag=rchild;
      			}
      		}
      		// 我们用flag找到根节点，左右子节点三个节点中的最大的节点
      		if(a[root]<a[flag]){
      			swap(a[root],a[flag]);
      			// 如果根节点不是最大的，那么需要进行堆调整，注意这个堆的堆顶是flag了
      			heapBuild(a,flag,len);
      		}
      	}
      }
      
      void heapSort(int a[],int len){
      	// 建堆的过程
      	for(int i=len/2;i>=0;--i){ // 找到第一个非叶子节点
      		heapBuild(a,i,len); // 对这个节点以下的堆进行调整
      	}
      
      	// 倒序遍历堆的所有的节点，放到堆顶，然后继续进行调整
      	for(int j=len-1;j>0;--j){
      		swap(a[0],a[j]);
      		heapBuild(a,0,j);
      	}
      }
      
      
      int main(){
      	int a[10]={0,3,1,2,5,4,7,8,9,6};
      	heapSort(a,10);
      	for(int i=0;i<10;i++){
      		cout<<a[i]<<" ";
      	}
      	cout<<endl;
      	return 0;
      }
      
      ```
