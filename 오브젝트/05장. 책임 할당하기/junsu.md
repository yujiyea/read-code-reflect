# 5장 책임 할당하기

```
책임 할당 과정은 일종의 트레이드오프 활동
```

## **책임 주도 설계를 향해**

데이터 중심의 설계에서 책임 중심의 설계로 전환하기위해서 따라야하는 두가지 원칙

- 데이터보다 행동을 먼저 결정하라
- 협력이라는 문맥 안에서 책임을 결정하라

### **데이터보다 행동을 먼저 결정하라**

- 객체에게 중요한 것은 데이터가 아니라 외부에 제공하는 행동이다. (행동이랑 곧 객체의 책임)
- 데이터는 객체가 책임을 수행하는 데 필요한 재료들일뿐 -> 너무 이른 시기에 데이터에 초점을 맞추면 캡슐화에 실패하고, 결합도가 높아진다.
- 우리에게 필요한것은? 객체의 데이터에서 행동으로 무게중심을 옮겨야함. 가장 간단한 해결방법은 객체를 설계하기 위한 질문의 순서를 바꿔보자

> "이 객체가 포함해야 하는 데이터가 무엇인가" → "데이터를 처리하는 데 필요한 오퍼레이션은 무엇인가"
> 
> 
> "이 객체가 수행해야 하는 책임은 무엇인가" -> "이 책임을 수행하는 데 필요한 데이터는 무엇인가"
> 
- 객체에게 어떤 책임을 할당해야할까? -> 협력에서 찾을 수 있다.

### **협력이라는 문맥 안에서 책임을 결정하라**

- 객체에게 할당된 책임의 품질은 협력에 적합한 정도로 결정된다. 책임은 객체의 입장이 아니라 객체가 참여하는 협력에 적합해야 한다.
- 협력을 시작하는 주체는 메시지 전송자이기 때문에 협력에 적합한 책임이란 메시지 수신자가 아니라 메시지 전송자에게 적합한 책임을 의미
- 협력에 적합한 책임을 얻기 위해서는 객체를 결정한 후 메시지를 선택하는 것이 아니라 메시지를 결정한 후 객체를 선택해야함.

> "이 클래스가 필요하다는 점은 알겠는데 이 클래스는 무엇을 해야하지?" (X)
"메시지를 전송해야 하는데 누구에게 전송해야하지?" (O)
> 
- 메시지가 클라이언트의 의도를 표현하고, 객체를 결정하기 전에 객체가 수신할 메시지를 먼저 결정한다는 점을 기억하자.
- 메시지를 먼저 결정하기 때문에 메시지 송신자는 메시지 수신자에 대한 어떠한 가정도 할 수 없다 -> 캡슐화 성공

### **책임 주도 설계**

3장에서 설명한 책임 주도 설계의 흐름 나열

1. 시스템이 사용자에게 제공해야 하는 기능인 시스템 책임을 파악한다.
2. 시스템 책임을 더 작은 책임으로 분할한다.
3. 분할된 책임을 수행할 수 있는 적절한 객체 또는 역할을 찾아 책임을 할당한다.
4. 객체가 책임을 수행하는 도중 다른 객체의 도움이 필요한 경우 이를 책임질 적절한 객체 또는 역할을 찾는다.
5. 해당 객체 또는 역할에게 책임을 할당함으로써 두 객체가 협력하게 한다.

## **책임 할당을 위한 GRASP 패턴**

General Responsibility Assignment Software Pattern(일반적인 책임 할당을 위한 소프트웨어 패턴)

### **도메인 개념에서 출발하기**

![image.png](https://github.com/dlwnstn0524/read-code-reflect/blob/dlwnstn0524/%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8/05%EC%9E%A5.%20%EC%B1%85%EC%9E%84%20%ED%95%A0%EB%8B%B9%ED%95%98%EA%B8%B0/image.png)

단, 올바른 도메인 모델이란 존재하지 않는다

### 정보 전문가에게 책임을 할당하라

예매 시스템에서 해당 상영의 금액을 알고싶다? → 할인 정책을 알고 있는 영화에게 책임을 할당.

영화에서 할인 여부를 알고싶다? → 할인 조건에게 책임을 할당.

### 높은 응집도, 낮은 결합도

할인조건은 영화와 협력하는 것이 유리할까? 상영과 협력하는게 유리할까? → 이미 할인 조건은 영화를 속성으로 결합되어 있음. 영화와 협력하는것이 상영 입장에서 낮은 결합도. 만일 상영에서 할인 조건과 협력한다고 하면 영화에서 할인 조건을 읽어와야 한다. (디미터의 법칙 위배?) 응집도 측면에서도 동일한 맥락.

### Creator 패턴

- B가 A를 포함하거나 참조
- B가 A를 기록
- B가 A를 긴밀하게 사용
- B가 A를 초기화하는데 필요한 데이터를 가지고 있다. (=B가 A의 정보 전문가)

## 코드로 이해하기

```java
public class DiscountCondition {
    private DiscountConditionType type;
    private int sequence;
    private DayOfWeek dayOfWeek;
    private LocalTime  startTime;
    private LocalTime endTime;
    
    public boolean isSatisfiedBy(Screening screening){
        if (type == DiscountConditionType.PERIOD){
            return isSatisfiedByPeriod(screening);
        }
        
        return isSatisfiedBySequence(screening);
    }
    private boolean isSatisfiedByPeriod(Screening screening){
        return screening.getWhenScreened().toLocalTime().isAfter(startTime) &&
                screening.getWhenScreened().toLocalTime().isBefore(endTime);
    }
    private boolean isSatisfiedBySequence(Screening screening){
        return sequnce == screening.getSequence(sequence);
    }
}
```

조건 1 : 새로운 할인 조건 추가

조건 2 : 순번 조건을 판단하는 로직 변경

조건 3 : 기간 조건을 판단하는 로직 변경

서로 다른 이유로 변경되는 두개의 메서드를 가지는 `DiscountCondition` 응집도 낮음

## 책임 주도 설계의 대안

솔직히 책임 주도 설계? 쉽지 않다. ~~괜찮아, 우린 파울러와 함께니까~~

몬스터 메소드 ㄷㄷ

일단 만들고 나서 객체를 자율적으로 만들어주자.

동작하는 소스를 먼저 만들기 → 리팩터링

## 고찰

저자는 오늘도 일단 구현하라는 말을 하신다. 경험해라. 그리고 그게 별로라는 것을 스스로 느껴라. 그러면 그 단계까지 알아서 반영하여 구현하게 될것이다. 인수분해 공식을 외운후에는 이차방정식이 얼추 인수분해 될거라는게 보이는 것처럼.
