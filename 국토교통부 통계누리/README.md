# 국토교통부 통계누리

> 국토교통부 통계누리 OpenAPI를 통한 주택/건설 통계 데이터 추출

## 개요

| 항목 | 내용 |
|------|------|
| **API 형식** | JSON |
| **API 엔드포인트** | `http://stat.molit.go.kr/portal/openapi/` |
| **API 키 발급** | http://stat.molit.go.kr/ |
| **상태** | ⚠️ 개선 필요 |

---

## 파일 목록

| 파일명 | 방식 | 설명 | 상태 |
|--------|------|------|------|
| `model_01.sas` | JSON 엔진 | 기본 API 호출 및 데이터 확인 | ⚠️ 기본 |

---

## 사용 방법

### 1. API 키 설정

```sas
%let Str01=YOUR_API_KEY_HERE;  /* 국토교통부에서 발급받은 API 키 */
```

> **참고**: 이 폴더에서는 변수명이 `Str01`입니다 (다른 폴더의 `String01`과 다름)

### 2. 출력 경로 설정

```sas
%let lib=C:\Json\;
libname json "&lib";
```

### 3. API 호출

```sas
%let url=http://stat.molit.go.kr/portal/openapi/service/rest/getList.do
    ?key=&str01
    &form_id=840           /* 통계표 ID */
    &style_num=1           /* 스타일 번호 */
    &start_dt=2007         /* 시작 연도 */
    &end_dt=2007;          /* 종료 연도 */

filename out temp;
proc http url="&url" method="get" out=out;
run;

libname raw json fileref=out;
```

### 4. 데이터 확인

```sas
/* 라이브러리 구조 확인 */
proc datasets lib=raw; quit;

/* 데이터 미리보기 */
proc print data=raw.RESULT_DATA_FORMLIST(obs=100); run;
```

---

## API 구조

### URL 구조

```
http://stat.molit.go.kr/portal/openapi/service/rest/getList.do
  ?key={API_KEY}
  &form_id={통계표ID}
  &style_num={스타일번호}
  &start_dt={시작연도}
  &end_dt={종료연도}
```

### 주요 파라미터

| 파라미터 | 설명 | 예시 |
|----------|------|------|
| `key` | API 키 | `2e03c601603e...` |
| `form_id` | 통계표 고유 ID | `840` (임대주택 재고현황) |
| `style_num` | 출력 스타일 | `1` |
| `start_dt` | 시작 연도 | `2007` |
| `end_dt` | 종료 연도 | `2020` |

### 응답 데이터 구조

```
raw (JSON 라이브러리)
├── alldata                # 전체 데이터 (평면화)
└── RESULT_DATA_FORMLIST   # 통계표 형태 데이터
```

---

## 주요 통계 분야

| 분야 | 설명 |
|------|------|
| 주택 | 주택보급률, 주택건설 실적 등 |
| 임대주택 | 임대주택 재고현황 |
| 건설 | 건축허가, 착공, 준공 현황 |
| 토지 | 지가변동률, 토지거래 현황 |
| 교통 | 자동차 등록, 도로 현황 |

---

## 알려진 문제 및 특이사항

### 1. 데이터 구조 복잡성

**문제**: API 응답 데이터가 복잡한 구조로 되어 있어 파싱이 어려움

**현상**:
- `RESULT_DATA_FORMLIST`가 통계표 형태로 구성되어 있음
- 통계표마다 구조가 다를 수 있음
- 추가적인 데이터 정제 작업 필요

### 2. 장점

- 웹사이트에서 엑셀 다운로드 시 필요한 **셀 분할 및 병합 작업 불필요**
- 원본 데이터 형태로 직접 접근 가능
- `RESULT_DATA_FORMLIST`가 원하는 테이블 형태로 구성됨

### 3. 통계표 ID 확인

원하는 통계표의 `form_id`를 찾는 방법:
1. 국토교통부 통계누리 웹사이트에서 통계표 검색
2. URL에서 `form_id` 파라미터 확인

---

## 개선 필요 사항

- [ ] 다중 연도 자동 처리 매크로 추가
- [ ] 데이터 정제 및 변환 코드 추가
- [ ] 주요 통계표별 예제 코드 추가

---

## 참고 링크

- [국토교통부 통계누리](http://stat.molit.go.kr/)
- [OpenAPI 안내](http://stat.molit.go.kr/portal/openapi/openApiPage.do)
