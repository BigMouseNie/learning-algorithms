```c++
#include<iostream>
#include<vector>
#include"MYSORT.h"

int main() {
	//MySort* Sorted=new MySort(); Sorted->InsertionSort(arr);
	MySort Sorted;
	vector<int> arr = { 9,65,4,3,2,1,3,3,54,32,2,45,6,8,80,64,3,6,78,5,11111,45,6,754,7,23 };
	//Sorted.BubbleSort(arr);
	//Sorted.SelectionSort(arr);
	//Sorted.InsertionSort(arr);
	//Sorted.ShellSort(arr);
	//Sorted.MergeSortRecursion(arr);
	//Sorted.MergeSortIteration(arr);
	//Sorted.HeapSort(arr);
	Sorted.QuickSort(arr);
	for (auto i : arr) {
		cout << i << endl;
	}
	return 0;
}
```

