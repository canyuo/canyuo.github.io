# Week 7 / 어댑터 패턴 (adapter pattern)

## 어댑터 패턴
- 정의 : 특정 인터페이스를 지원하지 않는 대상 객체를 인터페이스를 지원하는 Adapter에 집어넣어서 사용하는 방법
- 클래스의 인터페이스를 사용자가 기대하는 인터페이스 형태로 적응(변환)
- 서로 일치하지 않는 인터페이스를 갖는 클래스들을 함께 동작

- 이미 존재하는 클래스의 인터페이스를 변경하고자 할때
- 호환되지 않는 인터페이스를 가진 두 클래스를 함께 사용해야할 때
- 
![01](https://github.com/canyuo/canyuo.github.io/blob/main/DesignPattern/week7_image1.png)
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

private:
	string id;
	string password;
	string name;
	string email;
public:
	static const string KAKAO_SECRET;
};

class GoogleAccount {
public:
	GoogleAccount(string newEmail,string newPassword,string newUsername)
		: email(newEmail)
		, password(newPassword)
		, username(newUsername)
	{
	}

	string login() {
		cout << "구글 로그인 성공" << endl;
		return email + GOOGLE_SECRET + password;
	}
	string& getUsername(){	return username;	}

private:
	string email;
	string password;
	string username;
public:
	static const string GOOGLE_SECRET;
};

//target
class SocialNetworkAuthTarget {
public:
	virtual string getServiceName() = 0;
	virtual string getUserName() = 0;
	virtual string getSecret() = 0;
	virtual string getToken() = 0;
};

const string KakaoAccount::KAKAO_SECRET = "KA_SECRET";
const string GoogleAccount::GOOGLE_SECRET = "GG_SECRET";

// Adapter
class GoogleSocialNetworkAuthAdapter : public SocialNetworkAuthTarget {
public:
	GoogleSocialNetworkAuthAdapter(GoogleAccount* newGoogleAccount)
	: googleAccount(newGoogleAccount)
	{
	}

	virtual string getServiceName() override{
		return "GOOGLE";
	}
	
	virtual string getUserName() override {
		return googleAccount->getUsername();
	}
	
	virtual string getSecret() override {
		return GoogleAccount::GOOGLE_SECRET;
	}
	
	virtual string getToken() override {
		return googleAccount->login();
	}

private:
	unique_ptr<GoogleAccount> googleAccount;
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
		return KakaoAccount::KAKAO_SECRET;
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
	return new KakaoSocialNetworkAuthAdapter(new KakaoAccount("kakaoman", "kakaopassword", "kakaoman@kakao.com", "카카오 한수"));
}

static SocialNetworkAuthTarget* getGoogleTarget() {
	return new GoogleSocialNetworkAuthAdapter(new GoogleAccount("me@naver.com", "mypassword", "구글 한수"));
}

int main()
{
	unique_ptr<SocialNetworkAuthService> socialNetworkAuthService;
	unique_ptr<SocialNetworkAuthTarget> kakaoAuth(getKakaoTarget());
	unique_ptr<SocialNetworkAuthTarget> googleAuth(getGoogleTarget());

	socialNetworkAuthService->socialLogin(kakaoAuth.get());
	socialNetworkAuthService->socialLogin(googleAuth.get());

	return 0;
}
```
