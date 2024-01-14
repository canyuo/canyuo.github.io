# Week 5 / 싱글톤 패턴 (Singleton Pattern)

## 싱글톤 패턴

![01](https://github.com/canyuo/canyuo.github.io/blob/main/DesignPattern/week5_image1.png)

- 정의 : 객체의 인스턴스가 오직 1개만 생성되는 패턴

## 사용이유 및 장점
- 메모리 낭비 방지(최초 한번의 new 연산자를 통해서 고정된 메모리 영역 사용)
- 속도 측면 이점(이미 생성된 인스턴스를 활용)
- 다른 클래스 간 데이터 공유 용이
- 도메인 관점에서 인스턴스가 하나만 존재함을 보증
- 코드의 가독성 상승

## 단점
- 여러곳에서 공유할 경우 다른 클래스의 인스턴스 간 의존성이 높아질 수 있음(추후 수정 및 테스트가 어려워짐)
- 멀티스레드 환경에서 객체가 1개 이상 생성될 여지가 있음

```cpp
class Singleton {
private:
	//복사생성자, 생성자, 소멸자를 private으로 선언하여 복사, 상속을 방지
	Singleton() {
		cout << "싱글톤 생성!!" << endl;
	}
	Singleton(const Singleton& ref) {}
	Singleton& operator=(const Singleton& ref) {}
	~Singleton()
	{
		cout << "싱글톤 소멸!!" << endl;
	}
public:
	static Singleton& getInstance() {
		//객체를 data영역에 선언해 행여 발생될 데이터의 해제를 방지
		//static Singleton s 변수를 getIncetance() 함수 안에 넣음으로 써 늦은 초기화가 가능
		static Singleton instance;
		return instance;
	}

	void say() {
		cout << "내가 가지고 있는 숫자는 : " << settingNum << endl;
	}

	void setNum(int newNum)
	{
		settingNum = newNum;
	}

private:
	int settingNum = 3;
};

int main()
{
	cout << "프로그램 시작" << endl;
	Singleton& singleton = Singleton::getInstance();

	singleton.say();
	singleton.setNum(5);
	singleton.say();

	return 0;
}
```

## 참고
- 피닉스 싱글톤 패턴(phoenix singleton) : 파괴된 메모리가 다시 생성되는 싱글톤, 마이어스 싱글톤(singleton)의 한계를 극복하는 싱글톤
