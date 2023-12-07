# 데이터 베이스 응용을 설계할 때 세가지 목표
1. 보안성(secrecy): 권한이 없는 사용자에게 정보가 노출되어서는 안된다.
2. 무결성(integrity): 권한이 있는 사용자만이 데이터를 수정할 수 있어야 한다.
3. 가용성(availability): 권한이 있는 사용자의 접근이 거부되어서는 안 된다.

이러한 목표를 달성하기 위해서는 보안 정책(security policy)를 달성해야 하며, 
이를 위해 여러 레벨에서 보안 조치를 해야 한다.
예를 들어 운영체제, dbms등 각단계에서 보안 조치를 해야 한다.

데이터베이스 접근 제어 메커니즘에 대해 알아보자

이에 대해서는 뷰(view)가 존재한다. 이는 데이터 자체가 아니라 제한된 버전을 접근하게 함으로써 데이터에 대한 접근을 제한한다.

# 접근 제어
모든 사용자가 데이터베이스 전부에 접근할 수 있는 것은 바람직하지 않다.
따라서 DBMS는 데이터 접근을 제어하는 메커니즘을 제공하여야 한다.
DBMS는 크게 두 가지 방법을 제공한다
* 임의적 접근제어(discretionary access control)
* 강제적 접근제어(mandatory access control)

## 임의적 접근 제어
임의적 접근 제어는 접근 권한, 권한 개념을 사용하여, 사용자에게 권한을 부여하는 방법이다.
여기서 권한이란 어떤 데이터베이스에 읽기, 쓰기 방법등으로 접근하도록 허락하는 것이다.
뷰, 객체를 만든 사용자는 자동적으로 모든 권한을 얻게 된다.
이 후 DBMS는 이들 권한이 어떤 사용자에게 허가(grant), 취소(revoke)되었는 지 추적하여, 권한에 맞지 않는 사용자는 접근하지 못하도록 한다.

SQL은 GRANT와 REVOKE를 통한 임의적 접근 제어를 지원한다.

다만 임의적 접근 제어는 약점을 갖고 있다.
권한이 없는 불순한 사용자가, 권한이 있는 사용자로 하여금 데이터를 유출시키게 할 수 있다는 것이다.

이로 인하여 경우에 따라 강제적 접근 제어를 사용하기도 한다.
### 임의적 접근제어 sql
sql에서는 grant와 revoke 명령을 통한 임의적 접근 제어를 지원한다.
grant는 객체(view or table)에 대한 권한을 부여한다.

```mySQL
GRANT privileges ON object TO users [WITH GRANT OPTION]
```

여기서 object에 대해 privileges에 다음과 같은 권한들을 명세할 수 있다.
* SELECT: 읽기 권한
* INSERT: 삽입 권한
* UPDATE: 변경 권한
* DELETE: 삭제권한
* REFERENCES: 참조 외래키를 정의할 수 있는 권한, 단순히 REFERENCES라고 표기하면 추후 추가되는 칼럼을 포함한 모든 칼럼들에 대한 이 권한을 부여한다.
object에는 적용할 테이블이나 뷰 이름을 명시하면 된다.
users에는 권한을 줄 users를 명시하면 된다.
WITH GRANT OPTION은 선택적인 명시인데, 이를 명시하면 똑같은 권한을 GRANT를 통해 다른 사람들에게 전파할 수 있는 권한을 갖는다.
여기서 UPDATE에 대해 구체화하려면 UPDATE (rating) 등과 같이 구체화한다.

참고로 테이블에 대해 select 권한이 있어야만 뷰를 생성할 수 있고,
이때 생성된 뷰에 대해서는 테이블에 대해 갖고 있는 권한 그대로 부여받는다.

테이블에 대한 DROP, CREATE, ALTER에 대한 권한은 오직 스키마 소유주만 갖고, 누구에게도 권한을 허가, 회수할 수 없다.

