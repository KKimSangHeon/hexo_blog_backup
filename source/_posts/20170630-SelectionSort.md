---
title: 선택정렬 (SelectionSort)
date: 2017-06-30 13:59:38
categories:
- Algorithm
- Sort
tags:
- Data Structure
- Sort
- Selection Sort
thumbnail: /images/datastructure.jpg
---
### 선택정렬
오름차순일 경우 작은걸 맨앞에 넣고 그 다음 작은걸 두 번째 넣고 ..
이 과정을 계속 반복한다. 시간복잡도는 O(n^2) 이다.


{% codeblock lang:c SelectionSort.c  %}
for(i=0;i<n-1;i++)
{
	maxIdx=i;
	for(j=i+1;j<n;j++)	//최소값 탐색
	{
		if(arr[i]<arr[maxIdx])
			maxIdx=j;
	}
	swap;
}
{% endcodeblock %}

참고 : 윤성우의 열혈 자료구조
