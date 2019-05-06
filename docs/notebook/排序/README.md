待排序的元素需要实现 `Java` 的 `Comparable` 接口，该接口有 `compareTo()` 方法，可以用它来判断两个元素的大小关系。
研究排序算法的成本模型时，统计的是**比较**和**交换**的次数。
使用辅助函数 `less()` 和 `swap()` 来进行比较和交换的操作，使得代码的可读性和可移植性更好。

```java
public abstract class Sort<T extends Comparable<T>> {

	public abstract void sort(T[] nums);
	
	// v 是否比 w 小
	public boolean less(T v, T w){
		return v.compareTo(w) < 0;
	}
	
	// 交换数组中的两个元素
	public void swap(T[] a, int i, int j){
		T temp = a[i];
		a[i] = a[j];
		a[j] = temp;
	}
	
}
```

> 下面是几种排序方法，在下面的排序中我们会直接用到上面的比较和交换方法。

- [选择排序](notebook/排序/选择排序)
- [冒泡排序](notebook/排序/冒泡排序)
- [插入排序](notebook/排序/插入排序)
- [希尔排序](notebook/排序/希尔排序)
- [归并排序](notebook/排序/归并排序)
- [快速排序](notebook/排序/快速排序)
- [堆排序](notebook/排序/堆排序)

