# 지방재정365

> 지방재정365 OpenAPI를 통한 지방자치단체 재정 데이터 자동 추출

## 개요

| 항목 | 내용 |
|------|------|
| **API 형식** | JSON |
| **API 엔드포인트** | `http://lofin.mois.go.kr/HUB/` |
| **API 키 발급** | http://lofin.mois.go.kr/ |
| **상태** | ✅ 완료 |

---

## 파일 목록

| 파일명 | 방식 | 설명 | 상태 |
|--------|------|------|------|
| `01.sas` | TXT 파싱 | 연도별 다중 페이지 자동 처리 | ✅ **추천** |

### 폴더 구조

```
지방재정365/
├── 01.sas              # 메인 코드
├── sas7bdat/           # 출력 데이터 저장 폴더
│   └── SeriesDataOut.txt   # API 응답 임시 파일
└── README.md
```

---

## 사용 방법

### 1. API 키 설정

```sas
%let String01=YOUR_API_KEY_HERE;  /* 지방재정365에서 발급받은 API 키 */
```

### 2. 출력 경로 설정

```sas
%let dir=D:\...\지방재정365\sas7bdat\;  /* 출력 폴더 */
%let lib=json;
libname &lib "&dir";
```

### 3. 매크로 실행

```sas
%json(
    String05=http://lofin.mois.go.kr/HUB/ARBGT,  /* API 테이블 URL */
    table=table001,                               /* 출력 데이터셋 이름 */
    date_s=2010,                                  /* 시작 연도 */
    date_e=2020                                   /* 종료 연도 */
);
```

---

## API 구조

### URL 구조

```
http://lofin.mois.go.kr/HUB/{테이블명}
  ?key={API_KEY}
  &type=json
  &pindex={페이지번호}
  &psize=100
  &accnut_year={회계연도}
```

### 주요 파라미터

| 파라미터 | 설명 | 예시 |
|----------|------|------|
| `key` | API 키 | `ESJRV10001334...` |
| `type` | 응답 형식 | `json` |
| `pindex` | 페이지 번호 | `1`, `2`, `3`, ... |
| `psize` | 페이지 크기 | `100` (최대) |
| `accnut_year` | 회계연도 | `2010`, `2020` |

### API 제한사항

- **한 번에 최대 100건** 반환
- 페이지 인덱스(`pindex`)로 다음 페이지 요청
- 데이터 없으면 응답 코드 `200` 반환

---

## 주요 테이블

| 테이블명 | 설명 |
|----------|------|
| `ARBGT` | 당초예산 총계 |
| `AABGT` | 추경예산 총계 |
| `AADGT` | 세입결산 총계 |
| `AAEDT` | 세출결산 총계 |

---

## 코드 동작 원리

### 1. 이중 반복 구조

```sas
%do date_want=&date_s %to &date_e;       /* 연도별 반복 */
    %do %until (&check=200);              /* 페이지별 반복 */
        /* API 호출 및 데이터 수집 */
    %end;
%end;
```

### 2. 자동 페이지 종료 조건

```sas
if _n_=5 then call symput('check', scan(raw,2));
/* 응답 코드가 200이면 더 이상 데이터 없음 → 루프 종료 */
```

### 3. 데이터 병합

```sas
/* 연도별 데이터셋 병합 */
data &lib..&table;
  set L001_&date_s-L001_&date_e;  /* L001_2010 ~ L001_2020 병합 */
  if accnut_year="" then delete;
run;
```

---

## 출력 결과

### 개별 연도 데이터셋

```
L001_2010  ← 2010년 데이터
L001_2011  ← 2011년 데이터
...
L001_2020  ← 2020년 데이터
```

### 병합 데이터셋

```
json.table001  ← 전체 연도 통합 데이터
```

---

## 알려진 문제 및 해결법

### 1. 페이지 크기 제한

**문제**: 한 번에 100건만 반환

**해결**: `%do %until` 자동 페이지 루프로 해결됨

### 2. 빈 데이터 행 제거

```sas
if accnut_year="" then delete;  /* 빈 행 제거 */
```

### 3. 불필요한 변수 제거

```sas
data &lib..&table(drop=group RESULT CODE MESSAGE ARBGT list_total_count);
/* 메타데이터 변수 제거 */
```

---

## 참고 링크

- [지방재정365](http://lofin.mois.go.kr/)
- [OpenAPI 안내](http://lofin.mois.go.kr/portal/bbs/openApiList.do)
