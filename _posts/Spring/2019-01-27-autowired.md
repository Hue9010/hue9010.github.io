---
layout: post
title:  "autowired로 의존성을 주입하는 세 가지 방법"
tags: [Spring]
categories: [Spring]
description: "필드, 생성자, 메서드에 autowired 사용하여 의존성을 주입해주는 두 가지 방법을 알아보자"
image:
  feature: header/java-spring-logo.png
---

기존에는 `Autowired`를 필드에서만 사용 할 수 있는줄 알았는데 최근 회사의 수석님과 백기선님의 스프링 강의를 통해 생성자 및 메서드를 통해서도 주입할 수 있다는 것을 알게 되었습니다.

필드, 생성자, 메서드에 `Autowired`를 사용하는 방법을 알아보도록 하겠습니다.


## 필드  

```java
@Controller
class OwnerController {

    @Autowired
    private OwnerRepository owners;

}
```

일반적으로 많이 사용하는 방식입니다.  

---

## 생성자  

```java
@Controller
class OwnerController {

    private OwnerRepository owners;

    public OwnerController(OwnerRepository clinicService) {
        this.owners = clinicService;
    }
}
```

생성자가 하나 일 때는 `Autowired`를 생략할 수 있습니다. 만약 생성자가 두 개 이상이라면

```java
@Controller
class OwnerController {

    private OwnerRepository owners;

    @Autowired
    public OwnerController(OwnerRepository clinicService) {
        this.owners = clinicService;
    }

    public OwnerController(){
    }
}
```

의존성을 주입할 생성자에 `Autowired`를 달아 주시면 됩니다.

참고로 생성자를 통해 의존성을 주입해주는 것이기 때문에 변수를 `private final`로 선언할 수도 있습니다.(실제 예제로 사용한 샘플 코드가 `privagte final`로 되어있습니다.)

---

## setter

```java
@Controller
class OwnerController {

    private OwnerRepository owners;

    @Autowired
    public void setOwners(OwnerRepository owners) {
        this.owners = owners;
    }
}
```

사실 `setter`메서드가 아니라도 가능합니다.

```java
@Controller
class OwnerController {

    private OwnerRepository owners;

    @Autowired
    public void test(OwnerRepository owners) {
        this.owners = owners;
    }
}
```

이처럼 전혀 다른 메서드명을 사용해도 owners에 제대로 된 빈이 주입됩니다.  
다만 값 설정 때는 `setter`를 쓰는 게 다른 개발자 및 미래의 자신을 위해 좋은 방법이라고 생각합니다.

---

## 무엇을 사용해야 하나?

정답은 없습니다. 개인적으로는 생성자를 통해 의존성을 주입하는 것이 가장 나은 방법이라고 생각합니다. 거기서도 하나의 생성자를 통해 `Autowired`가 없는 경우가 가장 좋은 것 같습니다. 그렇게 생각하는 두 가지 이유는 스프링에 대한 의존성이 줄어든다는 것, 그로 인해 테스트하기 쉬워진다는 것입니다. 하지만 조심해야 할 것은 필드를 통해 주입하는 방법만 알고 있는 사람이 보면 알 수 없는 코드가 되어 버릴 수도 있다는 점과 가장 좋은 컨벤션은 팀이 합의한 컨벤션이란 걸 잊지 않으시길 바랍니다!

---

### 참고 자료

- 백기선님의 인프런 [스프링 프레임워크 입문](https://www.inflearn.com/course/spring/) 강의  

- [Spring Boot Reference Guide](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-spring-beans-and-dependency-injection)

- https://ohjongsung.io/2017/06/02/%ED%95%84%EB%93%9C-%EC%A3%BC%EC%9E%85-field-injection-%EC%9D%84-%ED%94%BC%ED%95%98%EC%9E%90

예제로 사용한 코드는 스프링부트 [샘플 애플리케이션](https://github.com/spring-projects/spring-petclinic)이며 원래는 하나의 생성자를 통해 `Autowired` 없이 주입하는 방식으로 구현되어 있습니다. 또한, owners가 `final`로 선언되어 있으나 필드, 메서드에 `Autowired`를 사용하기 위해 `final`을 제거하고 진행하였습니다.
