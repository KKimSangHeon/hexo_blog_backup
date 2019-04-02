---
title: 삽입정렬 (InsertionSort)
date: 2017-06-30 14:35:28
categories:
- Algorithm
- Sort
tags:
- Data Structure
- Sort
- Insertion Sort
thumbnail: /images/datastructure.jpg
---
### 삽입정렬
1~n까지 정렬해 나감 1~2 정렬 12묶음 3정렬 13묶음 4정렬
첫 번째 데이터는 정렬이 되어있다고 봄. 그래서 i=0이아닌 1부터 시작.시간복잡도는 O(n^2) 이다.

{% codeblock lang:c InsertionSort.c  %}
for(i=1;i<n;i++)
{
	insData=arr[i];	//정렬대상을 insData에 저장
	for(j=i-1;i>=0;j--)
	{
		if(arr[j]>insData)
			arr[j+1]=arr[j];
		else
			break;
	}
	arr[j+1]=insData;
}
{% endcodeblock %}

참고 : 윤성우의 열혈 자료구조
