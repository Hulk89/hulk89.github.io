* pyenv
    * 로컬에 다양한 파이썬 버전을 설치하고 사용할 수 있도록 한다. pyenv를 사용함으로써 파이썬 버전에 대한 의존성을 해결할 수 있다.
* virtualenv
    * 로컬에 다양한 파이썬 환경을 구축하고 사용할 수 있도록 한다.
* autoenv 
    * 특정 프로젝트 폴더로 들어가면 자동으로 개발 환경을 설정해주는 스크립트

## pyenv 깔기
```bash
brew install pyenv
brew upgrade pyenv --HEAD
brew install openssl readline xz
```
### pyenv에 지원하는 python list 살펴보기
```bash
pyenv versions
```
### 새로운 python 깔기
```bash
pyenv install 3.5.1
```
> 만약 zlib 관련 에러가 난다면 `xcode-select --install`로 개발자 툴을 업데이트하자

## pyenv-virtualenv 깔기

```bash
brew install pyenv-virtualenv  
```
이후 bash_profile에 다음 줄을 추가한다.
```bash
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```
## pyenv-virtualenv 사용하기
`pyenv virtualenv 3.5.1 pyView-3.5.1`하면 3.5.1버전으로 pyView-3.5.1 가상환경을 만든다.

이제 `pyenv shell pyView-3.5.1`로 가상환경을 사용가능하다.

## autoenv를 자동으로 실행
`brew install autoenv`

bash_profile에 다음을 추가하면 폴더의 .env가 존재할 경우 실행시켜준다.
```bash
source /usr/local/opt/autoenv/activate.sh
```
make .env 
```bash
#~/Git/math-logic-server/.env

pyenv shell mathServer-3.5.1
```
요렇게 만들면 폴더에 들어가면서 자동으로 python을 바꿔준다.
물론 이는 내 클라이언트에만 해당하니 git에 올리지 않는 것이 좋다.