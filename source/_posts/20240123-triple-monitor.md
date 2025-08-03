---
title: 다중모니터 사용시 모니터를 bat파일로 제어
date: 2024-01-23 23:13:33
tags:
---


트리플 모니터를 사용 중 하나의 모니터는 필요에 따라 편하게 관리하고 싶었다.
dual - triple 로 클릭만으로 전환할 수 있게 적용


1.MultiMonitorTool 설치
https://www.nirsoft.net/utils/multi_monitor_tool.html

2.MultiMonitorTool 에서 File -> Save Monitor Configuration을 눌러 파일 저장 (triple.cfg)

3.아래 bat 파일 생성

{% codeblock lang:c triple.bat  %}
 C:\Users\shkim\Desktop\utility\multimonitortool\MultiMonitorTool.exe /LoadConfig "C:\Users\shkim\Desktop\utility\multimonitortool\triple.cfg" /enable 3
{% endcodeblock %}


{% codeblock lang:c dual.bat  %}
C:\Users\shkim\Desktop\utility\multimonitortool\MultiMonitorTool.exe /disable 3

{% endcodeblock %}



모니터 번호는 MultiMonitorTool 에서 확인할 수 있다.
