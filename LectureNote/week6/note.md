# 주제
TDD
## 발표자

- 이소윤

## 참여자

- 김라희, 김주완, 안소현, 이상아

## 내용

### TDD 정의
> TDD(Test Driven Development)란 테스트 주도적을 프로그램을 개발하는 방법! 즉, 테스트 코드르 먼저 작성하고 그 테스트 코드 통과하느 실제 코드를 나중에 만드는 개발방법이다. 

### TDD의 목표
Clean code that works!!!! 작동하는 깨끗한 코드!🔥🔥🔥
### TDD의 흐름 
1. 무엇을 테스트할 것인가 생각한다.
2. 실패하는 테스트를 작성한다.
3. 테스트를 통과하는 코드를 작성한다.(실제코드)
4. 코드를 리팩토링한다.(테스트 코드 또한 리팩토링한다)
- 리팩토링 소스 코드의 기능을 유지한 채로 소스코드의 디자인을 개선해 나가는 방법이다.
5. 구헌해야 할 것이 있을 때까지 위의 작업을 반복한다.

### TDD로 개발해보자!
두날짜(YYYYMMDD)의 일 수 차이 구하는 프로그램을 TDD로 개발해보며 완성해보자. 
``` 20070515 sub 20070501 = 14
20070501 sub 20070515 = 14 
20070301 sub 20070501 = 31 + 30
```
결과 = 절대값(첫 번째 날짜의 총 일수 - 두 번째 날짜의 총 일수)<br>
날짜의 0년부터 그 날짜의 지나온 총 일수를 구한다면 문제가 쉽게 해결될 듯 하다! 🤔<br>
하지만 보기에는 쉬워보이지만 걸림돌은? 윤달처리! (2월의 28일 vs 2월의 29일)

즉, 20070515라는 날짜의 총 일수를 구하고 싶다면 
```
2007년 5월 15일의 총 일수 =
0년부터 2006년까지의 총 일수 +
2007년 1월부터 4월까지의 총 일 수 + 
15
```
### TODO 리스트
TODO리스트를 미리 작성해 놓아 프로그램을 만들면서 잊지 않게!
우리는 다음과 같이 테스트해야 할 목록들을 가지게 되었고 테스트 코드를 작성할 준비가 되었다! 뚜둔🤓
* 두 날짜(YYYYMMDD)의 차이 일자를 구한다. 
* 특정일자의 총 일수를 구한다.
  * >전년도까지의 총 일수를 구한다.
  * 전월까지의 총 일수를 구한다.
  * 해당일자까지의 총 일수를 구한다.
(">"표시는 앞으로 진행 하려고 하는 테스트를 뜻한다.)

### 첫번째 테스트 코드 
```
public void testGetYearDay(){
    assertEquals(0, SubDate.getYearDay(1));
    assertEquals(365, SubDate.getYearDay(2));
}
```
* 0년이란 것은 존재하지 않기 때문에 1년까지의 총 일 수는 0이 되어야 한다. (최초 일자를 1년 1월 1일이라고 나름대로 설정한 것이다.)
* 2년까지의 총 일 수는 1년 1월 1일부터 2년 1월 1일까지이므로 365일이 될 것이다.
* 위 테스트 코드를 작성하는 순간 SubDate라는 클래스는 아직 가상의 클래스! SubDate라는 클래스는 테스트코드에 의해서 만들어진 가상의 클래스이다.
즉, 테스트 코드에 의해서 SubDate라는 클래스가 디자인되고 있는 것이다. 위처럼 테스트 코드를 작성하고 저장하면 컴파일이 되지 않는다.
컴파일이 되기 위한 가장 빠른 방법?! 👇 컴파일 되는 실제코드 만들기!
```
public class SubDate {
    public static int getYearDay(int year){
        return 1;
    }
}
```
컴파일은 되지만 테스트 코드 실행 시 실패하는 것을 볼 수 있다. 테스트가 실패한다고 낙담할 필요는 없다.
테스트를 실패하게 만드는 것은 TDD의 중요한 과정 중 하나이다.
TDD는 "테스트가 실패할 경우에만 실제코드를 작성 한다"는 간단한 법칙을 따른다.
#### 가장 빨리 테스트를 통과하라~~~

