# **Binary Algorithm**



```c++
/* 一个关于二分的模板 */


#include <iostream>
#include <vector>

template <typename T>
int binarySearch(const std::vector<T>& arr, const T& target) {
    int left = 0;
    int right = arr.size() - 1; // 该二分的右边界是有意义的

    while (left <= right) {
        int mid = left + (right - left) / 2;

        if (arr[mid] == target) {
            return mid;
        } else if (arr[mid] < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }

    return -1; // 如果未找到目标元素，返回-1表示失败
}


/* 一个运用的例子 */

int main() {
    std::vector<int> arr = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    int target = 6;

    int result = binarySearch(arr, target);

    if (result != -1) {
        std::cout << "目标元素 " << target << " 在数组中的索引是 " << result << std::endl;
    } else {
        std::cout << "未找到目标元素 " << target << std::endl;
    }

    return 0;
}










```

