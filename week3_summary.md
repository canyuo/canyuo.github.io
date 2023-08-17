# Week 3 / 데코레이터 패턴(Decorator Pattern)

## 데코레이터 패턴
- 정의 : 주어진 상황 및 용도에 따라 어떤 객체에 책임(기능)을 동적으로 추가하는 패턴
- 기본 기능을 가지고 있는 클래스를 하나 만들어주고 추가할 수 있는 기능들을 추가하기 편하도록 설계하는 방식

![01](https://github.com/canyuo/canyuo.github.io/blob/main/week3_image1.png)
- Component : 실질적인 인스턴스를 컨트롤하는 역할
- ConcreteComponent : Component의 실질적인 인스턴스의 부분으로 책임의 주체의 역할
- Decorator : Component와 ConcreteDecorator를 동일시 하도록 해주는 역할
- ConcreteDecoreator : 실질적인 장식 인스턴스 및 정의이며 추가된 책임의 주체

## 장점
1. 기존 코드를 수정하지 않고도 데코레이터 패턴을 통해 행동을 확장시킬 수 있음
2. 구성과 위임을 통해서 실행중에 새로운 행동을 추가할 수 있음

## 단점
1. 의미없는 객체들이 너무 많이 추가될 수 있음
2. 데코레이터를 너무 많이 사용하면 코드가 필요 이상으로 복잡해질 수 있음

## 데코레이터 패턴을 아래와 같은 상황일 때 사용해주시면 좋음
1. 클래스의 요소들을 계속해서 수정을 하면서 사용하는 구조가 필요한 경우
2. 여러 요소들을 조합해서 사용하는 클래스 구조인 경우
 
```cpp
class Component {
public:
	virtual void add(string& text) {};
};

class BaseComponent : public Component {
public:
	virtual void add(string& text) override {
		text += "에스프레소";
	}
};

class Decorator : public Component {
public:
	Decorator(Component* coffeeComponent)
	{
		this->coffeeComponent = coffeeComponent;
	}

	virtual void add(string& text) override
	{
		coffeeComponent->add(text);
	}

public:
	Component* coffeeComponent;
};

class WaterDecorator : public Decorator{
public:
	WaterDecorator(Component* coffeeComponent)
		: Decorator(coffeeComponent)
	{
	}

	virtual void add(string& text) override
	{
		__super::add(text);
		text += " + 물";
	}
};

class MilkDecorator : public Decorator{
public:
	MilkDecorator(Component* coffeeComponent)
		: Decorator(coffeeComponent)
	{
	}

	virtual void add(string& text) override
	{
		__super::add(text);
		text += " + 우유";
	}
};

class CaramelSyrupDecorator : public Decorator{
public:
	CaramelSyrupDecorator(Component* coffeeComponent)
		: Decorator(coffeeComponent)
	{
	}

	virtual void add(string& text) override
	{
		__super::add(text);
		text += " + 캬라멜시럽";
	}
};

int main() {
	string recipe;
	Component* espresso = new BaseComponent();
	espresso->add(recipe);
	cout << "에스프레소 : " << recipe << endl;

	recipe.clear();
	Component* americano = new WaterDecorator(new BaseComponent());
	americano->add(recipe);
	cout << "아메리카노 : " << recipe << endl;

	recipe.clear();
	Component* latte = new MilkDecorator(new BaseComponent());
	latte->add(recipe);
	cout << "라떼 : " << recipe << endl;

	recipe.clear();
	Component* caramelLatte = new CaramelSyrupDecorator(new MilkDecorator(new BaseComponent()));
	caramelLatte->add(recipe);
	cout << "캬라멜라떼 : " << recipe << endl;

	delete espresso;
	delete americano;
	delete latte;
	delete caramelLatte;

	return 0;
}
```
