# Sorting Algorithm By C++



[TOC]





| 排序算法 | 平均时间复杂度 | 空间复杂度 | 排序方式  | 稳定性 |
| -------- | -------------- | ---------- | --------- | ------ |
| 冒泡排序 | O(n^2)         | O(1)       | In-place  | 稳定   |
| 选择排序 | O(n^2)         | O(1)       | In-place  | 不稳定 |
| 插入排序 | O(n^2)         | O(1)       | In-place  | 稳定   |
| 希尔排序 | O(nLog n)      | O(1)       | In-place  | 不稳定 |
| 归并排序 | O(nLog n)      | O(n)       | Out-place | 稳定   |
| 快速排序 | O(nLog n)      | O(Log n)   | In-place  | 不稳定 |
| 堆排序   | O(nLog n)      | O(1)       | In-place  | 不稳定 |
| 计数排序 | O(n+k)         | O(k)       | Out-place | 稳定   |
| 桶排序   | O(n+k)         | O(n+k)     | Out-place | 稳定   |
| 基数排序 | O(n*k)         | O(n+k)     | Out-place | 稳定   |

- **时间/空间复杂度**：描述一个算法执行时间/占用空间与数据规模的增长关系。
- **n**：待排序列的个数。
- **k**：“桶”的个数（上面的三种非比较类排序都是基于“桶”的思想实现的）。
- **In-place**：原地算法，指的是占用常用内存，不占用额外内存。空间复杂度为 O(1) 的都可以认为是原地算法。
- **Out-place**：非原地算法，占用额外内存。
- **稳定性**：假设待排序列中两元素相等，排序前后这两个相等元素的相对位置不变，则认为是稳定的。





#### -冒泡排序

```c++
void MySort::BubbleSort(vector<int>& Arr) {
	int arr_size = Arr.size();
	if (arr_size <= 1) { return; }
	int MaxEml = Arr[0];
	for (int i = arr_size; i >= 0; --i) {
		for (int j = 1; j < i; ++j) {
			if (Arr[j] < Arr[j - 1]) {
				MySwap(Arr, j, j - 1);
			}
		}
	}
	return;
}

```



#### - 选择排序

```c++
void MySort::SelectionSort(vector<int>& Arr) {
	int arr_size = Arr.size();
	for (int i = 0; i < arr_size - 1; ++i) {
		int Min = i;
		for (int j = i + 1; j < arr_size; ++j) {
			if (Arr[Min] > Arr[j]) {
				Min = j;
			}
		}
		MySwap(Arr, Min, i);
	}
}

```



#### - 插入排序 & 希尔排序

```C++
void MySort::InsertionSort(vector<int>& Arr) {
	int arr_size = Arr.size();
	if (arr_size <= 1) { return; }
	for (int i = 1; i < arr_size; ++i) {
		int TEml = Arr[i];
		int j = i - 1;
		while (j >= 0&&Arr[j]>TEml) {
			Arr[j + 1] = Arr[j];
			--j;
		}
		Arr[j+1] = TEml;
	}
}

void MySort::ShellSort(vector<int>& Arr) {
	int h = 0;
	int arr_size = Arr.size();
	while (h <= arr_size) {
		h = h * 3 + 1;
	}
	while (h >= 1) {
		for (int i = h; i < arr_size; ++i) {
			int j = i - h;
			int TEml = Arr[i];
			while (j >= 0 && Arr[j] > TEml) {
				Arr[j + h] = Arr[j];
				j -= h;
			}
			Arr[j + h] = TEml;
		}
		h = (h - 1) / 3;
	}
}

```



#### - 归并排序

```c++
void MySort::Merge(vector<int>& Arr, int left, int mid, int right) {
	int len = right - left + 1;
	vector<int> temp(len);
	int index = 0;
	int i = left, j = mid + 1;
	while (i <= mid && j <= right) {
		temp[index++] = Arr[i] <= Arr[j] ? Arr[i++] : Arr[j++];
	}
	while (i <= mid) { temp[index++] = Arr[i++]; }
	while (j <= right) { temp[index++] = Arr[j++]; }
	for (int k = 0; k < len; ++k) {
		Arr[left++] = temp[k];
	}
}

void MySort::ImplementationMergeSortRecursion(vector<int>& Arr, int left, int right) {
	if (left == right) { return; }
	int mid = (left + right) / 2;
	ImplementationMergeSortRecursion(Arr, left, mid);
	ImplementationMergeSortRecursion(Arr, mid + 1, right);
	Merge(Arr, left, mid, right);
}
void MySort::MergeSortRecursion(vector<int>& Arr) {
	ImplementationMergeSortRecursion(Arr, 0, Arr.size() - 1);
}

void MySort::MergeSortIteration(vector<int>& Arr) {
	int arr_size = Arr.size();
	int left, mid, right;
	for (int i = 1; i < arr_size; i *= 2) {
		left = 0;
		while (left + i < arr_size) {
			mid = left + i - 1;
			right = mid + i < arr_size ? mid + i : arr_size - 1;
			Merge(Arr, left, mid, right);
			left = right+1;
		}
	}
}

```

#### - 快速排序

```C++
void MySort::ImplementationQuickSort(vector<int>& Arr, int left, int right) {
	if (left >= right) { return; }
	int p = Arr[left];
	int i = left;
	int j = right;
	while (i < j) {
		while (i < j&&Arr[j]>=p) { --j; }
		while (i < j&&Arr[i] <= p) { ++i; }
		if (i < j) { MySwap(Arr, i, j); }
	}
	MySwap(Arr, i, left);
	ImplementationQuickSort(Arr, left, i - 1);
	ImplementationQuickSort(Arr, j + 1, right);	
}
void MySort::QuickSort(vector<int>& Arr) {
	ImplementationQuickSort(Arr, 0, Arr.size() - 1);
}
```



#### - 堆排序

```C++
void MySort::Heapify(vector<int>& Arr, int index, int size) {
	int left_child = 2 * index + 1;
	int right_child = 2 * index + 2;
	int max = index;
	if (left_child < size&&Arr[left_child] > Arr[max]) { max = left_child; }
	if (right_child < size&&Arr[right_child] > Arr[max]) { max = right_child; }
	if (max != index) {
		MySwap(Arr, index, max);
		Heapify(Arr, max, size);
	}

}
int MySort::BulidHeap(vector<int>& Arr, int n) {
	int heap_size = n;
	for (int i = heap_size / 2 - 1; i >= 0; --i) {
		Heapify(Arr, i, heap_size);
	}
	return heap_size;
}
void MySort::HeapSort(vector<int>& Arr) {
	int arr_size = Arr.size();
	int heap_size = BulidHeap(Arr, arr_size);
	while (heap_size > 1) {
		MySwap(Arr, 0, --heap_size);
		Heapify(Arr, 0, heap_size);
	}
}

```



#### - 计数排序

#### - 桶排序

#### - 基数排序



