```
public class SubDate {
    public static int getYearDay(int year){
        if(year==1) return 0;
        else return 365;
    }
}
```
1이면 0을 리턴하고 아닌 경우 365를 리턴하다니, 정말 테스트를 통과하기에 급급한 황당한 메써드 아닌가?
그렇다, 테스트를 통과하기 위한 급급한 방법을 찾는 것! 그것 또한 TDD의 당연한 과정이다. 켄트벡(Kent Beck)은 테스트를 재빠르게 통과하기 위해서는 어떤 죄악(?)을 저질러도 상관없다고 말한다. 그 이유는 TDD싸이클에 의해 결국에는 당연한 코드 로 변경되기 때문이다.

#### 윤년체크?!
```
public void testGetYearDay(){
    //assertEquals(0, SubDate.getYearDay(1));
    //assertEquals(365, SubDate.getYearDay(2));
    assertEquals(365+365+365+366, SubDate.getYearDay(5));
}
```
맨 마지막 코드를 통과하기 위해서는 1년부터 4년까지 중 윤년이 있ㄴ느지 조사하는 로직이 필요하다.
먼저 윤년인지 아닌지를 검사하는 테스트 코드를 작성해보자.
```
public void testLeapYear(){
    assertTrue(SubDate.isLearYear(0));
    assertFalse(SubDate.isLearYear(1));
    assertTrue(SubDate.isLearYear(4));
    //4로 나누어 떨어지는 년도가 윤년이라고 알고 있어서 위와 같은 테스트 작성
}
```
##### 실제코드
```
public static boolean isLeapYear(int year){
    if(year == 0) return true;
    if(year == 1) return false;
    if(year == 4) return true;
    return false;
}
```
실제 코드 리팩토링
```
public static boolean isLeapYear(int year){
    if(year % 4 == 0) return true;
    return false;
}
```
* 서력 기원 연수가 4로 나누어 떨어지는 해는 우선 윤년으로 하고, 
* 그 중에서 100으로 나누어 떨어지는 해는 평년으로 하며, 
* 다만 400으로 나누어 떨어지는 해는 다시 윤년이다.
* ⭐️우선순위 : 400, 100, 4⭐

아래의 테스트코드를 실행하면 에러가 발생한다. 왜? 
700은 4로 나누어 떨어지지만, 100으로도 나누어 떨어지기 때문에 평년이다.
하지만 실제코드가 리팩토링 되지 않아서 윤년이라고 결과가 나온다. 수정해야돼!
```
public void testLeapYear(){
    assertTrue(SubDate.isLearYear(0));
    assertFalse(SubDate.isLearYear(1));
    assertTrue(SubDate.isLearYear(4));
    assertTrue(SubDate.isLearYear(1200));
    assertFalse(SubDate.isLearYear(700)); // error! 
}
```
실제코드 
```
public static boolean isLeapYear(int year){
    if(year % 4 == 0) return true;
    return false;
}
```
수정을 하게 되면.. 
```
public static boolean isLeapYear(int year){
    if(year % 100 == 0) return false;
    if(year % 4 == 0) return true;
    return false;
}
```
 테스트 코드를 실행하면 1200에서 오류가 발생한다.
 4와 100으로 나누어떨어져 fasle값을 리턴한다. 하지만 400으로도 나누어 떨어지기 때문에 실제코드를 수정해주어야 한다.
 ```
 assertTrue(SubDate.isLearYear(1200));
 ```