\# 여기서 REFERENCES는 어떻게 사용할까?
![[Pasted image 20231207145253.png]]
이렇게 bid의 외래키에 대한 권한을 부여받으면
![[Pasted image 20231207145329.png]]
이와 같이 bid를 외래키로 참조할 수 있다. 만약 외래키 권한이 없으면 불가능하다.

\# 참고로 뷰는 어떻게 생성할까?
![[Pasted image 20231207144122.png]]
이는 테이블 생성과 비슷하게 생성하지만 단지 VIEW를 붙여주는 것 뿐이다.

sql에서 권한은 권한 식별자(authorization ID)에게 부여되는데, 즉
`GRANT privileges ON object TO users [WITH GRANT OPTION]`
에서 TO users에 users가 권한 식별자이다.

사용자는 DBMS에서 명령을 수행하려면 권한 식별자와 암호를 입력해야만 한다.

만약 특정 유저에게 SELECT 권한을 주지 않고 UPDATE 권한만 주게 된다면
그 사용자는
`UPDATE Sailors S SET S.rating = S.rating -1` 과 같은 명령을 실행할 수 업다.
왜냐하면 S.rating에 대한 SELECT권한이 필요한데 없기 때문이다. 
따라서 테이블을 만들고, 수정하고, 삽입하는 유저는 반드시 SELECT 권한도 있어야 한다.


기존의 권한을 회수하는 명령은 다음과 같이 명시한다.
```mySQL
REVOKE [GRANT OPTION FOR] privileges ON object FROM users {RESTRICT | CASCADE}
```
이 명령을 통해 특정 권한을 회수하거나, 특정 권한의 옵션만 회수하는데 사용할 수 있다.
여기서 특정 권한의 옵션만 회수하는 것은 GRANT OPTION FOR을 사용한다.
RESTRICT와 CASCADE중 하나는 반드시 명시해야 한다.

여기서 CASCADE 키워드는 회수되는 User가  GRANT 명령어를 통해 권한을 준 모든 사용자들도 포함하여 권한을 철회한다.
다만 같은 권한을 다른 GRANT 명령을 통해 받았을 경우는 회수하지 않는다.
RESTRICT키워드는 회수되는 User가 GRANT 명령어를 통해 다른 user에게 권한을 주지 않았을 경우에만 REVOKE가 실행되고, 아니면 실행이 거부된다.


GRANT 명령이 실행되면  DBMS가 관리하는 권한 정보 테이블에 권한 설명자(privilege descriptor)가 추가 된다. 여기에는 권한의 허가자(grantor), 권한을 받는 피허가자(grantee), 허가권한(객체), 허가옵션 등이 명시된다.


![[Pasted image 20231207145651.png]]
![[Pasted image 20231207145700.png]]
위 두 명령어는 분명히 다르다. 만약 아래와 같은 권한은 Sailor에 새로운 필드가 생긴다면, Michael은 새로운 칼럼에 대한 INSERT 권한을 갖지 못한다.

![[Pasted image 20231207150725.png]]
위와 같이 실수로 똑같은 권한을 두번 주고, 한번 취소하면 어떻게 될까? 같은 권한을 두번주면 한버의 REVOKE 명령으로 권한 회수 가능하다.

참고로 view를 external schema라고 하고 
DB scherma를 logical schema라고 한다.
view를 모아 DB schema를 만들면, 이는 실체가 존재하는 테이블이고 이를 DB에(physical schema)에 저장할 수 있다.

## 강제적 접근제어(mandatory access control)
임의적 접근 제어 메커니즘은 권한이 없는 불순한 사용자가 권한이 있는 사용자로 하여금 민감한 데이터를 유출시키도록 속이는 트로이 목마 방식에 취약하다. 

예를 들어 Justin의 학점 테이블을 훔쳐보고 싶은 A는 다음과 같은 방법을 사용할 수 있다.
* mine이라는 새로운 테이블을 만들어서 Justin에게 쓰기권한을 부여한다.
* justin이 자주 사용하는 DBMS 응용 프로그램에 Grades라는 테이블을 읽어 mine에 쓰도록 시킨다.

