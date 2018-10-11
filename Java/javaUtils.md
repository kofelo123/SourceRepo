- [대문자->소문자 +문자 추가 (정규표현식 Pattern,Matcher 클래스 사용)](#8061991)
- [자바 크롤링](#8062192)
- [정리되어있는 lombok 사용법](#181006_4)

- [롬복 toString 에러 ](#181006_5)

---


###### 8061991

대문자->소문자 +문자 추가 (Pattern,Matcher사용)
-

```java
public static String convert(String input) {
		
		//1. 대문자 -> '_'+소문자
		int inputSize = input.length();
		
		for (int i = 0; i < inputSize ; i++) {
			//대문자인지 체크
			if(Character.isUpperCase(input.charAt(i))) {
				//char -> String 을 위한 valueOf  (Character.toString() 해도 되지않을까)
				String str = String.valueOf(input.charAt(i));
				input = input.replace(str , "_" + str.toLowerCase());
			}
		}

		//2. 숫자 -> '_'+숫자   자바에서는 \\두개가 \ 가 된다. \d는 정규표현식에서 digit(0~9)를 뜻한다.
		Pattern p = Pattern.compile("\\d");
		Matcher m = p.matcher(input);
		
		while(m.find())input = input.replace(m.group(0),  "_" + m.group(0));
		return input;
	}

```

Character.isDigit(word)  해서 숫자를 찾는 방법도있다.


---


###### 8062192

자바 크롤링
-

알아야할것들

<로봇배제표준>

웹사이트에 로봇이 접근하는것을 방지하는 규약

robots.txt 파일안에 기술한다.

의무는 아니고 권고안이므로 없는곳도 많다.

<크롤링,파싱 차이>

크롤링: 웹 크롤러에서 나온말로, html,문서를 수집해서 분류하고 저장한후에 쉽게 찾아볼 수 있도록 하는 열할을 하는 일종의 로봇.

데이터를 수집하고 분류

ex)약 2만평짜리 다이아몬드 광산이 있는데, 이 중에 다이아몬드가 주로 나오는 곳이 입구에서 직진해서 200m 떨어진 부분과 입구에서 오른쪽을 꺾어서 400m 떨어진 부분이라는 이런 정보가 어디에 있는지에 대한 위치를 분류하는 것이라고 보면 된다.


파싱: 어떤 페이지(문서,html등)에서 내가 원하는 데이터를 특정 패턴이나 순서로 추출하여 정보로 가공하는 것을 말한다.

ex)다이아몬드가 많이 나오는 위치로 이동을 일단 한 후에 돌을 많이 캔 다음, 다이아몬드만 쏙쏙 뽑아서 보석으로 가공하는 과정이라고 보면 된다.


//
```
<dependency>
    <groupId>org.jsoup</groupId>
    <artifactId>jsoup</artifactId>
    <version>1.11.3</version>
</dependency>
```

특정 페이징에 어떤값이 날아오는지 보기위해 개발자도구 네트워크 -> 눌러서 -> 파라미터를 봤다.
파이어폭스에서 했고, 크롬에는 잘 나오지않았다.  	

jsoup에서 document를 가져오고 패턴을 배열로 가져오는것을 elements라고 보면되고
하나하나 가져오는게 element

```java
public class MainApp {
	
	private static String URL = "http://www.jobkorea.co.kr/Search/?";
	
	public static void main(String[] args) throws IOException {
		String KEY_WORD = "jquery";
		
		System.out.println(URL + getParameter(KEY_WORD, 2));
		
		//1. Document를 가져온다.
		Document doc = Jsoup.connect(URL + getParameter(KEY_WORD, 2)).get();
		
		//2. 목록을 가져온다.
		//System.out.println("" + doc.toString());
		
		//#smGiList .list .dataList 아래 li를 모두 가져온다 (전체 doc에서 구분되는 패턴으로 필요한 정보가져오기)
		Elements elements = doc.select("#smGiList .list .detailList li");
		
		//3. 목록(배열)에서 정보를 가져온다.
		int idx = 0;
		for(Element element : elements) {
			//element.toString = html코드까지 / element.text() = 텍스트만
			System.out.println(++idx + " : " + element.toString());
			System.out.println("===========================\n\n");
		}
	}
	/**
	 * URL 완성
	 *@param KEY_WORD 
	 *@param page
	 *@return
	 */
	public static String getParameter(String KEY_WORD, int PAGE) {
	 	return "stext="+ KEY_WORD + ""
	 					+ "&IsCoInfoSC=false"
	 					+ "&IsRecruit=false"
	 					+ "&ord=1"
	 					+ "&Order=1"
	 					+ "&page="+PAGE + ""
	 					+ "&pageSize=10"
	 					+ "&pageType=HRP";					
	}
}
```

```


public class test {
	
	private static final String LOGIN_URL = 
			"http://jeongwon.me/thearc/user/loginPost";
	
	private static final String BOARD_URL = 
			"http://jeongwon.me/thearc/sboard/list/free";
	
	private static final String LIST_URL = "http://jeongwon.me/thearc/sboard/location"; 
	
	private static String ID = "admin";
	private static String PW = "admin";
	private static Map<String, String> cookies;
	
			
			
	public static void main(String[] args) throws IOException {
		//1.login
		Response loginResponse = (Response) Jsoup.connect(LOGIN_URL)
				.data("uid", ID)
				.data("upw", PW)
				.method(Method.POST)
				.execute();
		
		System.out.println(" - PAGE STATUS CODE : " + loginResponse.statusCode());
		Document doc = loginResponse.parse();
//		System.out.println("" + doc.toString());		
		
		//2. session 정보 담기.
		cookies = loginResponse.cookies();
		System.out.println(cookies);
		
		//3. 게시판 접근
		doc = Jsoup.connect(BOARD_URL)
				.cookies(cookies)
				.get();
//		System.out.println("" + doc.toString());

		
		//4. AJAX - JSON 조회
		doc = Jsoup.connect(LIST_URL)
				.cookies(cookies)
				.ignoreContentType(true) //html이 아니라서 document로 넘어오는게 아니라서 
				.get();
//		System.out.println("" + doc.toString());		

		
		
		//5. 최신 제목 출력 (등 추출할떄).
		System.out.println("" + doc.toString());
		String docu = doc.toString();
		//디아크 라는 제목앞 태그
		System.out.println("시작점 : " + docu.indexOf("firstHeading\">"));
		System.out.println("종료점 : " + docu.indexOf("크</h2>"));
		int startIdx = docu.indexOf("firstHeading\">");
		int endIdx = docu.indexOf("크</h2>");
		//출력 디아크
		System.out.println("" + docu.substring(startIdx +14 , endIdx +1));

	}
}


```


-----------------------------------------

###### 181006_4

정리되어있는 lombok 사용법
-

```java


//lombok 사용법
@Data
@Getter @Setter
@ToString
public class TestDto {
    private String id;
    private String name;
}
  
@Slf4j
@log4j
public class TestController {
    public String test() {
        log.info("긋");
    }
}
  
@Data = getter, setter, tostring, hashcode 등등을 자동으로 만들어준다.
@Getter = getter를 자동으로 만들어준다.
@Setter = setter를 자동으로 만들어준다.
@ToString = toStirng을 자동으로 만들어준다.
이클립스의 outline에 보면 메소드들이 만들어진걸 확인 할수 있다.
  
  
@Slf4j = logback을 사용할 경우 logback은 slf4j를 기반으로 로그를 사용한다.
@log4j = lo4j를 로그로 사용할경우
  
  
lombok을 사용 하지 않을 경우 아래와 같이 logger클래스를 만들어서 사용을 하였을것이다.
private final Logger logger = Logger.getLogger(this.getClass());
  
  
하지만 lombok을 사용하면
@Slf4j 어노테이션 하나로 인해 간편하게 로거를 사용할수 있다.
  
  
ex)
@Slf4j
class AbcController {
  public tmpMethod() {
     log.info("so good!!")
  }
}
 
@EqualsAndHashCode
이퀄과 해시코드를 만들어줍니다.
@NoArgsConstructor
매개변수가 없는 생성자를 만들어줍니다.
@AllArgsConstructor
모든필드가 매개변수에 들어간 생성자를 만들어줍니다.
@Builder
클래스를 빌더패턴으로만들수 있게 해줍니다.

```
출처: http://pstree.tistory.com/122 [Hello World!! PsTree]




-----------------------------------------

###### 181006_5

롬복 toString 에러 
-

커스텀이 getter등이 있을때는 

롬복의 toString을 사용하면 에러가났다.

405 같은 에러가 뜨고 메시지도 딱히 안떠서 곤혹스러웠다.

