# MessageCodesResolver

> 해당 내용

```
void rejectValue(@Nullable String field, String errorCode, @Nullable Object[] errorArgs, @Nullable String defaultMessage);
```
`rejectValue()`의 파라미터 중 `errorCode`는 `messages.properties`의 오류코드를 적는 파라미터이다.   
그런데 code.object.field(range.item.price)를 모두 적어야했던 FieldError 생성자와는 다르게 code만 적는것만으로도 여러 메세지를 적용시킬 수 있다.   
예를들어 
```
bindingResult.rejectValue("price", "range", new Object[]{1000, 1000000}, null);
```
처럼 `range` 를 적는것 만으로도 `range.item.price`, `range.price`, `range.java.lang.String`, `range`의 메세지 코드를 모두 읽을 수 있다.   
**이렇게 하나의 오류code로 여러 메세지 코드들을 생성할 수 있게됨으로써    
해당 코드를 사용하고 있는 화면단의 메세지를 `messages.properties`만 수정해서 일괄적으로 변경할 수 있다는 장점을 가지게된다.**   
</br>

### 이렇게 하나의 에러코드가 여러개의 메세지코드를 인식할 수 있었던 이유는 바로 `MessageCodesResolver` 덕분이다. ###   

![image](https://user-images.githubusercontent.com/108853290/223119139-873440d3-2f79-4099-babc-52e4641e9e21.png)      
**MessageCodesResolver의 `resolveMessageCodes`를 사용하면 오류코드와 Object, field, field type 의 조합으로 만들어진 여러개의 메세지 코드들을 배열에 담는다.**   
**우선순위도 존재한다. 우선순위가 제일 높은것을 보여주며 구체적일수록 우선순위가 높다.**   

### 객체 오류 우선순위 ###
```
1. code + "." + object name
2. code
```

### 필드 오류  우선순위 ###
```
1. code + "." + object name + field
2. code + "." + field
3. code + "." + field type
4. code
```
</br>

## 정리 ##
1. `rejectValue()`, `reject()`를 호출하면    
2. 내부에서 이 `MessageCodesResolver`를 사용하고 여기에서 메세지 코드들을 생성한다.
3. 그 다음 `new FieldError()`를 생성하면서 오류코드를 보관한다.   
> 참고 :  FieldError(), ObjectError() 생성자를 보면 오류코드들을 배열타입으로 여러 개 저장할 수 있다.
