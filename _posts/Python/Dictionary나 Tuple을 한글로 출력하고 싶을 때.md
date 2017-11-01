[원본 블로그](http://egloos.zum.com/mcchae/v/11076302)
[공식 reference](https://docs.python.org/2/library/pprint.html)

## 문제
```python
d={
	'_id':'12345',
	'kind':'abc',
	'name':'가나다'
}
```
이걸 출력해보면

```python
pprint.pprint(d)
print pprint.pformat(d)
```
> 이게

```python
{'_id': '12345', 'kind': 'abc', 'name': '\xea\xb0\x80\xeb\x82\x98\xeb\x8b\xa4'}
```
요렇게 나옴

## 원인

dict 를 serialize 시키면서 문자를 출력시킬 때 인코딩 무시한 출력 때문.

## 해결방법
```python
import pprint
class MyPrettyPrinter(pprint.PrettyPrinter):
	def format(self, _object, context, maxlevels, level):
		if isinstance(_object, unicode):
			return "'%s'" % _object.encode('utf8'), True, False
		elif isinstance(_object, str):
			_object = unicode(_object,'utf8')
			return "'%s'" % _object.encode('utf8'), True, False
		return pprint.PrettyPrinter.format(self, _object, context, maxlevels, level)
```
위와 같이 pprint의 출력 클래스를 상속받아
unicode 인 경우에는 UTF-8로 변환시킨 문자열을 리턴하고,
일반 UTF-8 인경우에는 애초 UTF-8로 되어 있던 문자도 다시 UTF-8로 인코딩시킨
유니코드를 다시 UTF-8로 반환한 결과를 리턴

## 사용법
```python
MyPrettyPrinter().pprint(d)
print MyPrettyPrinter().pformat(d)
```
>이러면

```python
{'_id': '12345',' kind': 'abc', 'name': '가나다'}
```
일케 잘나옴

## 레퍼런스를 보자...!!

PrettyPrinter의 method를 보면

### pformat(object)

object의 formatted representation을 리턴한다.
물론 string 객체지!

### format(object, context, maxlevels, level)
다음 세가지 결과를 return 한다.
 * object의 formatted version를 string으로 제공.
 * 결과가 readable한지 flag
 * recursion이 있는지 flag
파라미터는 다음과 같다.
 * 보여줄 object
 * 현재 보여줄 context의 일부분인 object들의 id()를 key로 가지고 있는 dictionary 
 * 나머지는 뭐... 당연한거니 패스