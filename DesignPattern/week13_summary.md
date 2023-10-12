# Week 13 / 빌더 패턴(Builder pattern)

## 빌더 패턴(Builder pattern)
- 정의
  - 복잡한 객체를 생성하는 방법을 정의하는 클래스와 표현하는 방법을 정의하는 클래스를 별도로 분리하여, 동일한 프로세스를 거쳐 다양한 구성의 인스턴스를 만드는 방법
 - 싱글톤 패턴, 팩토리 패턴, 추상 팩토리 패턴과 마찬가지로 생성 패턴(인스턴스를 만드는 절차를 추상화하는 패턴)
  
![01](https://github.com/canyuo/canyuo.github.io/blob/main/week13_image1.png)

```cpp
class TranslationBook
{
protected:
	string contents;
public:
	string GetContents() { return contents; }
	void AddContents(string newContents) { contents += newContents; }
	void showContents()
	{
		cout << contents << endl;
	}
};

//Abstract Class
class Translator
{
protected:
	TranslationBook result;
public:
	TranslationBook& GetResult() { return result; }
	virtual void TransSentence(const string& sentence) = 0;
};

class EnglishTranslator : public Translator
{
public:
	void TransSentence(const string& sentence)
	{
		string output("영어번역[ ");
		output.append(sentence);
		output.append(" ] ");
		result.AddContents(output);
	}
};

class JapaneseTranslator : public Translator
{
public:
	void TransSentence(const string& sentence)
	{
		string output("일본어번역[ ");
		output.append(sentence);
		output.append(" ] ");
		result.AddContents(output);
	}
};

#include <vector>
#include <sstream>

vector<string> split(const string& input, char delimiter);

class Director
{
public:
	void DoTranslate(const string& sentence, Translator& translator)
	{
		vector<string> result = split(sentence, ' ');
		for (const string& sp : result)
		{
			translator.TransSentence(sp);
		}
	}
};

vector<string> split(const string& input, char delimiter)
{
	vector<string> answer;
	stringstream ss(input);
	string temp;

	while (getline(ss, temp, delimiter)) {
		answer.push_back(temp);
	}

	return answer;
}

int main()
{
	string sentence = "이것은 번역해야 할 내용입니다!";
	Director director;
	EnglishTranslator et;
	director.DoTranslate(sentence, et);
	TranslationBook& English = et.GetResult();
	English.showContents();

	JapaneseTranslator jt;
	director.DoTranslate(sentence, jt);
	TranslationBook& Japanese = jt.GetResult();
	Japanese.showContents();

	return 0;
}
```
