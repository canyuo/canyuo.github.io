# Week 1 / 전략 패턴(Strategy Pattern)

## 전략 패턴
- 정의 : 알고리즘군을 정의하고 캡술화해서 각각의 알고리즘군을 수정해서 쓸 수 있게 해준다. 전략 패턴을 사용하면 클라이언트로부터 알고리즘을 분리해서 독립적으로 변경 가능하다
- '전략'을 쉽게 바꿀 수 있도록 해줌
- 같은 문제를 해결하는 여러 알고리즘이 클래스별로 캡슐화되어 있고 이들이 필요할때 교체할 수 있도록 함으로써 동일한 문제를 다른 알고리즘으로 해결할 수 있게 하는 디자인 패턴

## 사용해야하는 이유
객체화 할때 상속을 이용하여 내부를 구현하게되면 SOLID의 원칙 중 OCP( Open-Closed Principle )에 위배되며 메서드의 중복을 줄이기 위하여 전략패턴을 사용해야 함

![01](https://github.com/canyuo/canyuo.github.io/blob/main/DesignPattern/week1_image1.png)
- Strategy: 인터페이스나 추상 클래스로 외부에서 동일한 방식으로 알고리즘을 호출하는 방법을 명시
- ConcreateStrategy: 스트래티지 패턴에서 명시한 알고리즘을 실제로 구현한 클래스
- Context: 스트래티지 패턴을 이용하는 역할 수행. 필요에 따라 동적으로 구체적인 전략을 바꿀 수 있도록 setter 메서드 제공

```cpp
//OCP 위배 구조 예제

class Movable {
public:
	virtual void move() {};
};

class Train : public Movable {
public:
	void move() override {
		cout << "선로를 통해 이동" << endl;
	}
};

class Bus : public Movable {
public:
	void move() override {
		cout << "도로를 통해 이동" << endl;
	}
};

int main()
{
	Movable* train = new Train();
	Movable* bus = new Bus();

	train->move();
	bus->move();
}
```

```cpp
//전략패턴 예제
//Strategy
class MovableStrategy {
public:
	virtual void move() {};
};

//ConcreateStrategy
class RailLoadStrategy : public MovableStrategy {
public:
	RailLoadStrategy() {};
	void move() override {
		cout << "선로를 통해 이동" << endl;
	}
};

class LoadStrategy : public MovableStrategy {
public:
	LoadStrategy() {};
	void move() override {
		cout << "도로를 통해 이동" << endl;
	}
};

class Moving {
public:
	virtual ~Moving() {
		delete movableStrategy;
	}
	void move() {
		movableStrategy->move();
	}

	void setMovableStrategy(MovableStrategy* movableStrategy) {
		this->movableStrategy = movableStrategy;
	}

private:
	MovableStrategy* movableStrategy;
};

class Bus : public Moving {
public:
	Bus() {};
	virtual ~Bus() {};
};
class Train : public Moving {
public:
	Train() {};
	virtual ~Train() {};
};

//Context
int main() {
	Moving* train = new Train();
	Moving* bus = new Bus();

	/*
		기존의 기차와 버스의 이동 방식
		1) 기차 - 선로
		2) 버스 - 도로
	 */
	train->setMovableStrategy(new RailLoadStrategy());
	bus->setMovableStrategy(new LoadStrategy());

	train->move();
	bus->move();

	/*
		선로를 따라 움직이는 버스가 개발
	 */
	bus->setMovableStrategy(new RailLoadStrategy());
	bus->move();

	return 0;
}
```


컴포넌트패턴과의 차이점?
- 컴포넌트 패턴은 본질적으로 복잡한 시스템을 구성요소화해야 한다고 말하는 것
- 전략 패턴은 런타임에 다양한 구현을 연결하는 방법
