```c++
#pragma once
#include<vector>
#ifndef MYSORT_H_
#define MYSORT_H_
using namespace std;


class MySort {
private:
	void MySwap(vector<int>& Arr, int idx, int idy);
	void ImplementationMergeSortRecursion(vector<int>& Arr, int left, int right);
	void Merge(vector<int>& Arr, int left, int mid, int right);
	int BulidHeap(vector<int>& Arr,int n);
	void Heapify(vector<int>& Arr, int index,int size);
	void ImplementationQuickSort(vector<int>& Arr, int left, int right);
public:
	MySort();
	void BubbleSort(vector<int>& Arr);
	void SelectionSort(vector<int>& Arr);
	void InsertionSort(vector<int>& Arr);
	void ShellSort(vector<int>& Arr);
	void MergeSortRecursion(vector<int>& Arr);
	void MergeSortIteration(vector<int>& Arr);
	void HeapSort(vector<int>& Arr);
	void QuickSort(vector<int>& Arr);
};



```

