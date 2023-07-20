# Week 8 / 파사드패턴(FACADE PATTERN)

## 어댑터 패턴(==Wrapper)
- 정의 : 하위 시스템을 보다 쉽게 사용할 수 있게 해주는 고급 인터페이스를 정의
## Adapter 패턴 vs Facade 패턴 vs Decorator 패턴
- Adapter 패턴 : 원래 코드를 다른 코드와 작동할 수 있게 래퍼를 제공
- Facade 패턴 : 원래 코드를 더 쉽게 처리 할 수 있는 래퍼를 제공
- Decorator 패턴 : 인터페이스는 바꾸지 않고 책임(기능)만 추가

![01](https://github.com/canyuo/canyuo.github.io/blob/main/week8_image1.png)

```cpp

class Computer {
public:
	void turnOn() {
		turnedOn = true;
		cout << "컴퓨터를 킨다." << endl;
	}
	void turnOff() {
		turnedOn = false;
		cout << "컴퓨터를 끈다." << endl;
	}
	bool isTurnedOn() {
		return turnedOn;
	}

private:
	bool turnedOn = false;
};

class Light {
public:
	void turnOn() {
		turnedOn = true;
		cout << "불을 킨다." << endl;
	}
	void turnOff() {
		turnedOn = false;
		cout << "불을 끈다." << endl;
	}
	bool isTurnedOn() {
		return turnedOn;
	}

private:
	bool turnedOn = false;
};

class Radio {
public:
	void turnOn() {
		turnedOn = true;
		cout << "라디오를 킨다." << endl;
	}
	void turnOff() {
		turnedOn = false;
		cout << "라디오를 끈다." << endl;
	}
	bool isTurnedOn() {
		return turnedOn;
	}

private:
	bool turnedOn = false;
};

class HomeFacade {
public:
	HomeFacade(shared_ptr<Computer> newComputer, shared_ptr<Light> newLight, shared_ptr<Radio> newRadio) {
		computer = newComputer;
		light = newLight;
		radio = newRadio;
	}
	void homeIn() {
		cout << "집에 오면 컴퓨터 켜고, 라디오 켜고, 불 켜기." << endl;
		if (!computer->isTurnedOn()) {
			computer->turnOn();
		}
		if (!light->isTurnedOn()) {
			light->turnOn();
		}
		if (!radio->isTurnedOn()) {
			radio->turnOn();
		}
	}
	void homeOut() {
		cout << "컴퓨터 끄고, 라디오 끄고, 불 끄고 외출하기." << endl;
		if (computer->isTurnedOn()) {
			computer->turnOff();
		}
		if (light->isTurnedOn()) {
			light->turnOff();
		}
		if (radio->isTurnedOn()) {
			radio->turnOff();
		}
	}

private:
	shared_ptr<Computer> computer;
	shared_ptr<Light> light;
	shared_ptr<Radio> radio;
};

int main()
{
	shared_ptr<Computer> computer(new Computer());
	shared_ptr<Light> light(new Light());
	shared_ptr<Radio> radio(new Radio());
	unique_ptr<HomeFacade> home(new HomeFacade(computer, light, radio));

	// 이전 사용 방식
	cout << "========집에 나갈때 파사드 적용 전==========" << endl;
	computer->turnOff();
	light->turnOff();
	radio->turnOff();
	//home->homeOut();

	// 파사드 패턴 적용 후 사용 방식
	cout << "========집에 들어올때 파사드 적용 후==========" << endl;
	home->homeIn();

	return 0;
}
```
