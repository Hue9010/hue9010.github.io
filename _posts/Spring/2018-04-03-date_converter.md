---
layout: post
title:  "LocalDate, LocalDateTime Converter 이용하기"
tags: [Spring, Spring Security]
categories: [Spring]
description: "날짜를 입력 받는 방식을 알아 봅시다!!!!"
---

시작하며...  
==========  

Date 타입에 따라 Converter를 이용하는 방식이 몇가지 있는데 오늘은 Config 클래스를 통해서 Coverter를 사용 하는 방식을 보도록 하겠습니다.

작업 환경  
> springBootVersion = '1.5.9.RELEASE'  

---

Converter 제작   
==============  

일단 사용 할 Date 타입에 대한 Converter를 만들어 줍니다.  

### LocalDateConverter  

```java
public final class LocalDateConverter implements Converter<String, LocalDate> {

    private final DateTimeFormatter formatter;

    public LocalDateConverter(String dateFormat) {
        this.formatter = DateTimeFormatter.ofPattern(dateFormat);
    }

    @Override
    public LocalDate convert(String source) {
        if (source == null || source.isEmpty()) {
            return null;
        }

        return LocalDate.parse(source, formatter);
    }
}
```  

### LocalDateTimeConverter  

```java
public final class LocalDateTimeConverter implements Converter<String, LocalDateTime> {

    private final DateTimeFormatter formatter;

    public LocalDateTimeConverter(String dateFormat) {
        this.formatter = DateTimeFormatter.ofPattern(dateFormat);
    }

    @Override
    public LocalDateTime convert(String source) {
        if (source == null || source.isEmpty()) {
            return null;
        }

        return LocalDateTime.parse(source, formatter);
    }
}
```  

`LocalDateTime`, `LocalDate` 둘 다 만드는 방식은 같습니다. 예제로 두개 다 제작 했지만 자신이 필요한 타입만 만드셔도 됩니다.  

---

Converter를 Config를 통해 등록   
============================

### WebMvcConfig  

```java
@Configuration
public class WebMvcConfig extends WebMvcConfigurerAdapter {
	@Override
	public void addFormatters(FormatterRegistry registry) {
		registry.addConverter(new LocalDateConverter("yyyy-MM-dd"));
		registry.addConverter(new LocalDateTimeConverter("yyyy-MM-dd'T'HH:mm"));
	}
```  

Config에서 Converter를 등록 할 때 사용된 `dateFormat` 형식으로 날짜를 받아 오게 됩니다. 그렇기 때문에 자신이 사용 하려는 날짜 형식을 고려해서 작성 하셔야 합니다.  

> **yyyy-MM-dd'T'HH:mm** -> 2018-04-03T12:12  
> **yyyy-MM-dd''T''HH : mm : ss.SSSX** -> 2018-04-03T14:18:13.105+01:00  
> **yyyy-MM-dd HH.mm** -> 2018-04-03 13.44  
> **yyyy-MM-dd** ->	2018-04-03

[각 알파벳의 의미(영어)](http://www.java2s.com/Tutorials/Java/Java_Format/0030__Java_Date_Format_Symbol.htm)  

[더 많은 패턴 유형(한글)](https://www.ibm.com/support/knowledgecenter/ko/SSHEB3_3.3.2/com.ibm.tap.doc_3.3.2/loc_topics/c_custom_date_formats.html)  

**참고로 패턴이 다른 형식으로 입력이 들어오면 정상 작동하지 않습니다.**  

---

테스트 코드 작성시  
===============

```java
@Test
public void createMilestone() {
	String subject = "mysubject";
	HttpEntity<MultiValueMap<String, Object>> request = HtmlFormDataBuilder.urlEncodedForm()
			.addParameter("subject", subject)
			.addParameter("startDate", "2018-04-02T10:10")
			.addParameter("endDate", "2018-04-11T12:12").build();

	ResponseEntity<String> response =
			basicAuthTemplate(findDefaultUser()).postForEntity("/milestones", request, String.class);

	assertThat(response.getStatusCode(), is(HttpStatus.FOUND));
	assertThat(milestoneRepository.findBySubject(subject).get().getSubject(), is(subject));
	assertThat(response.getHeaders().getLocation().getPath(), is("/milestones/list"));
}
```

Milestone의 필드는 아래와 같고, Post 방식을 이용하여 Milestone 생성을 요청 합니다.   

```java
public class Milestone extends AbstractEntity {
	private String subject;

	private LocalDateTime startDate;

	private LocalDateTime endDate;
}
```  

사실 날짜만 있는게 아니라 조금 복잡해 보일 수도 있지만...파라미터로 Date를 추가하는 방식이 몇가지 있지만 보여 드리고 싶었던 부분은 저렇게 String 형태로 작성해도 정상적으로 작동 합니다.  
