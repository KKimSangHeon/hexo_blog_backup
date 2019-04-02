---
title: 거품정렬 (BubbleSort)
date: 2017-06-29 14:36:52
categories:
- Algorithm
- Sort
tags:
- Data Structure
- Sort
- Bubble Sort
thumbnail: /images/datastructure.jpg
---
### 거품정렬
거품정렬의 코드는 다음과 같다.
시간복잡도는 O(n^2) 이다.


{% codeblock lang:c BubbleSort.c  %}
for(i=0;i<n-1;i++)
{
	for(j=0;j<(n-i)-1;j++)
	{
		if(arr[j]>arr[j+1])
		{
			swap;
		}
	}
}
{% endcodeblock %}

참고 : 윤성우의 열혈 자료구조
