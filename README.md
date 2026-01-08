# SAS-Extraction

> 한국 정부 공공데이터를 SAS로 자동 추출하는 코드 저장소

본 프로젝트는 한국 정부 기관에서 제공하는 통계 API를 활용하여 데이터를 자동으로 수집하고 정제하는 SAS 코드 모음입니다. 각 기관마다 서로 다른 API 형식과 데이터 구조를 가지고 있어, 기관별 특성에 맞춘 코드를 제공합니다.

## 주요 기능

- 5개 정부 기관 통계 API 지원 (JSON/XML)
- SAS 매크로 기반 자동화된 데이터 수집
- 다중 페이지 처리 및 연도별 반복 수집
- 한글 인코딩 문제 해결

---

## 지원 기관

| 기관 | API 형식 | 상태 | 추천 파일 | 폴더 |
|------|---------|------|-----------|------|
| 통계청 (KOSIS) | JSON | ✅ 완료 | `json_engine_api_format_auto(완).sas` | [KOSIS/](./KOSIS/) |
| 열린재정 | JSON | ✅ 완료 | `model_04_fis.sas` | [열린재정/](./열린재정/) |
| 국토교통부 통계누리 | JSON | ⚠️ 개선중 | `model_01.sas` | [국토교통부 통계누리/](./국토교통부%20통계누리/) |
| 환경부 환경통계포털 | XML | ✅ 완료 | `model_03_env_xml.sas` | [환경부 환경통계포털/](./환경부%20환경통계포털/) |
| 지방재정365 | JSON | ✅ 완료 | `01.sas` | [지방재정365/](./지방재정365/) |

---

## 빠른 시작

### 요구사항

- **SAS 9.4** (유니코드 지원 버전, TS1M5 이상 권장)
- 각 기관별 **API 키** (무료 발급)

```
NOTE: SAS (r) Proprietary Software 9.4 (TS1M5 MBCS3170)
NOTE: 이 세션은 X64_10HOME 플랫폼에서 실행되고 있습니다.

NOTE: 업데이트 된 분석 제품:
  SAS/STAT 14.3
  SAS/ETS 14.3
  SAS/OR 14.3
  SAS/IML 14.3
  SAS/QC 14.3
```

> API로 불러오는 자료의 인코딩 문제로 SAS 9.4 (유니코드 지원) 버전이 필요합니다.

### 설치

```bash
git clone https://github.com/idencosmos/SAS-Extraction.git
```

### API 키 설정

각 SAS 파일 상단에서 API 키와 출력 경로를 설정합니다:

```sas
/* API 키 설정 */
%let String01=YOUR_API_KEY_HERE;

/* 출력 라이브러리 경로 설정 */
%let lib=C:\Output\;
```

### API 키 발급 링크

| 기관 | API 키 발급 |
|------|------------|
| KOSIS | https://kosis.kr/openapi/ |
| 열린재정 | https://openapi.openfiscaldata.go.kr/ |
| 국토교통부 통계누리 | http://stat.molit.go.kr/ |
| 환경부 환경통계포털 | http://stat.me.go.kr/ |
| 지방재정365 | http://lofin.mois.go.kr/ |

---

## 프로젝트 구조

```
SAS-Extraction/
│
├── KOSIS/                        # 통계청 KOSIS API (JSON)
│   ├── json_engine_api_format_auto(완).sas   # ✅ 추천
│   ├── json_engine_통계청(완).sas
│   ├── json_txt_api_format_auto.sas
│   ├── json_txt_api_format_semiauto.sas
│   ├── json_engine_api_format_semiauto.sas
│   ├── KOSIS_list_of_statistics.sas
│   └── README.md
│
├── 열린재정/                      # 열린재정 API (JSON)
│   ├── model_01_fis.sas
│   ├── model_02_fis.sas
│   ├── model_03_fis.sas
│   ├── model_04_fis.sas          # ✅ 추천
│   └── README.md
│
├── 국토교통부 통계누리/            # 국토교통부 API (JSON)
│   ├── model_01.sas
│   └── README.md
│
├── 환경부 환경통계포털/            # 환경부 API (XML)
│   ├── model_01_env_macro.sas + model_01_env_xml.sas
│   ├── model_02_env_macro.sas + model_02_env_xml.sas
│   ├── model_03_env_macro.sas + model_03_env_xml.sas  # ✅ 추천
│   └── README.md
│
├── 지방재정365/                   # 지방재정365 API (JSON)
│   ├── 01.sas                    # ✅ 추천
│   ├── sas7bdat/                 # 출력 데이터 폴더
│   └── README.md
│
└── README.md                     # 이 문서
```

