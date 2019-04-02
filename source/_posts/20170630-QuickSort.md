---
title: 퀵정렬 (QuickSort)
date: 2017-06-30 14:36:18
categories:
- Algorithm
- Sort
tags:
- Data Structure
- Sort
- Quick Sort
thumbnail: /images/datastructure.jpg

---
### 퀵정렬
퀵 정렬 O(nlog2n)- 피벗을 정하여 분할하여 정복.. 피벗을 선택 할때 3개를 선택하여 평균치를 구하여 구하면 더 효율적으로 피벗을 정할 수 있다.
피벗이 잘 선택될 경우 시간복잡도가 줄어든다.

최악의 경우 O(n^2)이 될 수 있다  (피벗값이 많이 안좋을 경우)

O(nlog2n)의 시간복잡도를 갖는 다른 정렬 알고리즘 보다 평균적으로 빠르다.
why ? 데이터 이동이 상대적으로 작고 병합정렬과 같이 별도의 메모리 공간을 요구하지 않기 때문에..

나만의 요약! : 피벗값을 기준으로 처음지점부터 시작한 반복문은 피벗보다 작은값을 찾고 뒤에서 시작한 반복문은 피벗보다 큰값을 찾아 서로 바꾼다. ( 단 피벗을 지나치치 않음)

{% codeblock lang:c QuickSort.c  %}
void Swap(int arr[], int idx1, int idx2)
{
	int temp = arr[idx1];
	arr[idx1] = arr[idx2];
	arr[idx2] = temp;
}


int Partition(int arr[], int left, int right)
{
	int pivot = arr[left];    // 피벗의 위치는 가장 왼쪽!
	int low = left+1;
	int high = right;

	while(low <= high)    // 교차되지 않을 때까지 반복
	{
		while(pivot > arr[low])
			low++;

		while(pivot < arr[high])
			high--;

		/*
		while(pivot >= arr[low] && low <= right)
			low++;

		while(pivot <= arr[high] && high >= (left+1))
			high--;
		*/

		if(low <= high)    // 교차되지 않은 상태라면 Swap 실행
			Swap(arr, low, high);    // low와 high가 가리키는 대상 교환
	}

	Swap(arr, left, high);    // 피벗과 high가 가리키는 대상 교환
	return high;    // 옮겨진 피벗의 위치 정보 반환
}

void QuickSort(int arr[], int left, int right)
{
	if(left <= right)
	{
		int pivot = Partition(arr, left, right);    // 둘로 나눠서
		QuickSort(arr, left, pivot-1);    // 왼쪽 영역을 정렬
		QuickSort(arr, pivot+1, right);    // 오른쪽 영역을 정렬
	}
}
{% endcodeblock %}

참고 : 윤성우의 열혈 자료구조
