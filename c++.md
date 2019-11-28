[TOC]

## 基础用法

- 

### foreach 循环

```c++
// 把数组的元素都输出到屏幕上；然后把数组的元素翻倍
auto array[] = {2,1,4,3,5};
for(auto i: array) { cout << i << endl ;}
for(auto& i: array) { i = 2* i ; }
```

### 哨兵的作用

```c++
// 顺序查找时设置哨兵，可以优化性能
// 普通：
int Sequential_Search(int *a,int n,int key) {
	//数组从1开始
	int i;
	for(int i=1;i<=n;i++) {
		if(a[i]==key)
			return i;
	}
	return 0;//查找失败
}
// 哨兵：
int Sequential_Search2(int *a int n,int key) {
	int i=0;
	a[0]=key;//哨兵
	i=n;
	while(a[i]!=key) {
		i--;
	}
	return i;//返回0就是查找失败
}
// 当n值很大时，用哨兵，少了i<n这个比较操作。
```