---

## 기관별 사용 가이드

각 기관별 상세 사용법은 해당 폴더의 README.md를 참조하세요.

<details>
<summary><b>통계청 (KOSIS)</b></summary>

- **API 형식**: JSON
- **추천 파일**: `json_engine_api_format_auto(완).sas`
- **특징**: 다양한 통계목록 지원 (국내통계, e-지방지표, 국제통계 등)
- **상세 문서**: [KOSIS/README.md](./KOSIS/README.md)

</details>

<details>
<summary><b>열린재정</b></summary>

- **API 형식**: JSON
- **추천 파일**: `model_04_fis.sas`
- **특징**: 한 번에 1,000건 제한 → 다중 페이지 자동 처리
- **상세 문서**: [열린재정/README.md](./열린재정/README.md)

</details>

<details>
<summary><b>국토교통부 통계누리</b></summary>

- **API 형식**: JSON
- **추천 파일**: `model_01.sas`
- **특징**: 주택/건설 통계, 데이터 구조가 복잡함
- **상세 문서**: [국토교통부 통계누리/README.md](./국토교통부%20통계누리/README.md)

</details>

<details>
<summary><b>환경부 환경통계포털</b></summary>

- **API 형식**: XML
- **추천 파일**: `model_03_env_macro.sas` + `model_03_env_xml.sas`
- **특징**: Macro-XML 쌍으로 구성, 한글 인코딩 문제 해결됨
- **상세 문서**: [환경부 환경통계포털/README.md](./환경부%20환경통계포털/README.md)

</details>

<details>
<summary><b>지방재정365</b></summary>

- **API 형식**: JSON
- **추천 파일**: `01.sas`
- **특징**: 연도별(2010~2020) 다중 반복 처리
- **상세 문서**: [지방재정365/README.md](./지방재정365/README.md)

</details>

---

## 공통 문제 해결

### 인코딩 오류

```
ERROR: 한글이 깨지거나 잘리는 현상
```

**해결**: SAS 9.4 유니코드 지원 버전 사용

### JSON 파싱 오류

```
ERROR: Invalid JSON in input near line X column Y
```

**해결**: `libname json` 대신 TXT 기반 파싱 방식 사용 (열린재정 model_02 이상 참조)

### XML 한글 절단

```
WARNING: Data truncation occurred on variable...
```

**해결**: XML Map에서 `length=2000` 설정 (환경부 model_03 참조)

### API 키 오류

```
ERROR: API 인증 실패
```

**해결**:
1. API 키가 올바르게 입력되었는지 확인
2. API 키 유효기간 확인
3. URL 인코딩 필요 여부 확인 (특수문자 포함 시)

---

## 기여 방법

1. 이 저장소를 Fork 합니다
2. 새 브랜치를 생성합니다 (`git checkout -b feature/new-agency`)
3. 변경사항을 커밋합니다 (`git commit -m 'Add new agency support'`)
4. 브랜치에 Push 합니다 (`git push origin feature/new-agency`)
5. Pull Request를 생성합니다

---

## 라이선스

이 프로젝트는 교육 및 연구 목적으로 자유롭게 사용할 수 있습니다.

---

## 관련 링크

- [KOSIS 국가통계포털](https://kosis.kr/)
- [열린재정](https://www.openfiscaldata.go.kr/)
- [국토교통부 통계누리](http://stat.molit.go.kr/)
- [환경부 환경통계포털](http://stat.me.go.kr/)
- [지방재정365](http://lofin.mois.go.kr/)
