---
layout: post
title: "Elastic APM, Elasticsearch"
author: "HJ Lee"
---

## 💡 Elastic APM이란?

> 사용자 애플리케이션이 미리 구성된 대시보드를 이용해 Kibana에서 시각화를 위해 Elasticsearch로 성능 메트릭을 전송할 수 있도록 하는 도구

- Elastic APM에 대한 설명은 공식 블로그에 가장 잘 설명되어 있다.

🔗 Link: [**Elastic 공식 블로그의 상세 설명**](https://www.elastic.co/kr/blog/monitoring-applications-with-elasticsearch-and-elastic-apm "Elastic APM 상세 설명 보러가기")

---

    🎇 그러면 Elasticsearch와 Kibana란 무엇인가?

---

### 🔍 1. Elasticsearch란?

    📌 검색엔진 application
    📌 루씬(Lucene: Java 언어로 개발된 정보 검색 오픈소스 라이브러리) 기반으로 만들어졌으므로 JVM 상에서 돌아간다. 즉 JDK가 설치된 OS 상에서만 동작한다.
    📌 RESTful HTTP API를 이용해 CRUD 가능하다.
    📌 입출력 데이터 포맷은 모두 JSON 형식이다.
    📌 실시간 분석이 가능하다.

### 🔍 2. Kibana란?

    📌 데이터를 실시간으로 시각화해주는 도구
    📌 html, javascript 등으로 구성된 웹 UI
    📌 강력하고 화려한 그래픽을 통해 데이터 시각화를 지원한다.

### 🔍 3. Elastic APM의 구성

    1) 우선 로그 분석 및 모니터링 서비스를 제공할 서버에 Elasticsearch, Kibana, APM Server를 설치한다.
    2) 각 어플리케이션 서버에 개발 언어에 맞는 APM Agent를 다운받아 매뉴얼대로 코드 안에 넣어주기만 하면 설치는 끝난다.

### 🔍 4. Elastic APM의 흐름

    1) 각 어플리케이션 서버를 구동하면 APM Agent가 정보를 수집한다.
    2) 수집된 정보는 APM Server로 전송된다.
    3) APM Server는 Elasticsearch로 데이터를 전송한다.
    4) Elasticsearch로 전송된 데이터를 Kibana에서 대시보드 형태 또는 APM UI에 맞게 시각화한다.

### 🔍 5. Elastic APM의 장단점과 활용 방안

#### 5.1. 장점

- 오픈소스로 무료
- 구조가 단순하여 여러 도메인에 범용적으로 사용하기 좋다.
- Elasticsearch는 매우 빠르고 인덱싱 과정이 쉽다.
- Elasticsearch의 데이터를 이용해 Kibana로 유연한 커스텀 대시보드를 쉽게 구축할 수 있다.

#### 5.2. 단점

- 검색엔진 특성 상 데이터가 이미 인덱싱 되어 있어서 커스터마이징, Join, 연산 등이 쉽지 않다.
- 유료 도구들에 비해 병렬 확장(Scale-Out)에 약점이 있다.

#### 5.3. 활용 방안

    🎇 Elastic APM을 자유롭게 활용하려면 결국은 Elasticsearch에 대한 이해도가 높아야 한다.

---

## 💡 ELK 스택이란?

> Elasticsearch(분석,검색,저장) + Logstash(수집) + Kibana(시각화) 조합으로 Logstash가 DB 또는 CSV 등의 데이터를 Elasticseach에 수집하면 시각화 도구인 Kibana가 Elasticesearch의 데이터를 화면에 보기좋게 보여준다.

---

## 💡 Elasticsearch 요약

### 🔍 1. RDBMS와의 개념 비교

    📌 Index = Database
    📌 Type = Table
    📌 Document = Row
    📌 Field = Column
    📌 Mapping = Schema

### 🔍 2. Elasticsearch 사용법 요약

#### 2.1. Index 조회, 생성, 삭제

    🎇 RDB에서의 데이터베이스 조회, 생성, 삭제와 동일

##### 2.1.1. Index 조회 (GET)

```c
// classes라는 이름을 가진 Index 조회
// 해당 Index가 없으면 "status": 404가 출력된다.
curl -XGET http://localhost:9200/classes?pretty
```

> 맨끝의 '?pretty'는 단순히 콘솔 상에서 JSON 데이터를 보기 좋게 출력하기 위해 사용하는 것이므로 필수 사용은 아님

