# Week 7 / 어댑터 패턴 (adapter pattern)

## 어댑터 패턴
- 정의 : 특정 인터페이스를 지원하지 않는 대상 객체를 인터페이스를 지원하는 Adapter에 집어넣어서 사용하는 방법
- 클래스의 인터페이스를 사용자가 기대하는 인터페이스 형태로 적응(변환)
- 서로 일치하지 않는 인터페이스를 갖는 클래스들을 함께 동작

![01](https://github.com/canyuo/canyuo.github.io/blob/main/week7_image1.png)
- Target : 변화에 대한 요구사항
- Adaptee : 기존의 코드, Adaptee 가 가지고 있는 기능을 Adapter 가 주입받아 operation() 을 구현
- Adapter : 변화에 대한 요구사항을 구현한 새로운 코드
- Client : 인터페이스인 Target 을 통해 이를 사용

```cpp

class KakaoAccount {
public:
	KakaoAccount(string newId, string newPassword, string newName, string newEmail)
		: id(newId)
		, password(newPassword)
		, name(newName)
		, email(newEmail)
	{
	}

	string getAuthToken() {
		cout << "카카오 로그인 성공" << endl;
		return id + KAKAO_SECRET + password;
	}

	string& getId(){	return id;	}
	string& getName(){	return name;	}
	string& getEmail(){	return email;	}
	const string& getSecret(){	return KAKAO_SECRET;	}

private:
	string id;
	string password;
	string name;
	string email;
	const string KAKAO_SECRET = "KA_SECRET";
};

class InflearnAccount {
public:
	InflearnAccount(string newEmail,string newPassword,string newUsername)
		: email(newEmail)
		, password(newPassword)
		, username(newUsername)
	{
	}

	string login() {
		cout << "인프런 로그인 성공" << endl;
		return email + INFLEARN_SECRET + password;
	}
	string& getUsername(){	return username;	}
	const string& getSecret() { return INFLEARN_SECRET; }

private:
	string email;
	string password;
	string username;
	const string INFLEARN_SECRET = "INF_SECRET";
};

//target
class SocialNetworkAuthTarget {
public:
	virtual string getServiceName() = 0;
	virtual string getUserName() = 0;
	virtual string getSecret() = 0;
	virtual string getToken() = 0;
};

// Adapter
class InflearnSocialNetworkAuthAdapter : public SocialNetworkAuthTarget {
public:
	InflearnSocialNetworkAuthAdapter(InflearnAccount* newInflearnAccount)
	: inflearnAccount(newInflearnAccount)
	{
	}

	virtual string getServiceName() override{
		return "INFLEARN";
	}
	
	virtual string getUserName() override {
		return inflearnAccount->getUsername();
	}
	
	virtual string getSecret() override {
		return inflearnAccount->getSecret();
	}
	
	virtual string getToken() override {
		return inflearnAccount->login();
	}

private:
	unique_ptr<InflearnAccount> inflearnAccount;
};

class KakaoSocialNetworkAuthAdapter : public SocialNetworkAuthTarget {
public:
	KakaoSocialNetworkAuthAdapter(KakaoAccount* newKakaoAccount) 
	: kakaoAccount(newKakaoAccount)
	{
	}

	virtual string getServiceName() override{
		return "KAKAO";
	}
	
	virtual string getUserName() override {
		return kakaoAccount->getName();
	}
	
	virtual string getSecret() override {
		return kakaoAccount->getSecret();
	}
	
	virtual string getToken() override {
		return kakaoAccount->getAuthToken();
	}

private:
	unique_ptr<KakaoAccount> kakaoAccount;
};

class SocialNetworkAuthService {
public:
	static void socialLogin(SocialNetworkAuthTarget* socialNetworkAuthTarget) {
		cout << "소셜 로그인을 시작합니다." << endl;
		cout << "이용하는 서비스: " + socialNetworkAuthTarget->getServiceName() << endl;
		cout << "이름: " + socialNetworkAuthTarget->getUserName() << endl;
		cout << "토큰: " + socialNetworkAuthTarget->getToken() << endl << endl;
	}
};

static SocialNetworkAuthTarget* getKakaoTarget() {
	return new KakaoSocialNetworkAuthAdapter(new KakaoAccount("kakaoman", "kakaopassword", "kakaoman@kakao.com", "카카오한수"));
}

static SocialNetworkAuthTarget* getInflearnTarget() {
	return new InflearnSocialNetworkAuthAdapter(new InflearnAccount("me@naver.com", "mypassword", "인프런한수"));
}

int main()
{
	unique_ptr<SocialNetworkAuthService> socialNetworkAuthService;
	unique_ptr<SocialNetworkAuthTarget> kakaoAuth(getKakaoTarget());
	unique_ptr<SocialNetworkAuthTarget> inflearnAuth(getInflearnTarget());

	socialNetworkAuthService->socialLogin(kakaoAuth.get());
	socialNetworkAuthService->socialLogin(inflearnAuth.get());

	return 0;
}
```
