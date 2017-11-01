1. `string`에서 `pattern`을 **모두** 찾아서 바꿔주는 함수
`re.sub(<pattern>,<replaceStr>,<string>)`

```python
re.sub(r"[ ]+", "+","we are  a   champion")
=>'we+are+a+champion'
```