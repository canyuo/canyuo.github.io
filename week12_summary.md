# Week 12 / 프록시 패턴(Proxy Pattern)

## 프록시 패턴(Proxy Pattern)
- 정의
  - 프록시에게 어떤 일을 대신 시키는 것
  - 어떤 객체를 사용하고자 할때, 객체를 직접적으로 참조하는 것이 아닌 해당 객체를 대항하는 객체를 통해 대상 객체에 접근하는 방식을 사용
  - 해당 객체가 메모리에 존재하지 않아도 기본적인 정보를 참조하거나 설정 가능
  - 실제 객체의 기능이 필요한 시점까지 객체의 생성을 미룰 수 있음
  
![01](https://github.com/canyuo/canyuo.github.io/blob/main/week12_image1.png)

- 장점
  - 사이즈가 큰 객체가 로딩되기 전에도 프록시를 통해 참조 가능
  - 실제 객체의 public, protected 메소드를 숨기고 인터페이스를 통해 노출 가능
  - 로컬에 있지 않고 떨어져있는 객체를 사용 가능
  - 원래 객체에 접근에 대해 사전처리 가능

- 단점
  - 객체를 생성할 때 한 단계를 거치게 되므로, 빈번한 객체 생성이 필요한 경우 성능이 저하
  - 프록시 내부에서 객체 생성을 위해 스레드가 생성, 동기화가 구현되어야 하는 경우 성능이 저하
  - 로직이 난해해져 가독성이 떨어짐

- 종류
  - 가상 프록시
    - 꼭 필요로 하는 시점까지 객체의 생성을 연기하고, 해당 객체가 생성된 것 처럼 동작하도록 만들고 싶을 때 사용하는 패턴
    - 프록시 클래스에서 작은 단위의 작업을 처리하고 리소스가 많이 요구되는 작업들이 필요할 경우만 주체 클래스를 사용하도록 구현

  - 원격 프록시
    - 원격 객체에 대한 접근을 제어 로컬 환경에 존재하며, 원격 객체에 대한 대변자 역할을 하는 객체 서로 다른 주소 공간에 있는 객체에 대해 마치 같은 주소 공간에 있는 것 처럼 동작하게 하는 패턴
    - 예시 : Google Docs

  - 보호 프록시
    - 주체 클래스에 대한 접근을 제어하기 위한 경우에 객체에 대한 접근 권한을 제어하거나 객체마다 접근 권한을 달리하고 싶을 경우 사용하는 패턴
    - 프록시 클래스에서 클라이언트가 주체 클래스에 대한 접근을 허용할지 말지 결정 가능
   
```cpp
class Image {
public:
	virtual void displayImage() = 0;
};

class Real_Image : public Image {
public:
	Real_Image(const string& newFileName)
	{
		fileName = newFileName;
		loadFromDisk(fileName);
	}

	virtual void displayImage() override
	{
		cout << "보여주기 : " << fileName << endl;
	}

private:
	void loadFromDisk(string& loadFileName)
	{
		cout << "로딩 : " << loadFileName << endl;
	}

private:
	string fileName;
};

class Proxy_Image : public Image
{
public:
	Proxy_Image(const string& newFileName)
	{
		fileName = newFileName;
	}

	virtual void displayImage() override
	{
		if (nullptr == realImage) {
			realImage.reset(new Real_Image(fileName));
		}

		realImage->displayImage();
	}

private:
	string fileName;
	shared_ptr<Real_Image> realImage;
};

int main() {
	unique_ptr<Image> image1(new Proxy_Image("test1.jpg"));
	unique_ptr<Image> image2(new Proxy_Image("test2.jpg"));

	image1->displayImage();
	image2->displayImage();

	return 0;
}
```
![02](https://github.com/canyuo/canyuo.github.io/blob/main/week12_image2.png)

