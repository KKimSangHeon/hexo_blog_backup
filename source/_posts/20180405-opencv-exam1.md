---
title: OpenCV 설치
date: 2018-04-05 08:23:32
categories:
- Etc
---
http://webnautes.tistory.com/1030
참고로 나는 마지막 pkg_config 관련해서 vi ~/.bash_profile을 입력후
export PKG_CONFIG_PATH="$LIB_DIR/opencv/lib64/pkgconfig" 을 입력했다

컴파일 방법
g++ -o facedetect facedetect.cpp $(pkg-config --libs --cflags opencv)  
g++ -o facedetect facedetect.cpp 'pkg-config --libs --cflags opencv'

참고할만한 예제
http://hanmin-dev.tistory.com/13 (이미지의 픽셀 RGB 구하는법 알려줌 Makefile 구성방법 알려줌)
http://eehoeskrap.tistory.com/34?category=537364  ( 픽셀 소금뿌리기)
