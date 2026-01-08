# 통계청 (KOSIS)

> KOSIS 국가통계포털 OpenAPI를 통한 통계 데이터 자동 추출

⚠️ **아카이브 안내**: 이 코드는 **2018년**에 작성되었습니다. API 구조, 엔드포인트, 인증 방식 등이 변경되었을 수 있으며, 추가 유지보수 예정이 없습니다. 참고용으로만 활용하세요.

---

## 개요

| 항목 | 내용 |
|------|------|
| **API 형식** | JSON |
| **API 엔드포인트** | `http://kosis.kr/openapi/` |
| **API 키 발급** | https://kosis.kr/openapi/ |
| **작성 시기** | 2018년 |
| **작성 당시 상태** | 완료 |

---

## 파일 목록

| 파일명 | 방식 | 설명 | 상태 |
|--------|------|------|------|
| `json_engine_api_format_auto(완).sas` | JSON 엔진 | 파라미터 기반 완전 자동화 | ✅ **추천** |
| `json_engine_통계청(완).sas` | JSON 엔진 | userStatsId 기반 반자동 | ✅ 완료 |
| `json_engine_api_format_semiauto.sas` | JSON 엔진 | userStatsId 기반 반자동 | ⚙️ 작동 |
| `json_txt_api_format_auto.sas` | TXT 파싱 | 텍스트 기반 자동화 | ⚙️ 작동 |
| `json_txt_api_format_semiauto.sas` | TXT 파싱 | 텍스트 기반 반자동 | ⚙️ 작동 |
| `KOSIS_list_of_statistics.sas` | - | 통계 목록 조회 유틸리티 | 🔧 유틸 |

### 파일 선택 가이드

- **처음 사용자**: `json_engine_api_format_auto(완).sas` 사용 권장
- **특정 통계표만 반복 추출**: `json_engine_통계청(완).sas` 또는 `_semiauto` 파일
- **SAS 9.4 이전 버전**: `json_txt_` 파일 사용 (JSON 엔진 미지원 시)
- **통계 목록 조회**: `KOSIS_list_of_statistics.sas`

---

## 사용 방법

### 1. API 키 설정

```sas
%let String01=YOUR_API_KEY_HERE;  /* KOSIS에서 발급받은 API 키 */
```

### 2. 출력 경로 설정

```sas
%let lib=C:\Json\;  /* 출력 파일 저장 경로 */
libname json "&lib";
```

### 3. 추천 파일 실행 (json_engine_api_format_auto)

```sas
%json(
    data_final=data_state,     /* 출력 데이터셋 이름 */
    String02=101,              /* orgId: 기관코드 */
    String03=DT_1B040M1,       /* tblId: 통계표 ID */
    String04=00+11+21+...,     /* objL1: 분류항목1 */
    String05=0,                /* objL2: 분류항목2 */
    String06=ALL,              /* objL3: 분류항목3 */
    String07=,                 /* objL4~8: 필요시 설정 */
    ...
    String12=all,              /* itmId: 항목 */
    String13=2,                /* loadGubun: 조회구분 */
    String14=Y,                /* prdSe: 수록주기 (Y:연, M:월 등) */
    date_s=1993,               /* 시작 연도 */
    date_e=2017                /* 종료 연도 */
);
```

### 4. 변수 필터링 (선택사항)

```sas
/* 원하는 변수만 추출 (공란이면 전체) */
%let var_want=PRD_DE C1 C1_NM C3_NM DT;
```

---

## API 구조

### 통계 데이터 조회 URL

```
http://kosis.kr/openapi/Param/statisticsParameterData.do
  ?method=getList
  &apiKey={API_KEY}
  &orgId={기관코드}
  &tblId={통계표ID}
  &objL1={분류1}&objL2={분류2}...
  &itmId={항목}
  &format=json
  &jsonVD=Y
  &prdSe={수록주기}
  &startPrdDe={시작기간}
  &endPrdDe={종료기간}
```

### 통계 목록 조회 URL

```
http://kosis.kr/openapi/statisticsList.do
  ?method=getList
  &apiKey={API_KEY}
  &vwCd={목록구분}
  &parentListId={상위목록ID}
  &format=json
```

---

## 통계목록 구분 (서비스뷰)

| 코드 | 설명 |
|------|------|
| `MT_ZTITLE` | 국내통계 주제별 |
| `MT_OTITLE` | 국내통계 기관별 |
| `MT_GTITLE01` | e-지방지표 (주제별) |
| `MT_GTITLE02` | e-지방지표 (지역별) |
| `MT_CHOSUN_TITLE` | 광복이전통계 (1908~1943) |
| `MT_HANKUK_TITLE` | 대한민국통계연감 |
| `MT_STOP_TITLE` | 작성중지통계 |
| `MT_RTITLE` | 국제통계 |
| `MT_BUKHAN` | 북한통계 |
| `MT_TM1_TITLE` | 대상별통계 |
| `MT_TM2_TITLE` | 이슈별통계 |
| `MT_ETITLE` | 영문 KOSIS |

---

## 주요 파라미터 설명

| 파라미터 | 설명 | 예시 |
|----------|------|------|
| `orgId` | 통계작성기관 코드 | `101` (통계청) |
| `tblId` | 통계표 ID | `DT_1B040M1` |
| `objL1~8` | 분류항목 (최대 8개) | `ALL`, `00+11+21` |
| `itmId` | 항목 코드 | `all`, `T1` |
| `prdSe` | 수록주기 | `Y`(연), `M`(월), `Q`(분기) |
| `loadGubun` | 조회구분 | `1`, `2` |

---

## 알려진 문제 및 해결법

| 문제 | 원인 | 해결 방법 |
|------|------|-----------|
| 한글 깨짐 | 인코딩 문제 | SAS 9.4 유니코드 버전 사용 |
| JSON 파싱 오류 | libname json 버그 | `json_txt_` 파일 사용 |
| 데이터 누락 | 연도별 API 제한 | 연도 범위를 나눠서 실행 |

---

## 참고 링크

- [KOSIS 국가통계포털](https://kosis.kr/)
- [KOSIS OpenAPI 안내](https://kosis.kr/openapi/)
- [API 사용 가이드](https://kosis.kr/openapi/guide/guide_01.jsp)
