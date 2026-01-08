# 환경부 환경통계포털

> 환경부 환경통계포털 API를 통한 환경 통계 데이터 자동 추출

## 개요

| 항목 | 내용 |
|------|------|
| **API 형식** | XML |
| **API 엔드포인트** | `http://stat.me.go.kr/nesis/mesp/searchApi/` |
| **API 키 발급** | http://stat.me.go.kr/ |
| **상태** | ✅ 완료 |

---

## 파일 목록

이 폴더의 파일들은 **Macro-XML 쌍**으로 구성되어 있습니다.

| Macro 파일 | 실행 파일 | 설명 | 상태 |
|------------|----------|------|------|
| `model_01_env_macro.sas` | `model_01_env_xml.sas` | URL 조합 방식 | ❌ 오류 |
| `model_02_env_macro.sas` | `model_02_env_xml.sas` | 기본 XML 파싱 | ⚠️ 한글 절단 |
| `model_03_env_macro.sas` | `model_03_env_xml.sas` | length 자동 수정 | ✅ **추천** |

### 파일 구조

```
model_XX_env_macro.sas  ← 매크로 정의 파일
       ↓ %include
model_XX_env_xml.sas    ← 실행 파일 (매크로 호출)
```

---

## 사용 방법

### 1. 출력 경로 설정 (model_03_env_xml.sas)

```sas
%let lib=xml;
%let dir=C:\Xml\;
libname &lib "&dir";
```

### 2. 매크로 파일 불러오기

```sas
%include 'D:\...\model_03_env_macro.sas';
```

> **주의**: `%include` 경로를 매크로 파일의 실제 위치로 수정해야 합니다.

### 3. 매크로 실행

```sas
%env(
    data=PM10_95_15,    /* 출력 데이터셋 이름 */
    url=http://stat.me.go.kr/nesis/mesp/searchApi/searchApiMainAction.do
        ?task=C
        &key=YOUR_API_KEY
        &tblID=DT_106N_03_0200029
        &startDate=1995
        &endDate=2015
        &dateType=Y
        &point=default
        &dataType=data
);
```

---

## API 구조

### 통계자료 URL 구조

```
http://stat.me.go.kr/nesis/mesp/searchApi/searchApiMainAction.do
  ?task=C
  &key={API_KEY}
  &tblID={통계표ID}
  &startDate={시작기간}
  &endDate={종료기간}
  &dateType={기간유형}
  &point=default
  &dataType=data
```

### 메타자료 URL 구조

```
http://stat.me.go.kr/nesis/mesp/searchApi/searchApiMainAction.do
  ?task=C
  &key={API_KEY}
  &tblID={통계표ID}
  &dataType=meta
```

### 주요 파라미터

| 파라미터 | 설명 | 예시 |
|----------|------|------|
| `task` | 작업 유형 | `C` |
| `key` | API 키 (URL 인코딩) | `8NfRsp8czHw0JtyP5AQ5Yg%3D%3D` |
| `tblID` | 통계표 ID | `DT_106N_03_0200029` |
| `startDate` | 시작 기간 | `1995`, `201001` |
| `endDate` | 종료 기간 | `2015`, `201709` |
| `dateType` | 기간 유형 | `Y`(연), `M`(월) |
| `dataType` | 데이터 유형 | `data`, `meta` |

---

## 주요 환경 통계

| 분야 | 통계표 예시 |
|------|------------|
| 대기 | PM10 미세먼지 관측자료 |
| 수질 | 하천 수질 현황 |
| 폐기물 | 폐기물 발생 및 처리 현황 |
| 소음 | 환경소음 측정 현황 |

---

## 알려진 문제 및 해결법

### 1. URL 조합 오류 (Model 01)

**문제**: `tranwrd` 또는 `translate`로 조합된 URL을 `xmlv2`로 불러올 때 오류

**현상**: XML 라이브러리 안에 Header와 message만 생성됨

**해결**: Model 02 이상 사용 (URL을 직접 입력)

### 2. 한글 데이터 절단 (Model 02)

**문제**: XML 데이터 읽기 시 한글이 잘림

**오류 메시지**:
```
WARNING: Data truncation occurred on variable data_LeftHakmok1
Column length=3 Additional length=9.
```

**원인**: XML Map의 기본 length가 짧음

**해결**: Model 03 사용 (XML Map의 length를 2000으로 자동 수정)

### 3. Model 03의 해결 원리

```sas
/* XML Map 파일 읽기 */
data map;
  infile map dsd lrecl=999999999;
  input raw: $2000.@@;
run;

/* LENGTH 태그를 2000으로 변경 */
data map_re;
  set map;
  if index(raw,'<LENGTH>')^=0 then raw='<LENGTH>2000</LENGTH>';
run;

/* 수정된 Map으로 다시 읽기 */
libname raw xmlv2 xmlfileref=out xmlmap=map_re;
```

---

## 예제: PM10 미세먼지 데이터

### 연도별 데이터 (1995-2015)

```sas
%env(
    data=PM10_95_15,
    url=http://stat.me.go.kr/nesis/mesp/searchApi/searchApiMainAction.do
        ?task=C
        &key=YOUR_API_KEY
        &tblID=DT_106N_03_0200029
        &startDate=1995
        &endDate=2015
        &dateType=Y
        &point=default
        &dataType=data
);
```

### 월별 데이터 (2010.01-2017.09)

```sas
%env(
    data=PM10_01_17_02,
    url=http://stat.me.go.kr/nesis/mesp/searchApi/searchApiMainAction.do
        ?task=C
        &key=YOUR_API_KEY
        &tblID=DT_106N_03_0200045
        &startDate=201001
        &endDate=201709
        &dateType=M
        &point=default
        &dataType=data
);
```

---

## 참고 링크

- [환경부 환경통계포털](http://stat.me.go.kr/)
- [환경통계 OpenAPI](http://stat.me.go.kr/nesis/mesp/help/openApi/openApiIntro.do)
