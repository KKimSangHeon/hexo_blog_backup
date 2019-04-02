---
title: 기수정렬 (RadixSort)
date: 2017-06-30 14:36:34
categories:
- Algorithm
- Sort
tags:
- Data Structure
- Sort
- Radix Sort
thumbnail: /images/datastructure.jpg
---
### 기수정렬
버켓에 데이터를 작은 수 부터 넣고 빼내어 정렬하는 방식이다.
기수정렬의 시간복잡도는 O(ln)이며 l은 버켓의 크기를 의미한다.

red, why, few 정렬가능    - 버킷 3개 필요
10,161,18,1 가능 - 버킷 3개 필요
proffesor , red , why 불가능
125 , -101, 167, -505 불가능

##참고사항

1의자리 수 구하기-num/1%10
10의자리 수 구하기 -num/10%10
100의 자리 수 구하기 -num/100%10

{% codeblock lang:c InsertionSort.c  %}
void RadixSort(int arr[], int num, int maxLen)   // maxLen은 가장 긴 데이터의 길이
{
	Queue buckets[BUCKET_NUM];
	int bi;
	int pos;
	int di;
	int divfac = 1;
	int radix;

	// 총 10개의 버킷 초기화
	for(bi=0; bi<BUCKET_NUM; bi++)
		QueueInit(&buckets[bi]);

	// 가장 긴 데이터의 길이만큼 반복
	for(pos=0; pos<maxLen; pos++)
	{
		// 정렬 대상의 수만큼 반복
		for(di=0; di<num; di++)
		{
			// N번째 자리의 숫자 추출
			radix = (arr[di] / divfac) % 10;

			// 추출한 숫자를 근거로 데이터 버킷에 저장
			Enqueue(&buckets[radix], arr[di]);
		}

		// 버킷 수만큼 반복
		for(bi=0, di=0; bi<BUCKET_NUM; bi++)
		{
			// 버킷에 저장된 것 순서대로 다 꺼내서 다시 arr에 저장
			while(!QIsEmpty(&buckets[bi]))
				arr[di++] = Dequeue(&buckets[bi]);
		}

		// N번째 자리의 숫자 추출을 위한 피제수의 증가
		divfac *= 10;
	}
}

{% endcodeblock %}

참고 : 윤성우의 열혈 자료구조
