# BindingResult #

> 해당 내용은 김영한님의 인프런 강의 Spring MVC2편을 바탕으로 합니다.

사용자가 넘기는 값에 대애서 유효값인지 검증을 거쳐야한다.    
예를 들어 필수 값을 적었는지, int형 필드에 맞는 값이 들어왔는지에 대한 체크를 해야한다. 스크립트로 먼저 체크할 수 있지만 스크립트 코드는 사용자가 언제든지 바꿀수 있기 때문에
코드가 취약한 편이다. 이러한 경우를 위해 서버측에서도 확인을 해야하는데   
컨트롤러단에서 이러한 검증을 **BindResult**를 통해서 검증할 수 있다.       
또한 필드에 대해 사용자가 잘못된 값을 넘기게 될 경우 필드에 맞지 않는 잘못된 바인딩을 통해 프로그램 자체에 오류가 날 수 있는데   
**BindResult**을 통해 이후를 진행하여 **흐름을 진행할 수 있도록 하는 장점**도 가지고 있다.   

 ----------------------------------------- 
 
### BindResult는 스프링이 제공하는 검증 오류를 보관하는 객체이다.    
### 사용자가 넘긴 값이 ModelAttribute 객체에 binding될 때 발생한 오류 정보를 받고 에러 데이터를 담을 수 있는 기능을 제공한다. ###    
**사용자가 필드 타입에 맞지 않는 값을 넘긴다면 VO객체에 담기는 시점에 에러가 발생해서 컨트롤러가 호출되지 못하고 화면에 에러가 나버린다. 하지만 `BindingResult` 를 사용하면 해당 에러에 대한 내용을 `BindingResult`에 담고 컨트롤러를 정상 호출한다.**  
  

* **순서가 중요!!** ModelAttribute에 사용된 객체에 대한 값을 바인딩하기 때문에 값을 담고 있는 ModelAttribute 객체 다음 순서에 파라미터를 써줘야한다. 떨어뜨려서 써도 안된다.   ()
* Model에 따로 넣지 않아도 자동으로 view에 같이 넘어간다.
* 스프링에서 제공하는 인터페이스이다.   

### FieldError 생성자
```
* public FieldError(String objectName, String field, String defaultMessage) {}    
* public FieldError(String objectName, String field     //사용자가 잘못 입력한 값을 다시 사용자에게 전달 할 때
                    , @Nullable Object rejectedValue, boolean bindingFailure, @Nullable String[] codes, @NullableObject[] arguments
                    , @Nullable String defaultMessage)
```
* 필드에 오류가 있으면 `FieldError`객체를 생성해서 `bindingResult`에 담아두면 된다.
* `objectName` : @ModelAttribute 이름
* `field` : 류가 발생한 필드 이름
* `rejectedValue` : 사용자가 입력한 값(거절된 값)
* `bindingFailure` : 타입 오류 같은 바인딩 실패인지, 검증 실패인지 구분 값
* `codes` : 메시지 코드
* `arguments` : 메시지에서 사용하는 인자
* `defaultMessage` : 전달할 오류 기본 메시지   
   
사용자가 잘못된 값을 입력하면 필드에 값을 담을 수 없어 사용자에게 잘못된 값을 다시 전달해서 보여줄 수가 없는데, 두번째 생성자를 사용하면 잘못 적힌 필드값을 담는 파라미터(`rejectedValue`)가 따로 있기 때문에 사용자에게 잘못 적은 값을 다시 보여줄 수 있다.

### ObjectError 생성자
```
* public ObjectError(String objectName, String defaultMessage) {}
* public ObjectError(String objectName,
                    , @Nullable String[] codes, @NullableObject[] arguments 
                    , @Nullable String defaultMessage)
```
* 특정 필드에 한정되지 않은 오류가 있으면 `ObjectError` 객체를 생성해서 `bindingResult` 에 담아두면 된다.
* `objectName` : @ModelAttribute 의 이름
* `codes` : 메시지 코드
* `arguments` : 메시지에서 사용하는 인자
* `defaultMessage` : 오류 기본 메시지  


> 참고
> 
>  `codes` , `arguments` 는 오류 발생시 오류 코드로 메시지를 찾기 위해 사용된다.
  
### 대략적인 사용 함수
* 에러가 담겼는지 확인 -> **boolean hasErrors()**
* 글로벌 에러가 담겼는지 확인 -> **boolean hasGlobalErrors()**
* bindingResult에 에러 추가 -> **void addError(ObjectError error)**
  (FieldError는 ObjectError의 자식)
  
  
  ```
  @PostMapping("/add")
  public String addItem1(@ModelAttribute Item item, BindingResult bindingResult) {
      
      if (!StringUtils.hasText(item.getItemName())){
          bindingResult.addError(new FieldError("item", "itemName", "상품 이름은 필수입니다."));
      }
      ...
   }
    ```
  
