---
title: 스키마란 무엇인가
date: "2020-03-08T10:00:00.169Z"
template: "post"
draft: false
slug: "about-schema"
category: "backend"
tags:
  # - "think"
  - "backend"
  # - "load balancing"
# description: "스키마가 무엇인지 고민해보"
socialImage: "/media/42-line-bible.jpg"
---

최근 백엔드 작업을 하다가, "JSON 스키마"를 처음 접했다. 스키마? 데이터의 구조로 알고 있는데... 데이터베이스에서나 쓰던 개념 아니야? JSON에 무슨 스키마가 있다는 거지? 라는 질문에서 스키마에 대해서 잘 모르는 것이 많다는 생각이 들어서, 용어에 대한 정의부터 출발해 스키마가 실제로 어떻게 쓰이고 있는지에 대하여 고민해보았다.

***

## 이론

스키마의 사전적 정의는 다음과 같다.
```
Schema : (계획·이론의) 개요[윤곽]
```

흠... -_-;;
사전적 의미가 크게 와닿지 않아서 구글에 "Schema"를 이미지로 검색해보았다.

![schema](/media/schema.png)

이미지를 보니, 복잡하고 큰 개념을 뭔가 형식으로 도식화한 느낌을 준다. 왜 이렇게 규칙을 만들어 도식화하는가? 규칙을 정해 데이터를 다룸으로써 우리가 복잡하고 큰 데이터를 컨트롤할 수 있겠다는 생각이 들었고, 형식과 규칙을 스키마의 핵심이라고 나름대로 생각을 정리했다. 

***

## 실전에서 사용하는 스키마들

이번에는 실전에서 사용하는 스키마의 용례들을 살펴보자. 사례를 찾아보니 데이터를 다루는 곳에서 스키마가 많이 사용되고 있었다. 데이터를 효율적으로 다루기 위해서는 규칙과 형식, 즉 스키마가 필요한 것이다. 찾아본 바로는 크게 database(RDBMS, noSQL), XML(XSD), JSON Schema 에서 스키마를 언급하여 소개해둔다. 

### 1. 데이터베이스에서의 기본 개념, 기본 구조로써 언급되는 스키마

- 외부 스키마, 개념 스키마, 내부 스키마 등 (아래 자료 참고)
- [https://ykcb.tistory.com/entry/데이터베이스-스키마의-개념-특징](https://ykcb.tistory.com/entry/%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EC%8A%A4%ED%82%A4%EB%A7%88%EC%9D%98-%EA%B0%9C%EB%85%90-%ED%8A%B9%EC%A7%95)
- [https://www.guru99.com/dbms-schemas.html](https://www.guru99.com/dbms-schemas.html)

### 2. RDBMS의 정해진 구조로써 언급되는 스키마

- noSQL이 스키마가 없어 데이터 구조가 유연하다는 말은, 다음 두 가지 의미를 내포한다.
    - RDBMS처럼 정해진 테이블 구조를 갖지 않는다.
        - RDBMS에서 property 하나를 추가하려면 Alter table 해서 모든 레코드에 컬럼을 추가해야 하지만, noSQL에서는 그럴 필요가 없다.
    - 각 레코드의 property가 고정돼있지 않다(예를 들면, int 컬럼을 가진 레코드인데 string을 넣으면 테이블에선 대응이 안되지만 noSQL에서는 된다는 것)
    - 참고 자료 : [https://www.mongodb.com/blog/post/why-schemaless](https://www.mongodb.com/blog/post/why-schemaless)

### 3. mySQL에서 DB 자체로써 언급되는 스키마

- mySQL에서는 테이블의 집합체로써의 데이터베이스를 스키마라고 얘기한다. 그래서 데이터베이스 만드는 코드랑 스키마 만드는 코드가 같다.
```SQL
    mysql > CREATE DATABASE `Sparta`;
    
    mysql > CREATE SCHEMA `Greek`;
```

### 4. JSON, XML 에서 언급되는 스키마

- JSON과 XML은 데이터를 형식화하는 방법이다. 이들과 함께 사용되는 스키마는 데이터의 구조를 알 수 있게 해주고, validation시 활용한다. (스키마에 맞지 않는 데이터를 보냈다면 오류를 발생시킨다던지)
- 참고 자료
    - [http://tcpschool.com/json/json_schema_schema](http://tcpschool.com/json/json_schema_schema)
    - [http://tcpschool.com/xml/xml_xsd_intro](http://tcpschool.com/xml/xml_xsd_intro)
    - [https://ko.wikipedia.org/wiki/XML_스키마_(W3C)](https://ko.wikipedia.org/wiki/XML_%EC%8A%A4%ED%82%A4%EB%A7%88_(W3C))

아래 예시는 각각 JSON, XML 스키마를 나타낸다.
``` JSON
    {
        "title": "강아지 스키마",
        "description": "이 스키마는 강아지에 관한 데이터를 검증하기 위해 작성된 스키마임.",
        "type": "object",
        "properties": {
            "name": {"type": "string"},
            "category": {"type": "string"},
            "age": {"type": "integer"},
            "weight": {"type": "number"},
        }
    }
```
``` XML
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema elementFormDefault="qualified" xmlns:xs="http://www.w3.org/2001/XMLSchema">
    	...
          <xs:sequence>
            <xs:element name="Recipient" type="xs:string" />
            <xs:element name="House" type="xs:string" />
            <xs:element name="Street" type="xs:string" />
            <xs:element name="Town" type="xs:string" />
            <xs:element name="County" type="xs:string" minOccurs="0" />
            <xs:element name="PostCode" type="xs:string" />
            <xs:element name="Country" minOccurs="0">
    
        ...
    </xs:schema>
```
***
### 정리하면...

![theory_practice](/media/theory_practice.png)

스키마 자체는 개요, 구조, 형식이라는 뜻을 가지고 있다. 데이터베이스에서 사용하는 스키마에 대해서만(그것도 겉할기로) 스키마를 알고 있다보니, 여러 문맥에서 사용되는 스키마를 봤을 때 헷깔렸던 것 같다. 스키마는 문맥에 따라 다른 모습으로 머릿속에 그려져야 한다. 이론과 실제가 머리에 들어오게 되어, 이제는 스키마가 뭔지 확실히 얘기할 수 있다는 확신이 생겼다.