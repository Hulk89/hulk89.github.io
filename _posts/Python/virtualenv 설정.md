# Virtualenv 설치
`sudo pip install virtualenv`

# Virtualenv 사용법

## 가상환경 만들기
`virtualenv <folder name>`
ex > `virtualenv numpy_env`

## 가상환경 활성화
`cd <folder name>`
`. bin/activate`
가상환경 활성화 되면 prompt 앞에 (가상환경폴더이름) 식으로  (numpy_test) 가 보인다.
이제부터  여기서  pip 로 해당 가상환경 전용 파이썬 모듈 설치하고, 실행하고 작업한다.

# 모듈들 설치

## numpy 설치
`pip install numpy`
> raspberry pi에서는 python.h가 없어서 안된다고 한다.  
* `sudo apt-get upgrade` 
* `sudo apt-get update`
* `sudo apt-get install python-dev`
를 하고 다시 설치하면 된다.

## scipy 설치
`pip install scipy`
> raspberry pi에서는 역시 모듈 좀 추가해줘야한다.
* `sudo apt-get install libblas-dev`
* `sudo apt-get install liblapack-dev`
* `sudo apt-get install libatlas-base-dev`
* `sudo apt-get install gfortran`
* `sudo apt-get install python-setuptools`

## matplotlib 설치
`pip install matplotlib`