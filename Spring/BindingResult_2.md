### BindingResult_2

> 해당 내용은 김영한님의 인프런 강의 Spring MVC2편을 바탕으로 합니다.


[BindingResult](https://github.com/MJeong00/TIL/blob/main/Spring/BindResult.md)에 적은 내용보다 더 간결하게 메소드를 사용할 수 있다.   
사실 이전에 사용했던 
```
* public FieldError(String objectName, String field, String defaultMessage) {}    
```
처럼 개발자가 `objectName`을 직접 지정해주지 않아도 이미 `bindingResult`는 `objectName`에 어떤 객체를 대상으로 하는지 알고있다. 
그래서 해당 코드를 생략할 수 있다. 
![image](https://user-images.githubusercontent.com/108853290/223056565-66fdd1db-e19b-4d3b-b89d-e7359a261188.png)   
![image](https://user-images.githubusercontent.com/108853290/223056629-4c970c68-10ba-468c-a437-b95cc3b769e3.png)   
> .getObjectName()으로 꺼내본 `objectName`

</br></br>  
      
      


### * rejectValue()
```
void rejectValue(@Nullable String field, String errorCode, @Nullable Object[] errorArgs, @Nullable String defaultMessage);
```
* `field` : 오류 필드명
* `errorCode` : 오류 코드
* `errorArgs` : 오류 메시지에서 {0} 을 치환하기 위한 값
* `defaultMessage` : 오류 메시지를 찾을 수 없을 때 사용하는 기본 메시지   


### * rejectValue()
 ```
void reject(String errorCode, @Nullable Object[] errorArgs, @Nullable String defaultMessage)
```

## 비교

### 이전 코드
```
bindingResult.addError(new FieldError("item", "price", item.getPrice(), false, new String[]{"range.item.price"}, new Object[]{1000, 1000000}, null));   
bindingResult.addError(new ObjectError("item", new String[]{"totalPriceMin"}, new Object[]{10000, resultPrice}, null));
```

### rejectValue() 사용
```
bindingResult.rejectValue("price", "range", new Object[]{1000, 1000000}, null);  
bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);

```   
</br>
파라미터를 줄였을 뿐아니라 객체 사용 코드까지 간결해졌다.
