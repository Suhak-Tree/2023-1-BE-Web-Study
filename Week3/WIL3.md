WIL3 - 내용 정리

회원관리 예제 - 백엔드 개발 순서 : 비즈니스 요구사항 정리 -> 회원 도메인과 리포지토리 만들기 -> 회원 리포지토리 테스트 케이스 작성 -> 회원 서비스 개발 -> 회원 서비스 테스트. : junit이라는 테스트 framework 활용

일반적인 웹 애플리케이션 계층 구조 컨트롤러 -> 서비스 -> 리포지토리 -> DB
-> 도메인 -> 도메인 ->도메인
컨트롤러 : 웹 MVC의 컨트롤러 역할
서비스 : 핵심 비즈니스 로직 구현. 비즈니스 도메인 객체를 가지고 핵심 비즈니스 로직을 구현한 객체. ex) 회원중복가입X
리포지토리 : 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
도메인 : 비즈니스 도메인 객체 ex) 회원, 주문, 쿠폰 등 주로 DB에 저장하고 관리됨

의존 관계 : MemberSercice -> MemberRepository(interface) <- MemoryMemberRepository

1. 회원 도메인과 리포지토리 만들기 -회원 객체. src -> main -> java -> hello.spring -> package 만들기 "hello.hellospring.domain" -> package에 Member라는 클래스 만들기 -> public class Member { } 로 구현. iD를 private에 Long 으로, name은 string으로. 그 외에는 getter and setter 등록

2. 회원 리포지토리 인터페이스 만들기
   -hello.spring 내에 .repository package 만들기(회원 객체 저장하는 저장소)
   <기능>
   Member save(Member member); -> 회원을 저장하고 저장된 회원을 반환
   Optional <Member> findByID(Long id); -> Optional은 Java8의 기능, return 값이 null일 경우 optional로 감싸서 반환함 ID로 회원찾기
   Optional <Member> findByName(String name); -> 이름으로 회원찾기
   List<Member> findAll(); -> 모든 회원 List 반환

3. 회원 리포지토리 메모리 구현체 -> repository에 class 만듦(MemoryMemberRepository)

public class MemoryMemberRepository implements MemberRepository -> Member Repository interface를 implement 함 (MemberRepository에서 옵션 엔터 -> 전부다 선택)
private static Map<Long, Member> store = new HashMap<>(); - Map에서 cral + space로 import 해줘야함. store = newHashMap<>();은 실무에서는 동시성 문제가 있어서X

