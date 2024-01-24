---
layout: post
title: 싱글턴 패턴(Singleton Pattern)
categories: Java
date: 2024-01-25 03:22:00 +0900
---
<b>싱글턴 패턴(Singleton Pattern)</b>에 대해 알아보겠다.

소프트웨어 디자인 패턴에서 싱글턴 패턴을 따르는 클래스는, 생성자가 여러 차례 호출되더라도 실제로 생성되는 객체는 하나이고 최초 생성 이후에 호출된 생성자는 최초의 생성자가 생성한 객체를 리턴한다.

객체는 유일하게 하나만 생성되도록 한다는 것이다. 왜 이렇게 할까?

<u>때로는 객체가 하나만 있으면 되는 경우가 있다.</u> 가령, 테트리스 게임에서 화면에 score을 표현하는 것은 하나만 있으면 된다.

```java
public class Manager {

    private static Manager manager = new Manager();

    private Manager() {}

    public static Manager getManager() {
        return manager;
    }
}
```

위 코드와 같이, Manager 생성자를 private을 접근 제한시켜놨다. 외부에서 호출할 수 없는 것이다. 대신, static 영역에 생성자로 생성해둔 manager을 넣어놓고, ```getManager()``` 메서드로 외부에서 사용할 수 있게 해놨다. <b>객체를 생성할 때, static 메모리 로딩 시점에 딱 하나만 생성되는 것이다.</b>

<br>

다음 예시를 보자.

```java
public class MovieManager {

    private static MovieManager instance;

    private MovieManager() {}   // 생성자 private으로 막혀 있음

    public static MovieManager getInstance() {
        if (instance == null)
            instance = new MovieManager();
        return instance;
    }
}
```

```java
public class MovieTest {

    public static void main(String[] args) {

        MovieManager manager = MovieManager.getInstance();  // static이기 때문에 클래스에서 직접 접근

        // MovieManager manager = new MovieManager();   // private으로 막혀 있어서 생성자로 생성 불가
    }
}
```

위 코드는 영화 객체 배열을 관리하기 위해 MovieManager 클래스를 생성해둔 예시이다. 영화 리스트의 경우 여러 개를 생성해야 하므로 싱글턴을 사용하기 적합하지 않다. 하지만, 영화 객체들을 관리해주는 영화 매니저 클래스는 하나만 있는 것이 적합하다.

우선, MovieManager클래스에서 static 영역에 instance를 위치시켰다. 그리고 만약 instance가 null이면 인스턴스를 하나 생성하고 리턴하는 getInstance() 로직을 넣어놨다. MovieManager의 생성자는 private으로 막아놔 외부 클래스에서 접근할 수 없게 해놨다.

다음으로, MovieTest클래스에서 MovieManager클래스에서 바로 getInstance() 메서드를 통해 인스턴스를 가져왔다. static이기 때문에 클래스에 직접 접근하는 것이다. 앞서 MovieManager클래스에서 생성자에 접근 제한자를 private으로 걸어두었으므로, 만약 ```new MovieManager();```을 호출할 경우 오류가 발생한다.

만약 MovieManager클래스에서 ```public MovieManager() {}```로 해서 생성자의 접근 제한자를 public으로 해두었다면, MovieTest클래스에서 생성자로 생성이 가능해진다. 그러면 MovieManager클래스의 instance와는 전혀 다른 주소 값을 가리키는 새로운 인스턴스가 생성되는 것이다. 싱글턴이 지켜지지 않는 것이다.