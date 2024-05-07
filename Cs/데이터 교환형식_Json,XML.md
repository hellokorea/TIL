# 데이터 교환 형식 JSON, XML
- 데이터 교환 형식 중 대표적인 JSON과 XML에 대한 설명을 기재한다.

<br>

# JSON
## JSON이란?
- Javascript 객체 문법으로 구조화된 데이터 교환 형식이다. 단순 배열 및 문자열도 표현 가능하며 여러 언어에서 사용 된다.

<br>


## JSON의 상세 정의
- JSON은 독립적인 데이터의 집합체이다.
- 즉, Js에서는 Js Object가 쓰이고, Python은 Dict을 쓰는 것처럼 여러 언어에서 사용된다.
- 직렬화 및 역직렬화를 통해 외부의 시스템에서도 사용할 있도록 만들 수 있다.

<br>

## 직렬화, 역직렬화 Javascript 예시
```
JSON.parse() => 역직렬화
JSON.stringify() => 직렬화
```

<br>

## JSON에서 지원하는 Type
- Number

- String
- Boolean
- Array
- Object
- null
- 메소드, undefined는 TypeError를 발생 시킨다. 

<br>

## JSON 활용 범위
- 독립적인 형태이므로 서로 다른 시스템간에 교환이 용이하다.
- API의 반환형태, 시스템을 구성하는 설정파일 등에 활용된다.

EX )
- 빗썸 API Request 시 응답 JSON 
```
[
    {
        "market" : "KRW-BTC",
        "korean_name" : "비트코인",
        "english_name" : "Bitcoin"
    },
    ...
]
```

<br>

## JSON 구조 
### 객체 

```
{
    "name" : "Kim Jeong Dong"
}
```

<br>

### 배열 내 객체
```
[
    {
        "name" : "Park Jun",
        "age" : 30,
    },
    {
        "name" : "Kim Mari",
        "age" : 28,
    }
]

OR

[
    {
        "habbyPrefer" : [ {"scoccer" : "상"}, {"game" : "중"} ]
    }
]
```

<br>

## 역직렬화
- 배열 내 객체 항목의 첫번째 JSON 데이터를 역직렬화 한다면 다음과 같다.
```
const fs = require("fs");
const path = require("path");
const json = fs.readFileSync(path.join("a.json"));
const parse = JSON.parse(json);

console.log(parse); 
// [ { name: 'Park Jun', age: 30 }, { name: 'Kim Mari', age: 28 } ]

```

## 직렬화
- 다시 해당 데이터를 역직렬화 한다면 다음과 같다.
```
const fs = require("fs");
const path = require("path");
const json = fs.readFileSync(path.join("a.json"));
const parse = JSON.parse(json);
const deSerialization = JSON.stringify(parse);

console.log(deSerialization);
// [{"name":"Park Jun","age":30},{"name":"Kim Mari","age":28}]
```

## 접근
- Object에 접근하는 것처럼 동일한 방식으로 해당 key의 value값을 추출할 수 있다.
```
const fs = require("fs");
const path = require("path");
const json = fs.readFileSync(path.join("a.json"));
const parse = JSON.parse(json);

console.log(parse[0].name);
// Park Jun

```

## 유의사항
- JSON 양식을 작성할 때 즉, 스키마을 정의할 때 Type은 정적으로 동일하게 하는 것이 유용하다.
- 데이터의 무결성을 유지할 수 있고, 추가적은 Type을 체크하는 불필요한 로직을 최소화 할 수 있다.

<br>

# XML

<br>

## XML이란?
- 마크업 형태를 쓰는 데이터 교환 형식이다.
- 태그 등을 이용하여 문서나 데이터의 구조를 나타내는 방법 ( 속성 부여도 가능 )

<br>

## HTML과 XML의 차이점
- HTML은 데이터를 표시하는데 사용되며, XML은 데이터를 저장 및 전송하는데 사용된다.
- HTML은 미리 정의된 태그를 사용해야하지만, XML은 직접 커스텀 태그를 작성할 수 있다.
- HTML은 대소문자를 구분하지 않지만, XML은 구분한다.

<br>

## JSON과 XML의 차이점
- 태그가 계속 들어가기 때문에 JSON에 비해 무겁다.
- Js Object로 역직렬화하기 굉장히 번거롭다.

<br>

## XML 구조
```
<?xml version="1.0" encoding="UTF-8"?>

<GirlGroup>
    <SONG like ="1">
        <name>엔믹스</name>
        <song>영덤 스튜핏</song>
    </SONG>
    <SONG like ="2">
        <name>트와이스</name>
        <song>우아하게</song>
    </SONG>
</GirlGroup>
```



<br>

## XML 파일 JSON 형태로 받아오기
- 외부 라이브러리를 통해 XML 파일을 JSON 형태로 변환 시킬수 있다.
- 그리고 다시 JSON.parse()를 통해 Js Object로 변환해야 한다.
- 이러한 이유 때문에 XML를 갖다 쓰는 것은 매우 비효율적이다.

```
const fs = require("fs");
const path = require("path");
const xmlParser = require("xml2json");

let xmlFile = fs.readFileSync(path.join("a.xml"));

xmlFile = xmlParser.toJson(xmlFile);
console.log(xmlFile);
// {"GirlGroup":{"SONG":[{"like":"1","name":"엔믹스","song":"영덤 스튜핏"},
   {"like":"2","name":"트와이스","song":"우아하게"}]}}

const result = JSON.parse(xmlFile);
console.log(result);
// { GirlGroup: { SONG: [ [Object], [Object] ] } }
```

<br>

## XML의 활용
- sitemap.xml으로 많이 활용된다.
- 다른 언어에서도 독립적으로 사용된다.

### sitemap?
- 크롤링 봇에 노출되어 구글 검색 엔진 DB에 저장 될 수 있도록 양식을 제출하는 데이터이다.

