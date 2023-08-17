# Week 10 / 이터레이터와 컴포지트 패턴(Iterator And Composite Pattern)

## 이터레이터(Iterator) 패턴
- 정의
  - 컬렉션 구현 방법을 노출시키지 않으면서도 모든 항목에 접근할 수 있게 해주는 방법을 제공 (반복 작업 캡슐화)
  - 각 항목에 접근하는 기능을 집합체(==컬렉션,aggregate)가 아닌 반복자 객체에서 책임져 집합체 인터페이스 및 구현이 간단해짐
- 여러 클래스에 공통되는 사항은 상위 추상 클래스에서 구현하고, 각각의 상세부분은 하위 클래스에서 구현
- 코드의 중복을 줄이고, Refactoring에 유리한 패턴으로 상속을 통한 확장 개발 방법
  
![01](https://github.com/canyuo/canyuo.github.io/blob/main/week10_image1.jpg)

## 컴포지트(Composite) 패턴
- 정의
  - 컴포지트 패턴을 이용하면 객체들을 트리 구조로 구성하여 부분과 전체를 나타내는 계층구조로 만들 수 있음
  - 클라이언트에서 개별 객체와 다른 객체들로 구성된 복합 객체(composite)를 똑같은 방법으로 다룰 수 있음
- 장점 : 클라이언트 단순화, 메소드 하나로 전체 구조에 대한 반복 가능
- 고려해야 할 점들
  - 자식 간의 특별한 순서가 필요한 경우, 추가/제거/조회 시 더 복잡한 관리 방법을 사용해야함
  - 복합구조가 너무 복잡하거나 많아져 많은 자원이 필요한 경우, 특정 작업에 대한 결과를 캐싱해 둘 수도 있음
    
![02](https://github.com/canyuo/canyuo.github.io/blob/main/week10_image2.jpg)
- Object
  - Base Component : 베이스 컴포넌트는 클라이언트가 composition 내의 오브젝트들을 다루기 위해 제공되는 인터페이스. 베이스 컴포넌트는 인터페이스 또는 추상 클래스로 정의되며 모든 오브젝트들에게 공통되는 메소드를 정의
  - Leaf : composition 내 오브젝트들의 행동을 정의. 이는 복합체를 구성하는 데 중요한 요소이며, 베이스 컴포넌트를 구현. 그리고 Leaf는 다른 컴포넌트에 대해 참조를 가지면 안됨
  - Composite : Leaf 객체들로 이루어져 있으며 베이스 컴포넌트 내 명령들을 구현

## Composite vs Decorator
- Composite와 Decorator 패턴은 비슷한 구조를 갖고 있는데, 이 두 패턴 모두가 여러 객체를 조직화하기 위해 재귀적 합성 기법을 사용하기 때문
- 이런 구조적 유사성으로 Decorator 객체를 약화된 합성 객체로 간주할 수도 있겠지만, 이는 Decorator의 중요한 특성을 놓치고 있는 것( 이 두 패턴의 공통성은 구조에서만 나타날 뿐 그 목적은 전혀 다름)
- Decorator 패턴 : 상속없이 객체에 새로운 서비스를 추가하려는 목적
- Composite 패턴 : 클래스의 구조화에 초점이 맞추어진 것으로서 어떻게 하면 관련된 객체들을 하나의 인터페이스로 다룰 수 있도록 일관성을 부여할 것인가가 중요한 관건
  
```cpp
//Base Component
class Shape {
public:
	virtual void draw(const string& fillColor) {};
};

//Leaf Objects
class Triangle : public Shape {
public:
	virtual void draw(const string& fillColor) override
	{
		cout << "Drawing Triangle with color : " << fillColor << endl;
	}
};

class Circle : public Shape {
public:
	virtual void draw(const string& fillColor) override
	{
		cout << "Drawing Circle with color : " << fillColor << endl;
	}
};

#include <list> 
//Composite Objects
class Drawing : public Shape {
public:
	void draw(const string& fillColor)
	{
		for (shared_ptr<Shape> sh : shapes) {
			sh->draw(fillColor);
		}
	}

	//adding shape to drawing
	void add(shared_ptr<Shape> s)
	{
		shapes.push_back(s);
	}

	//removing shape from drawing
	void remove(shared_ptr<Shape> s)
	{
		shapes.remove(s);
	}

	//removing all the shapes
	void clear()
	{
		cout << "Clearing all the shapes from drawing" << endl;
		shapes.clear();
	}

private:
	//collection of Shapes
	list<shared_ptr<Shape>> shapes;
};

int main() {
	shared_ptr<Shape> tri(new Triangle());
	shared_ptr<Shape> tri1(new Triangle());
	shared_ptr<Shape> cir(new Circle());
	shared_ptr<Drawing> drawing(new Drawing());

	drawing->add(tri1);
	drawing->add(tri1);
	drawing->add(cir);

	const string colorRed = ("Red");
	drawing->draw(colorRed);

	///////////////////////////////
	cout << endl;
	///////////////////////////////

	list<shared_ptr<Shape>> shapes;
	shapes.push_back(drawing);
	shapes.push_back(shared_ptr<Shape>(new Triangle()));
	shapes.push_back(shared_ptr<Shape>(new Circle()));

	const string colorGreen = ("Green");
	for (shared_ptr<Shape> shape : shapes) {
		shape->draw(colorGreen);
	}
	
	return 0;
}
```
