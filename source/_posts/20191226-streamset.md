---
title: Centos에서 Streamset 설치하기
date: 2019-12-26 21:11:21
categories:
- CS
- 오픈소스,기술
tags:
- Streamset
thumbnail: /images/streamsets.png
---

# StreamSet 설치


스트림셋
오픈소스 데이터 콜렉터 : ETL 도구라함 (추출, 변환, 적재)
동일기종 또는 타기종으로 부터 추출하고, 이를 변환하고 적재하는 과정을 의미
NIFI / KNIME도 유명하다

```
# wget https://archives.streamsets.com/datacollector/3.11.0/tarball/streamsets-datacollector-all-3.11.0.tgz

# tar xvf {datacollector}.tar

# cd streamsets-datacollector-all-3.11.0

// 설정
# cp ./etc /tc/sdc -r

# cd libexec // 환경변수 세팅하는 디렉

# vi sdc-env.sh  // 아래 네개항목 주석 해제
export SDC_DATA=/var/lib/sdc
export SDC_LOG=/var/log/sdc
export SDC_CONF=/etc/sdc
export SDC_RESOURCES=/var/lib/sdc-resources

# cd ..
# cd bin

# ulimit -n 32768  // 최대 열 수 있는 갯수를 늘려줌

# ./streamsets dc

# firewall-cmd --zone=public --add-port=18630/tcp --permanent
# service sshd start

xxx:18630 접속

admin/ admin
# cd ..

# mkdir data
# cd data
# mkdir BasicTutorial
# cd BasicTutorial
# mkdir error
# mkdir origin		// 원천데이터
# mkdir destination	//최종적으로 변환된 것이 저장된 공간

```

만약 경로관련 문제로 실행이 안될경우
스트림셋 실행하기 전에 아래를 입력하고 `./streamsets dc` 해볼것
```
export SDC_CONF=/etc/sdc
export SDC_DATA=/var/lib/sdc
export SDC_LOG=/var/log/sdc
export SDC_HOME=/opt/streamsets-datacollector
```
