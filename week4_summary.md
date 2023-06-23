# Week 4 / 팩토리 패턴 (Factory Pattern)

## 팩토리 패턴
- 정의 : 객체 생성을 공장(Factory) 클래스로 캡슐화 처리하여 대신 생성하게 하는 생성 디자인 패턴
- 추상 팩토리 패턴 : 상속으로 객체 생성
- 팩토리 메소드 패턴 : 객체 구성(Composition) 으로 객체 생성
  
![01](https://github.com/canyuo/canyuo.github.io/blob/main/week4_image1.png)
- Creator : 최상위 공장 클래스로서, 팩토리 메서드를 추상화하여 서브 클래스로 하여금 구현하도로 함
- 객체 생성 처리 메서드(someOperartion) : 객체 생성에 관한 전처리, 후처리를 템플릿화한 메소드
- 팩토리 메서드(createProduct) : 서브 공장 클래스에서 재정의할 객체 생성 추상 메서드
- ConcreteCreator : 각 서브 공장 클래스들은 이에 맞는 제품 객체를 반환하도록 생성 추상 메소드를 재정의한다. 즉, 제품 객체 하나당 그에 걸맞는 생산 공장 객체가 위치된다.
- Product : 제품 구현체를 추상화
- ConcreteProduct : 제품 구현체

```cpp

class Dough {
public:
	Dough() {};
};

class ThinCrustdough : public Dough {
public:
	ThinCrustdough() {};
};

class ThickCrustDough : public Dough {
public:
	ThickCrustDough() {};
};

class Sauce {
public:
	Sauce() {};
};

class MarinaraSauce : public Sauce {
public:
	MarinaraSauce() {};
};

class PlumTomatoSauce : public Sauce {
public:
	PlumTomatoSauce() {};
};

class Cheese {
public:
	Cheese() {};
};

class ReggianoCheese : public Cheese {
public:
	ReggianoCheese() {};
};

class MozzarellaCheese : public Cheese {
public:
	MozzarellaCheese() {};
};

class Veggies {
public:
	Veggies() {};
};

class Farlic : public Veggies {
public:
	Farlic() {};
};

class Onion : public Veggies {
public:
	Onion() {};
};

class Mushroom : public Veggies {
public:
	Mushroom() {};
};

class RedPepper : public Veggies {
public:
	RedPepper() {};
};

class BlackOlives : public Veggies {
public:
	BlackOlives() {};
};

class Spinach : public Veggies {
public:
	Spinach() {};
};

class EggPlant : public Veggies {
public:
	EggPlant() {};
};

class Pepperoni {
public:
	Pepperoni() {};
};

class SlicedPepperoni : public Pepperoni {
public:
	SlicedPepperoni() {};
};

class Clams {
public:
	Clams() {};
};

class Freshclams : public Clams {
public:
	Freshclams() {};
};

class FrozenClam : public Clams {
public:
	FrozenClam() {};
};

class PizzaIngredientFactory {
public:
	virtual Dough* createDough() { return nullptr; };
	virtual Sauce* createSauce() { return nullptr; };
	virtual Cheese* createCheese() { return nullptr; };
	virtual Veggies* createVeggies() { return nullptr; };
	virtual Pepperoni* createPepperoni() { return nullptr; };
	virtual Clams* createClams() { return nullptr; };

public:
	string CC_STYLE = "Chicago Style";
	string NY_STYLE = "NewYork Style";
};

class NYPizzaingredientFactory : public PizzaIngredientFactory{
public:
	virtual Dough* createDough() { return new ThinCrustdough(); };
	virtual Sauce* createSauce() { return new MarinaraSauce(); };
	virtual Cheese* createCheese() { return new ReggianoCheese(); };
	virtual Veggies* createVeggies() {
		Veggies veggies[] = { Farlic(), Onion(), Mushroom(), RedPepper() };
		return veggies;
	};
	virtual Pepperoni* createPepperoni() { return new SlicedPepperoni(); };
	virtual Clams* createClams() { return new Freshclams(); };
};

class ChicagoPizzaingredientFactory : public PizzaIngredientFactory{
public:
	virtual Dough* createDough() { return new ThickCrustDough(); };
	virtual Sauce* createSauce() { return new PlumTomatoSauce(); };
	virtual Cheese* createCheese() { return new MozzarellaCheese(); };
	virtual Veggies* createVeggies() {
		Veggies veggies[] = { BlackOlives(), Spinach(), EggPlant() };
		return veggies;
	};
	virtual Pepperoni* createPepperoni() { return new SlicedPepperoni(); };
	virtual Clams* createClams() { return new FrozenClam(); };

};

class Pizza {
public:
	virtual void prepare() {
		cout << getname() << endl;
	};

	void bake() {
		cout << "Bake for 25 minutes at 350" << endl;
	}

	void cut() {
		cout << "Cutting the pizza into diagonal slices" << endl;
	}

	void box() {
		cout << "Place pizza in official PizzaStore box" << endl;
	}

	string& getname() {
		return name;
	}
	
	void setName(string newName) {
		name = newName;
	}

public:
	string			name;
	Dough*			dough;
	Sauce*			sauce;
	Veggies*		veggies;
	Cheese*			cheese;
	Pepperoni*		pepperoni;
	Clams*			clams;
};

class CheesePizza : public Pizza {
public:
	CheesePizza(PizzaIngredientFactory* ingredientFactory)
	{
		this->ingredientFactory = ingredientFactory;
	}

	virtual void prepare() override
	{
		__super::prepare();
		this->dough = ingredientFactory->createDough();
		this->sauce = ingredientFactory->createSauce();
		this->cheese = ingredientFactory->createCheese();
		this->clams = ingredientFactory->createClams();
	};

public:
	PizzaIngredientFactory* ingredientFactory;
};

class ClamPizza : public Pizza {
public:
	ClamPizza(PizzaIngredientFactory* ingredientFactory)
	{
		this->ingredientFactory = ingredientFactory;
	}

	virtual void prepare() override
	{
		this->dough = ingredientFactory->createDough();
		this->sauce = ingredientFactory->createSauce();
		this->cheese = ingredientFactory->createCheese();
	};

public:
	PizzaIngredientFactory* ingredientFactory;
};

class PizzaStore {

public:
	virtual Pizza* createPizza(string type) = 0;
	Pizza* orderPizza(string type) {
		Pizza* pizza = createPizza(type);
		pizza->prepare();
		pizza->bake();
		pizza->cut();
		pizza->box();

		return pizza;
	}
};

class NYPizzaStore : public PizzaStore {
public:
	Pizza* createPizza(string type)
	{
		Pizza* pizza = nullptr;
		PizzaIngredientFactory* ingredientFactory = new NYPizzaingredientFactory();

		if (type._Equal("cheese")) {
			pizza = new CheesePizza(ingredientFactory);
			pizza->setName(ingredientFactory->NY_STYLE + " Cheese Pizza");
		}
		//		else if (type._Equal("peper")) {
		//			pizza = new PepperoniPizza(ingredientFactory);
		//			pizza.setName(ingredientFactory.NY_STYLE + " Pepperoni Pizza");
		//		}
		else if (type._Equal("clam")) {
			pizza = new ClamPizza(ingredientFactory);
			pizza->setName(ingredientFactory->NY_STYLE + " Clam Pizza");
		}
		//		else if (type._Equal("veggie")) {
		//			pizza = new VeggiePizza(ingredientFactory);
		//			pizza.setName(ingredientFactory.NY_STYLE + " Veggie Pizza");
		//		}

		return pizza;
	}
};

int main()
{
	PizzaStore* nyPizzaStore = new NYPizzaStore();
	std::unique_ptr<Pizza> cheesePizza(nyPizzaStore->orderPizza("cheese"));

	return 0;
}
```

## Template Method 패턴과 Factory Method 패턴과의 관계
뭔가 이름 구성이 비슷해서 둘이 어떠한 관계가 있어 보이는데, 템플릿 메서드는 행동 패턴이고 팩토리 메서드는 생성 패턴이라 둘은 전혀 다른 패턴이다.
다만 클래스 구조의 결은 둘이 같다고 보면 되는데, 인스턴스를 생성하는 공장을 Template Method 패턴으로 구성한 것이 Factory Method 패턴이 되기 때문이다.
Template Method 패턴에서는 하위 클래스에서 구체적인 처리 알고리즘의 내용을 만들도록 추상 메소드를 상속 시켰었다. 이 로직을 알고리즘 내용이 아닌 인스턴스 생성에 적용한 것이 Factory Method 패턴 인 것이다.
