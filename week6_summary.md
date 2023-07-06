# Week 6 / 커맨드 패턴 (Command Pattern)

## 커맨드 패턴
- 정의 : 실행될 기능을 캡슐화함으로써 주어진 여러 기능을 실행할 수 있는 재사용성이 높은 클래스를 설계하는 패턴
- 보통 매개변수를 이용해서 기능에 다른 요구 사항들을 넣을 수 있도록 작성

![01](https://github.com/canyuo/canyuo.github.io/blob/main/week6_image1.png)
- Client : 커맨드를 실행하는 객체
- Invoker(호출자) : 커맨드 실행 방법을 아는 객체
- Receiver(수신자) : 실제 작업을 수행하는 객체
- Command(명령) : 실행할 작업과 데이터 모음

## 장점
- 인보커와 리시버, 커맨드가 각각 캡슐화 되어서 결합도가 낮아짐
- Single Responsibility Principle (단일 책임 원칙)을 만족
- OCP (개방 / 폐쇄 원칙)를 만족
## 단점
- 리시버 객체의 동작이 늘어날 때 마다 커맨드 클래스가 늘어나기 때문에 클래스가 많아짐
- Receiver, Invoker 사이에 관계를 도입해야 하므로 코드가 복잡

```cpp
class Computer {
public:
	void turnOn() { cout << "[on] 컴퓨터가 켜졌습니다!" << endl; }
	void turnOff() { cout << "[off] 컴퓨터가 꺼졌습니다!" << endl; }
};

class Command {
public:
	virtual void execute(){ }
};

class ComputerOnCommand : public Command {
public:
	ComputerOnCommand(Computer* newComputer){
		computer = newComputer;
	}

	virtual void execute() override {
		computer->turnOn();
	}

private:
	Computer* computer;
};

class ComputerOffCommand : public Command {
public:
	ComputerOffCommand(Computer* newComputer){
		computer = newComputer;
	}

	virtual void execute() override {
		computer->turnOff();
	}

private:
	Computer* computer;
};

class Button {
public:
	Button(Command* newCommand)
	{
		setCommand(newCommand);
	}

	void setCommand(Command* newCommand)
	{
		command = newCommand;
	}

	void pressButton()
	{
		if (nullptr == command)
			return;

		command->execute();
	}
private:
	Command* command;
};

int main()
{
	//리시버
	unique_ptr<Computer> computer(new Computer());

	unique_ptr<ComputerOnCommand> computerOnCmd( new ComputerOnCommand(computer.get()) );
	unique_ptr<ComputerOffCommand> computerOffCmd( new ComputerOffCommand(computer.get()) );

	//인보커
	unique_ptr<Button> btn( new Button(computerOnCmd.get()) );

	btn->pressButton();
	btn->setCommand(computerOffCmd.get());
	btn->pressButton();

	return 0;
}
```