@Override public Member save(Member member) { member.setId(++sequence); // ID 세팅 store.put(member.getId(), member); // store에다 넣기 전에 Member에 ID값 세팅 후 store에 저장 return member; }

@Override public Optional<Member> findById(Long id) { // store에서 꺼내면 됨 return Optional.ofNullable(store.get(id)); } // null이어도 감쌀 수 있음 -> client에서 무언가를 할 수 있음
findAll()에서 store.values() 의 value 값들은 member들이다.

@Override public Optional<Member> findByName(String name) { return store.values().stream() // loop로 돌림 .filter(member -> member.getName().equals(name)) // parameter로 넘어온 name과 동일한지 비교하고 같은지 필터링. 찾으면 반환함 .findAny(); } // 하나로도 찾기 가능

public void clearStore() { store.clear() }} // store를 싹 비우는 역할. test할 때 After Each를 위해 구현해둠.

=> 구현이 끝남 -> 검증이 필요 => 테스트 케이스 작성

\*회원 리포지토리 테스트 케이스 작성. Java는 Junit이라는 프레임워크로 테스트를 실행해서 문제점들을 해결 O

4. 회원 리포지토리 메모리 구현체 테스트 - src -> test -> java -> hello.spring에서 .repository 폴더를 만들고 MemoryMembetRepositorytest로 클래스를 만듦

-class를 굳이 public으로 안해도 됨.

MemoryMemberRepository repository = new MemoryMemberRepository(); 새로운 MemoryMemberRepository가 생기면 MemoryMemberRepository 객체인 repository에 저장됨

@AfterEach
public void afterEach() { repository.clearStore(); } // methor가 실행될 때 마다 실행이 됨. 즉, 끝날 때 마다 메모리 DB에 저장된 데이터를 삭제한다.

@Test -> org. junit, jupiter, api 클릭 -> import 해주면 실행이 가능해짐 public void save() { //given Member member = new Member(); member.setName("spring"); // cmd + shift + Enter or ^ + shift + Enter //when repository.save(member); //then Member result = repository.findById(member.getId()).get(); // 검증하는 단계, .get으로 optional에서 꺼냄. assertThat(result).isEqualTo(member);} // 만약에 같으면 참= Assertion.assertEquals(member, actual:null); = Assertions.assertThat(member).isEqualTo(result); -> Assertions를 static 시킬 수 있음.

_꿀팁 : 같은 객체 이름만 다르게 하려면 shift F6를 통해서 rename이 가능_

-findAll -> List<Member> result = repository.findAll();
assertThat(result.size()).isEqualTo(2);

_중요!_ : 테스트틑 각각 독립적으로 실행되어야한다. 테스트 순서에 의존관계가 있는 것은 좋은 테스트가 아니다.
_테스트 주도 개발(TDD) : 테스트 케이스부터. 틀을 먼저 만들고 나서 그 후에 구현해 나가는 방식. 순서를 뒤집음_

5. 회원 서비스 개발 - 회원 리포지토리와 도메인을 이용해서 실제 비즈니스 로직을 작성함.
   hello.spring -service 폴더. (hello.hellospring 폴더에서 우클릭 RunTestin"hello.hellospring" 클릭하면 test를 자동으로 돌려줌)

private final MemberRepository memberRepository = new MemoryMemberRepository(); // MemberRepository가 필요해서 선언

public Long join(Member member) { // 회원가입 서비스 validateDuplicateMember(member); //중복 회원 검증 memberRepository.save(member); return member.getId(); }
private void validateDuplicateMember(Member member) { memberRepository.findByName(member.getName()) //Optional<Member> reuslt 를 생략. .ifPresent(m -> { //result를 생략 throw new IllegalStateException("이미 존재하는 회원입니다."); }); } // ctrl + T -> 9.Extra Method => 이름 설정(validateDuplicateMember)

-전체 회원 조회
public List<Member> findMembers() { return memberRepository.findAll(); } // 멤버 객체의 리스트로 해야 조회 가능

public Optional<Member> findOne(Long memberId) { return memberRepository.findById(memberId);} // memberID라는 변수를 입력 받으면 findById 함수에 인자로 넣어 실행시킨 값을 반환

6. 회원 서비스 테스트

기존에는 회원 서비스가 메모리 회원 리포지토리를 직접 생성하게 햇지만 현재는 cmd shift T or ^ + ^| + ^ -> create test -> junit 5 -> 밑에 리스트들 체크 하면 껍데기를 자동으로 만들어둠

Member 서비스 test case와 MemoryMemberRepository는 서로 다른 repository이다. static으로 선언된 경우에는 괜찮지만, 내용물이 달라질 수 있기 때문에 test 실행마다 각각 생성할 수 있도록 @BeforeEach를 선언한다. 내가 직접 new를 통해서 넣는 것이 아닌 외부에서 넣어주는 개념이다. = DI, @BeforeEach : 각 테스트 실행 전에 호출된다. 테스트가 서로 영향이 없도록 항상 새로운 객체를 생성하고, 의존관계도 새로 맺어준다.

MemoryMemberRepository memberRepository;
@BeforeEach public void beforeEach() { memberRepository = new MemoryMemberRepository(); memberService = new MemberService(memberRepository); }

public class MemberService { private final MemberRepository memberRepository; public MemberService(MemberRepository memberRepository) { this.memberRepository = memberRepository; } ... }

_꿀팁 : test는 한글로 작성해도 상관 없음, 실제 코드에 포함이 안되기 때문이다. 오히려 한국계에서는 그 의미를 명확하게 알 수 있어서 좋다_
_꿀팁 : 대부분의 테스트 케이스는 Given, When, Then으로 나뉘어진다. Given은 이런 사랑히 주어졌다는 것이고, When은 이것을 실행했을 때. Then은 실행한 결과가 이게 나와야한다는 것을 검증하는 형태로 이루어진다. 테스트에서는 예외 flow가 굉장히 중요하다_

@Test public void 중복*회원*예외() throws Exception { //Given Member member1 = new Member(); member1.setName("spring"); Member member2 = new Member(); member2.setName("spring");
//When memberService.join(member1); IllegalStateException e = assertThrows(IllegalStateException.class, //e를 검증. IllegalStateException 대신 NullPointException은 안됨. () -> memberService.join(member2));//예외가 발생해야 한다. 람다가 넘어가야함 assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다."); }} // 메시지가 똑같은지를 검증
