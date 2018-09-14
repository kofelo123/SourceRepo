- [이미지 축소를 위한 imgScalr](#imgscalr)
- [스프링 업로드 처리를 위한 multipartResolver](#multipartresolver)
- [MultipartFile](#multipartfile)

### imgScalr

> 이미지를 꺠끗하게 축소하는 라이브러리다
```
<dependency>
			<groupId>org.imgscalr</groupId>
			<artifactId>imgscalr-lib</artifactId>
			<version>4.2</version>
		</dependency>
```
---

### multipartResolver

> 스프링 MVC 에서 파일 업로드를 처리하기 위해서 파일 업로드로 들어오는 데이터를 처리하는 객체가 필요하다.
> 웹 과 관련 있기 때문에, root-context.xml이 아닌 servlet-context.xml을 이용해서 설정한다.

```xml
<beans:bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">

<beans:property name="maxUploadSize" value="10485760"></beans:property>

</beans:bean>

```
---

MultipartFile은 post방식으로 들어온 데이터를 의미한다.
이 객체를 사용해서 전송된 파일의 이름이나, 파일의 크기, 파일의  MIME 타입 등과 같은 정보들을 추출할 수 있다.

form에 enctype="multipart/form-data"
로 구성된 데이터의 처리는 프로젝트에 적용한 multipartResolver 설정을 통해 처리된다.


```java

@Resource(name = "uploadPath")
private String uploadPath;

@RequestMapping(value = "/uploadForm", method = RequestMethod.POST)
public void uploadForm(MultipartFile file, Model model)throws Exception{
  logger.info("originalName: " + file.getOriginalFilename());
  logger.info("size: " + file.getSize());
  logger.info("contentType: " + file.getContentType());

  String saveName = uploadFile(file.getOriginalFilename(), file.getBytes());

  model.addAttribute("saveName", saveName);

}
 // UUID값_파일이름.jpg
 // 1.saveName만드는것 2.실제저장
private String uploadFile(String originalName, byte[] fileData)throws Exception{

  UUID uid = UUID.randomUUID();

  String saveName = uid.toString() + "_" + originalName;

  File target = new File(uploadPath,savedName);//아래까지 두줄은 실제 저장하는 로직

  FileCopyUtils.copy(fileData, target);//파일 데이터를 파일로 처리하거나, 복사하는 등의 작업에 유용하게 사용됨.

  return savedName;
}
```
