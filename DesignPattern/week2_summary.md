# Week 2 / 옵저버 패턴(Observer Pattern)

## 옵저버 패턴
- 정의 : 객체의 상태 변화를 관찰하는 관찰자들, 즉 옵저버들의 목록을 객체에 등록하여 상태 변화가 있을 때마다 메서드 등을 통해 객체가 직접 목록의 각 옵저버에게 통지하도록 하는 디자인 패턴
- 주로 분산 이벤트 핸들링 시스템을 구현하는 데 사용
- 발행/구독 모델

![01](https://github.com/canyuo/canyuo.github.io/blob/main/week2_image1.png)

## 장점
- 상태를 변경하는 객체(publicher)와 변경을 감지하는 객체(subscriber)의 관계를 느슨하게 유지가능
- Subject의 상태 변경을 주기적으로 조회하지않고 자동으로 감지
- 런타임에 옵저버를 추가하거나 제거 가능

## 단점
- 복잡도 증가
- 다수의 Observer 객체를 등록 이후 해지하지 않는다면 memory leak 이 발생 가능

```cpp
#include <map>

class Observer {
public:
	void receive(string& msg) {
		cout << name + "에서 메시지를 받음 : " + msg << endl;
	}

public:
	string name;
};

class User1 : public Observer {
public:
	User1(string name) {
		this->name = name;
	}
};

class User2 : public Observer {
public:
	User2(string name) {
		this->name = name;
	}
};

class Notice {
public:
	void attach(string name, Observer* observer) {
		observers.insert(make_pair(name, observer));
	}

	// 옵저버에서 제거
	void detach(string name) {
		observers.erase(name);
	}

	// 옵저버들에게 알림
	void notifyObservers(string& msg) {
		map<string, Observer*>::iterator iter = observers.begin();
		for (; iter != observers.end(); iter++) {
			iter->second->receive(msg);
		}
	}
private:
	map<string, Observer*> observers;
};

int main() {
	Notice* notice = new Notice();
	User1* user1 = new User1("유저1");
	User2* user2 = new User2("유저2");

	notice->attach("user1", user1);
	notice->attach("user2", user2);

	string msg = "공지사항입니다~!";
	notice->notifyObservers(msg);

	notice->detach("user1"); // user1 공지사항 받는 대상에서 제거
	msg = "안녕하세요~";
	notice->notifyObservers(msg);

	delete notice;
	delete user1;
	delete user2;

	return 0;
}
```
