# 열린재정

> 열린재정 OpenAPI를 통한 국가 재정 데이터 자동 추출

⚠️ **아카이브 안내**: 이 코드는 **2018년**에 작성되었습니다. API 구조, 엔드포인트, 인증 방식 등이 변경되었을 수 있으며, 추가 유지보수 예정이 없습니다. 참고용으로만 활용하세요.

---

## 개요

| 항목 | 내용 |
|------|------|
| **API 형식** | JSON |
| **API 엔드포인트** | `http://openapi.openfiscaldata.go.kr/` |
| **API 키 발급** | https://openapi.openfiscaldata.go.kr/ |
| **작성 시기** | 2018년 |
| **작성 당시 상태** | 완료 |

---

## 파일 목록

| 파일명 | 방식 | 페이지 처리 | 설명 | 상태 |
|--------|------|------------|------|------|
| `model_01_fis.sas` | JSON 엔진 | 고정 루프 | 기본 구현 (일부 오류) | ❌ 오류 |
| `model_02_fis.sas` | TXT 파싱 | 고정 루프 | JSON 오류 회피 | ✅ 완료 |
| `model_03_fis.sas` | TXT 파싱 | 자동 루프 | 페이지 자동 처리 | ✅ 완료 |
| `model_04_fis.sas` | TXT 파싱 | 자동 루프 | 숫자형 변환 추가 | ✅ **추천** |

### 모델 진화 과정

```
Model 01 (JSON 엔진)
    ↓ JSON 파싱 오류 발생
Model 02 (TXT 파싱)
    ↓ 페이지 수 고정 문제
Model 03 (자동 페이지 루프)
    ↓ 변수 타입 문제
Model 04 (숫자형 변환) ← 추천
```

---

## 사용 방법

### 1. API 키 설정

```sas
%let String01=YOUR_API_KEY_HERE;  /* 열린재정에서 발급받은 API 키 */
```

### 2. 출력 경로 설정

```sas
%let dir=C:\json\;           /* 작업 디렉토리 */
%let lib=json;               /* 라이브러리 이름 */
libname &lib "&dir";
```

### 3. 매크로 실행 (model_04 기준)

```sas
%json(
    String05=http://openapi.openfiscaldata.go.kr/ExpendituresSettlement,
    date_s=2000,    /* 시작 연도 */
    date_e=2018     /* 종료 연도 */
);
```

### 4. 추출 가능 변수 설정

```sas
%let var_want=
FSCL_YY       /* 회계연도 */
OFFC_CD       /* 소관 코드 */
OFFC_NM       /* 소관명 */
FSCL_CD       /* 회계 코드 */
FSCL_NM       /* 회계명 */
...
;
```

---

## API 구조

### URL 구조

```
http://openapi.openfiscaldata.go.kr/{테이블명}
  ?FSCL_YY={회계연도}
  &key={API_KEY}
  &type=json
  &pindex={페이지번호}
  &psize=1000
```

### 주요 테이블

| 테이블명 | 설명 | 데이터 유형 |
|----------|------|------------|
| `RevenuesSettled` | 세입 결산 현황 | 세입 |
| `ExpendituresSettlement` | 세출 결산 현황 | 세출 |
| `DepartRevenExpenSettle` | 부처별 세입세출 결산 | 세입/세출 |
| `VWFOEM` | 월별 집행 현황 | 집행 |

### API 제한사항

- **한 번에 최대 1,000건** 반환
- 페이지 인덱스(`pindex`)로 다음 페이지 요청
- 데이터 없으면 응답 코드 `200` 반환

---

## 주요 변수 설명

### 세입 관련 (RevenuesSettled)

| 변수명 | 설명 |
|--------|------|
| `FSCL_YY` | 회계연도 |
| `OFFC_CD` / `OFFC_NM` | 소관 코드/명 |
| `FSCL_CD` / `FSCL_NM` | 회계 코드/명 |
| `REVN_BDGAMT_F` | 세입예산액 (당초) |
| `REVN_BDGAMT_M` | 세입예산액 (수정) |
| `RC_AMT` | 수납액 |
| `NPL_AMT` | 불납결손액 |

### 세출 관련 (ExpendituresSettlement)

| 변수명 | 설명 |
|--------|------|
| `FSCL_YY` | 회계연도 |
| `FLD_CD` / `FLD_NM` | 분야 코드/명 |
| `SECT_CD` / `SECT_NM` | 부문 코드/명 |
| `PGM_CD` / `PGM_NM` | 프로그램 코드/명 |
| `ANEXP_BDG_CAMT` | 세출예산현액 |
| `EP_AMT` | 지출액 |
| `DUSEAMT` | 불용액 |

---

## 알려진 문제 및 해결법

### 1. JSON 파싱 오류 (Model 01)

```
ERROR: Invalid JSON in input near line 1 column 101883: Encountered an illegal character.
```

**원인**: `libname json` 엔진의 특정 데이터 파싱 오류
**해결**: Model 02 이상 사용 (TXT 기반 파싱)

### 2. 페이지 수 예측 불가 (Model 02)

**원인**: 테이블별 데이터 건수가 달라 페이지 수 예측 불가
**해결**: Model 03 이상 사용 (`%do %until` 자동 종료)

### 3. 변수 타입 문제 (Model 03)

**원인**: 모든 변수가 문자형으로 추출됨
**해결**: Model 04 사용 (`input()` 함수로 숫자형 변환)

### 4. 다중 페이지 자동 처리 원리

```sas
%do %until (&check=200);
    /* API 호출 */
    if _n_=5 then call symput('check', scan(raw,2));
    /* 응답 코드가 200이면 더 이상 데이터 없음 */
    %let string02=%eval(&string02+1);  /* 페이지 증가 */
%end;
```

---

## 참고 링크

- [열린재정](https://www.openfiscaldata.go.kr/)
- [열린재정 OpenAPI](https://openapi.openfiscaldata.go.kr/)
- [API 활용 가이드](https://openapi.openfiscaldata.go.kr/portal/guide/useGuide)
