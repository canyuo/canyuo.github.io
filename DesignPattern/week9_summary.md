# Week 9 / 템플릿 메소드 패턴(Template Method Pattern)

## 템플릿 메소드 패턴
- 정의 : 상위 클래스에서 처리의 흐름을 제어하며, 하위클래스에서 처리의 내용을 구체화
- 여러 클래스에 공통되는 사항은 상위 추상 클래스에서 구현하고, 각각의 상세부분은 하위 클래스에서 구현
코드의 중복을 줄이고, Refactoring에 유리한 패턴으로 상속을 통한 확장 개발 방법

## 고려사항
1. 멤버 함수들의 접근 범위 지정에 대한 명확화가 필요
2. 가상함수, 일반함수로 선언에 대한 결정이 필요
3. 재정의 함수(virtual)의 수를 줄이는 것이 필요(virtual table 확장에 따른 perfomance 문제점 발생)

![01](https://github.com/canyuo/canyuo.github.io/blob/main/week9_image1.png)

```cpp
//AbstractClass
class FileViewer {
public:
	void viewFile() {
		open();
		view();
		saveLog();
		additionalFunc();
	}
protected:
	virtual void open() = 0;
	virtual void view() = 0;
	virtual void saveLog() = 0;
	virtual void additionalFunc() {}
};


//ConcreteClass
class DocFileViewer : public FileViewer {
protected:
	virtual void open() {
		cout << "> 문서파일 열기" << endl;
	}
	virtual void view() {
		cout << ">> 문서파일 보기" << endl;
	}
	virtual void saveLog() {
		cout << ">>> 문서파일 열람기록 저장" << endl;
	}
	virtual void additionalFunc() {
		cout << ">>>> 문서파일 포멧변경" << endl;
	}
};

class PictureFileViewer : public FileViewer {
protected:
	virtual void open() {
		cout << "> 그림파일 열기" << endl;
	}
	virtual void view() {
		cout << ">> 그림파일 보기" << endl;
	}
	virtual void saveLog() {
		cout << ">>> 그림파일 열람기록 저장" << endl;
	}
};

int main() {
	unique_ptr<FileViewer> fileViewer(new DocFileViewer());
	fileViewer->viewFile();
	fileViewer.reset(new PictureFileViewer());

	cout << "================= change PictureViewer =============" << endl;
	fileViewer->viewFile();

	return 0;
}
```