이는 데이터베이스 객체에 대해 보안등급(security class)를 부여하고,
사용자들에게 허가등급(clearance)를 부여해서,
이를 이용해 사용자의 데이터베이스 읽기와 쓰기를 제안한다. 

SQL에서는 강제적 접근제어를 지원하고 있지 않다.

### 강제적 접근제어 모델 Bell-LaPadula 
이는 객체(테이블,뷰,튜플,필드), 주체(사용자), 보안등급(security class), 허가등급(clearance)로 기술된다.
객체에 대하여 보안 등급이 지정되고
주체에 대해 허가등급이 지정된다.

여기서 보안, 허가등급에는 4가지가 존재한다.
* Top secret(TS)
* Secret(S)
* Confidentail(C)
* Unclassified(U)
TS > S > C > U 순으로 TS가 가장 보안등급이 높다.

Bell-LaPadula에서는 다음과 같은 두 가지 제약을 가진다
* 단순 보안성질(simple Security Property): 이는 class(S) >= class(O)인 경우에만 주체 S가 객체O를 읽을 수 있다.
* \*-성질: class(S) <= class(O)인 경우에만 주체 S가 객체 O에 쓸 수 있다.
* 왜 낮은 객체에 대해서 높은 주체가 쓸수 없는 걸까? 이는 기밀정보가 낮은 객체로 세어 나가는 것을 방지하기 위해서이다.

#### 다단계 테이블(Multilevel Table)
![[Pasted image 20231207154510.png]]
다음과 같이 테이블에 보안등급이 명시되어 있는 것이다.

여기서 만약 c등급이 <101, a, b, c>을 테이블에 넣을려고 한다면 
접근을 거절하거나 접근을 허락하는 두가지 경우가 있는데
만약 접근을 거절하면 101에 해당하는 객체의 보안등급이 C보다 높다는 것을 알게 되고
접근을 허락하면 키가 중복되어 버린다.

따라서 Security Class 또한 partial key로 두어 문제를 해결한다.


# Security to the Level of a Field
만약 테이블의 한 속성에 대해서만 읽을 수 있는 뷰만 제공한다면,
이는 아무것도 못해 보안적으로는 좋다
하지만 너무 많은 뷰가 생성되어야 하므로 현실적이지 않다.
# Internet-Oriented Security
만약 인터넷을 통하여 정보를 주고 받는다면 이에 대한 신뢰가 필요하다.
이를 위해 Encryption을 이용한다.
이에 대해
* Symmetric Encryption
* Public Key Encryption
이 존재한다.
대칭 키 알고리즘은 key 하나로 데이터를 암호화하고 해독한다.
이를 이용하여 인터넷 http 프로토콜 보안을 이룬다.
SSL, SET
## SSL은 다음과 같이 동작한다.
클라이언트가 Amazon에 대한 public key가 진짜인지 어떻게 알까?
이에 대해 아마존이 대행 업체에 인증서를 발행한다. 
이 인증서에는 \< 인증센터, Amazon, public key>가 있다.
이 인증서는 암호화된 상태로 존재하며 이는 인증센터의 private로 암호화한다.
클라이언트는 인증센터에 대한 public key를 보관하고 있고, Amazon에 대한 인증서를 요구하여 이를 받아 public key로 확인한다.
이 안에서 Amazon의 public key를 가져온다.

만약 클라이언트가 order를 전송하려한다면?
클라이언트가 자신의 private key로 order를 암호화하고, 이를 Amazon의 public key로 암호화한다.
이를 받은 Amazon은 Amazon의  private key로 해독하고 클라이언트의 public key로 해독해 내용을 읽는다.

## Statistical DB Security
이는 개개인의 정보는 있는 데 이에 대해 오직 aggregate 질의만 던질 수 있게 하는 것이다.
이로 인해 어느정도의 보안을 달성할 수 있다(개개인의 정확한 정보는 알 수없다)
다만 이는 50보다 나이 많은 사람? 등으로 추론은 가능하다.

