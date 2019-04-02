---
title: BinarySearch
date: 2017-06-30 19:38:20
categories:
- Algorithm
- Search
tags:
- Data Structure
- Search
- BinarySearch
thumbnail: /images/datastructure.jpg
---
### 이진탐색
데이터의 정렬이 선행되어야 한다. 시간복잡도는 O(log2n) 이다.
{% codeblock lang:c BinarySearch.c 재귀적인 방법  %}
int ISearch(int ar[], int first, int last, int target)
{
	int mid;

	if(first > last)
		return -1;    // -1의 반환은 탐색의 실패를 의미

	mid=(first+last) / 2 ;

	if(ar[mid] == target)
		return mid;    // 탐색된 타겟의 인덱스 값 반환
	else if(target < ar[mid])
		return ISearch(ar, first, mid-1, target);
	else
		return ISearch(ar, mid+1, last, target);
}
{% endcodeblock %}

{% codeblock lang:c BinarySearch.c 반복문 이용  %}
BinarySearch(int DataSet[], int Size, int target){
	int Left,Right,Mid;

	Left=0;
	Right=Size-1;

	while(Left<=Right){
		Mid=(Left+Rigth) / 2 ;

		if( Target==DataSet[Mid])
			return DataSet[Mid];
		else if(Target>DataSet[Mid])
			Left=Mid+1;
		else
			Right=Mid-1;
	}
	return NULL;
}
{% endcodeblock %}

이진탐색의 경우 비교대상이 되는 mid값을 단순히 (first+last)/2로 설정한다.
그러나 보간 탐색의 경우 mid 값 설정방식이 다르며 이진탐색보다 우수한 성능을 보인다.

### 보간탐색
탐색대상이 앞쪽에 위치 할 경우 앞쪽에서 탐색을 시작하고 뒤쪽에 위치할 경우 뒤쪽에서 탐색을 시작한다.
이진탐색보다 우수하다.
{% codeblock lang:c ISearch.c  %}
int ISearch(int ar[], int first, int last, int target)
{
	int mid;

	if(ar[first]>target || ar[last]<target)
		return -1;
	// 이진 탐색과의 차이점을 반영한 문장
	mid = ((double)(target-ar[first]) / (ar[last]-ar[first]) *(last-first)) + first;

	if(ar[mid] == target)
		return mid;    // 탐색된 타겟의 인덱스 값 반환
	else if(target < ar[mid])
		return ISearch(ar, first, mid-1, target);
	else
		return ISearch(ar, mid+1, last, target);
}
{% endcodeblock %}

#### 보간탐색의 mid값 계산 방법
{% asset_img BinarySearch1.bmp 보간탐색의 mid값 계산 방법 [그림1] %}
그림[1]에서 arr[s]는 찾는값을 의미함.

참고 : 윤성우의 열혈 자료구조