##### 2.1.2. Index 생성 (PUT)

```c
// classes라는 이름의 Index 생성
curl -XPUT http://localhost:9200/classes
```

##### 2.1.3. Index 삭제 (DELETE)

```c
// classes Index 삭제
curl -XDELETE http://localhost:9200/classes
```

#### 2.2. 다양한 Document 생성 방법

    🎇 RDB에서의 테이블 생성과 동일

> Document 생성 시 POST 메서드를 사용한다.

##### 2.2.1. JSON 문자열로 Document 생성

```c
// 기존에 classes라는 Index가 존재하지 않더라도 아래 커맨드를 실행하면 Index까지 한번에 만들어진다.
curl -XPOST http://localhost:9200/classes/class/1/ -d '{"title":"Algorithm", "professor":"John"}'
```

##### 2.2.2. 파일로 Document 생성

```c
// json 파일을 불러와서 Document를 생성할 수 있다.
curl -XPOST http://localhost:9200/classes/class/1/ -d @[filename].json
```

##### 2.2.3. 한꺼번에 여러 Document 생성하기 (batch)

```c
// Document _bulk 생성
curl -XPOST http://localhost:9200/_bulk?pretty --data-binary @classes.json
```

#### 2.3. Field 추가, 수정

    🎇 RDB에서의 컬럼 추가, 수정과 동일

> Field 추가, 수정 시 POST 메서드를 사용한다.

##### 2.3.1. Field 추가

```c
// id가 1인 Document에 seq라는 이름을 가진 Field를 추가하고 그 값은 1로 설정한다.
curl -XPOST http://localhost:9200/classes/class/1/_update?pretty -d '{"doc": {"seq":1}}'
```

##### 2.3.2. field 값 수정

```c
// 위에서 추가한 seq Field의 값을 1에서 999로 변경
curl -XPOST http://localhost:9200/classes/class/1/_update?pretty -d '{"doc": {"seq":999}}'
```

##### 2.3.3. field 값을 script로 연산하여 수정하기

```c
// seq Field 의 값에 5를 더한다.
curl -XPOST http://localhost:9200/classes/class/1/_update?pretty -d '{"script":"ctx._source.seq += 5"}'
```

#### 2.4. 데이터 검색 (Search)

##### 2.4.1. 모든 Document 검색

```c
// _search 사용
curl -XGET http://localhost:9200/classes/class/_search?pretty
```

##### 2.4.2. Document 내 특정 Field의 조건 검색

    🎇 RDB에서 where절에 해당한다.

```c
// 'q='로 시작하는 쿼리문을 만들어 사용하면 된다.
// point가 100인 Document 검색
curl -XGET http://localhost:9200/classes/class/_search?q=point:100&pretty
```

##### 2.4.3. Request Body를 사용한 검색

```javascript
// 위 쿼리문을 사용한 커맨드와 동일한 결과를 보여주는 검색이다.
curl -XGET http://localhost:9200/classes/class/_search -d '{
  "query":{
    "term":{"point":100}
  }
}'
```

#### 2.5. Mapping

> Mapping이란 Date, String, Number 등으로 Field에 데이터 타입을 설정해주는 작업이다.

    📌 실제 운용 시 mapping 작업을 해주지 않으면 Elasticsearch는 모든 데이터의 타입을 문자열로 인식한다.
    📌 예를 들면 평균값이나 총합을 계산하고 싶어도 Elasticsearch는 해당 데이터를 문자열로 인식하고 있기 때문에 산술연산이 아예 불가능해진다.
    📌 이는 Kibana로 데이터를 시각화하려고 할 때 치명적인 문제가 된다.
    📌 애초에 mapping하여 데이터를 넣는 것이 가장 좋지만 mapping 없이 입력된 값에 후처리 해주는 것도 가능하다.

#### 2.6. Aggregation

> Aggregation이란 Document 내의 조합을 통해 어떠한 값을 도출할 때 쓰이는 방법이다.

##### 2.6.1. Metric Aggregation

    🎇 avarage, total, max, min 등의 산술 계산을 수행한다.

##### 2.6.2. Bucket Aggregation

    🎇 RDB의 group by와 동일하다.

## 💡 Elasticsearch 실용 예제 모음

🔗 Link: [**위키북스의 Elasticsearch 예제**](https://github.com/wikibook/elasticsearch "Elasticsearch 예제 보러가기")
