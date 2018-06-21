- [대문자->소문자 +문자 추가 (정규표현식 Pattern,Matcher 클래스 사용)](#8061991)
- [자바 크롤링](#8062192)

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

