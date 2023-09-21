# Week 14 / 인터프리터 패턴 (Interpreter Pattern)

## 인터프리터 패턴 (Interpreter Pattern)
- 정의
   - 자주 등장하는 문제를 간단한 언어로 정의하고 재사용하는 패턴
   - 자주 사용되는 특정한 문법적 규칙이 존재한다면, 이를 일련의 규칙을 통해 언어/문법으로 규격화하여 해석하는 목적을 가지는 패턴( ex. 정규표현식, SQL을 파싱, 기호를 처리 )


- 장점
   - 명확한 관심사 분리 : 문법과 해석을 기본 로직에서 분리하여 별도의 클래스로 캡슐화 되므로 모듈화되어 유지보수하기 쉬움
   - 쉬운 확장 : Expression 클래스에서 파생된 새로운 구현 클래스만 추가하면 문법을 쉽게 확장 가능
   - 가독성 : 문법과 규칙을 계층구조로 명시적으로 모델링하기 때문에 코드의 가독성이 높아짐, 문법과 요소의 관계를 이해하는데 도움됨
 
   
- 단점
   - 복잡성 : 복잡한 문법의 경우 많은 별도의 클래스가 생기므로 관리 및 이해가 어려워 질 수 있음
   - 성능 : 종종 재귀와 객체 생성을 포함하므로 규모가 크거나 복잡한 표현식의 경우 오버헤드가 발생
   - 제한된 적용 가능성 : 문법이 너무 자주 진화하는 경우엔 유지 관리하는데에 비용이 더 들 수 있음, 주로 간단하고 잘 정의된 문법에 효과적
   - 불완전한 솔루션 : 인터프리터 패턴만으로는 렉싱, 에러 핸들링, 최적화와 같은 케이스의 측면을 처리하기 어려울 수 있음


![01](https://github.com/canyuo/canyuo.github.io/blob/main/week14_image1.png)


```cpp

class Expression {
public:
	virtual ~Expression() {}
	virtual double evaluate() = 0;
};

class NumberExpression :public Expression {
public:
	NumberExpression(double value) : value_(value) {}
	virtual double evaluate() { return value_; }

private:
	double value_;
};

class AdditionExpression :public Expression {
public:
	AdditionExpression(Expression* left, Expression* right)
		: left_(left), right_(right) {}

	virtual double evaluate() {
		double left = left_->evaluate();
		double right = right_->evaluate();

		return left + right;
	}

private:
	shared_ptr<Expression> left_;
	shared_ptr<Expression> right_;
};

class SubtractExpression :public Expression {
public:
	SubtractExpression(Expression* left, Expression* right)
		: left_(left), right_(right) {}

	virtual double evaluate() {
		double left = left_->evaluate();
		double right = right_->evaluate();

		return left - right;
	}

private:
	shared_ptr<Expression> left_;
	shared_ptr<Expression> right_;
};

class ExpressionContext 
{
public:
	ExpressionContext(string expression)
	{
		char* str1 = NULL;
		char* str2 = NULL;
		bool isPlus = false;
		bool isMinus = false;
		if (expression.find('+') > 0)
		{
			isPlus = true;
		}
		else if (expression.find('-') > 0)
		{
			isMinus = true;
		}

		str1 = strtok_s(expression.data(), "+-", &str2);
		int a = atoi(str1);
		int b = atoi(str2);
		if (nullptr != str2)
		{
			if (isPlus)
			{
				expression_ = unique_ptr< Expression>(new AdditionExpression(new NumberExpression(a), new NumberExpression(b)));
			}
			else if (isMinus)
			{
				expression_ = unique_ptr< Expression>(new SubtractExpression(new NumberExpression(a), new NumberExpression(b)));
			}
			else
			{
				cout << "Error!!! operator!!" << endl;
			}
		}
		else
		{
			expression_ = unique_ptr<Expression>(new NumberExpression(a));
		}
	}

	void run()
	{
		cout << expression_->evaluate() << endl;
	}

private:
	unique_ptr<Expression> expression_;
};

int main() {
	ExpressionContext expressionPlus("5+2");
	ExpressionContext expressionMinus("5-2");
	ExpressionContext expressionNum("5");

	expressionPlus.run();
	expressionMinus.run();
	expressionNum.run();
	return 0;
}
```
