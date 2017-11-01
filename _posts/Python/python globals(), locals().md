## globals(), locals()

 * `globals()`는 module의 namespace에 대한 dictionary를 리턴한다.
 * `locals()`는 현재의 namespace에 대한 dictionary를 리턴한다.
   * 함수에서는 `locals()`를 **부른 시점**의 dictionary를 return.
   * 함수 바깥에서는 항상 최신상태를 유지하는 dictionary를 return.