최종 수정을 하게 되면.. 
 ```
public static boolean isLeapYear(int year){
    if(year % 400 == 0) return true;
    if(year % 100 == 0) return false;
    if(year % 4 == 0) return true;
    return false;
}
```
#### 즉, 실제코드는 TDD를 거쳐 완성된다. 
```
public class SubDate {
    public static int getYearDay(int year){
        if(year==1) return 0;
        else return 365;
    }
}
```
에서 
```
public class SubDate {
    public static int getYearDay(int year){
        int result = 0;
        for(int i=0;i<year;i++){
            if(isLeapYear(i)) result += 366;
            else result +=365;
        }
        return result;
    }
}
```
 ```
public static boolean isLeapYear(int year){
    if(year % 400 == 0) return true;
    if(year % 100 == 0) return false;
    if(year % 4 == 0) return true;
    return false;
}
```
이렇게.

### TODO리스트 체크체크
* 두 날짜(YYYYMMDD)의 차이 일자를 구한다. 
* 특정일자의 총 일수를 구한다.
  * 전년도까지의 총 일수를 구한다.
  * > 전월까지의 총 일수를 구한다.
  * 해당일자까지의 총 일수를 구한다.
(">"표시는 앞으로 진행 하려고 하는 테스트를 뜻한다.)

### 전 달까지의 총 일 수 구하기!

#### 테스트코드
```
public void testGetMonthDay(){
    assertEquals(0, SubDate, getMonthDay(1));
    assertEquals(31, SubDate.getMonthDay(2));
}
```
#### 실제코드 
```
public static int getMonthDay(int month){
    if(month == 1) return 0;
    else return 31;
}
```
갑자기 든 의문! 구하려는 일수에 포함되는 1월이 19일까지 있는지, 28일까지 있는지 어떻게 알지? 
간단하게 윤년인 경우 true, 윤년이 아닌 경우는 false를 파라미터로 보내주자.

#### 테스트코드 
```
public void testGetMonthDay(){
    assertEquals(0, SubDate, getMonthDay(1, true));
    assertEquals(31, SubDate.getMonthDay(2,flase));
}
```
#### 실제코드 
1. 파라미터 추가 
```
public static int getMonthDay(int month, boolean isLeap){
    if(month == 1) return 0;
    else return 31;
}
```
2. 윤달 처리
```
static final int[] = monthDays = {
    31,28,31,30,31,30,31,31,30,31,30,31};
public static int getMonthDay(int month, boolean isLeap){
    int result = 0;
    for(int i=1;i<month ;i++){
        result +=monthDays[i-1];
    }
    if(isLeap && month>2) result +=1;
    return result;
}
```
### TODO리스트 체크체크
* 두 날짜(YYYYMMDD)의 차이 일자를 구한다. 
* 특정일자의 총 일수를 구한다.
  * 전년도까지의 총 일수를 구한다.
  * 전월까지의 총 일수를 구한다.
  * > 해당일자까지의 총 일수를 구한다.
(">"표시는 앞으로 진행 하려고 하는 테스트를 뜻한다.)
### 특정 일자의 총 일수 구하기!
#### 테스트 코드
```
public void testGetTotalDay(){
    assertEquals(1, SubDate.getYearDay("00010101"));
    assertEquals(366, SubDate.getYearDay("00020101"));
}
```
#### 실제코드
public static int getTotalDay(String date){
    int year = Integer.parseInt(date.substring(0,4));
    int month = Integer.parseInt(date.substring(4,6));
    int day = Integer.parseInt(date.substring(6,8));
    return getYearDay(year)
    + getMonthDay(month, isLeapYear(year))
    + day;
}

#### 최종 테스트 코드
```
public void testSubDate({
    assertEquals(1, subDate.sub("20191231","20200101"));
    assertEquals(31+28+30+31+1, subDate.sub("20190701","20190515"));
})
```
#### 최종 실제 코드
```
public static int suv(String date1, String date2){
    return Math.abs(getTotalDay(date1) - getTotalDay(date2));
}
```


