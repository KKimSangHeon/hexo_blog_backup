---
title: 병합정렬 (MergeSort)
date: 2017-06-30 14:36:10
categories:
- Algorithm
- Sort
tags:
- Data Structure
- Sort
- Merge Sort
thumbnail: /images/datastructure.jpg
---
### 병합정렬
재귀적으로 분할하여 병합하는 방식으로 재귀적으로 모든 요소들을 쪼갠 후 합쳐나간다. 시간복잡도는 O(nlog2n))이다.


{% codeblock lang:c MergeSort.c  %}
void MergeTwoArea(int arr[], int left, int mid, int right)
{
	int fIdx = left;
	int rIdx = mid+1;
	int i;

	int * sortArr = (int*)malloc(sizeof(int)*(right+1));
	int sIdx = left;

	while(fIdx<=mid && rIdx<=right)
	{
		if(arr[fIdx] <= arr[rIdx])
			sortArr[sIdx] = arr[fIdx++];
		else
			sortArr[sIdx] = arr[rIdx++];

		sIdx++;
	}

	if(fIdx > mid)
	{
		for(i=rIdx; i<=right; i++, sIdx++)
			sortArr[sIdx] = arr[i];
	}
	else
	{
		for(i=fIdx; i<=mid; i++, sIdx++)
			sortArr[sIdx] = arr[i];
	}

	for(i=left; i<=right; i++)
		arr[i] = sortArr[i];

	free(sortArr);
}

void MergeSort(int arr[], int left, int right)
{
	int mid;

	if(left < right)
	{
		// 중간 지점을 계산한다.
		mid = (left+right) / 2;

		// 둘로 나눠서 각각을 정렬한다.
		MergeSort(arr, left, mid);
		MergeSort(arr, mid+1, right);

		// 정렬된 두 배열을 병합한다.
		MergeTwoArea(arr, left, mid, right);
	}
}
{% endcodeblock %}

참고 : 윤성우의 열혈 자료구조
