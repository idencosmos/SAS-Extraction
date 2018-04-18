### SAS

본 repo의 목적은 SAS 코드를 개선하고 공유하는 것입니다. 우선은 한국의 통계청(KOSIS), 한국은행 경제통계시스템(ECOS)를 비롯하여 국토교통부, 열린재정 등에 있는 정부 자료를 API를 통해 추출하고자 합니다. 각 기관에서 손쉽게 자료를 얻을 수 있는 서비스를 제공하고 있지만, 기관마다 그 방법이 다릅니다. 또한 각 기관에서 제공하는 자료를 이용한 연구가 많음에도 불구하고, 이용된 자료들이 일회성으로 이용되고 사라지고 있습니다. 그래서 각 기관에 맞추어 원하는 자료를 일관된 형태로 추출하는 방법을 공유하고자 합니다. 그리고 이용된 자료들이 일회성으로 사라지는 것이 아니라 지속적으로 유지되어 필요한 사람들이 사용할 수 있기를 바랍니다.

---

    NOTE: Copyright (c) 2016 by SAS Institute Inc., Cary, NC, USA.
    NOTE: SAS (r) Proprietary Software 9.4 (TS1M5 MBCS3170)
    NOTE: 이 세션은 X64_10HOME 플랫폼에서 실행되고 있습니다.

    NOTE: 업데이트 된 분석 제품:

      SAS/STAT 14.3
      SAS/ETS 14.3
      SAS/OR 14.3
      SAS/IML 14.3
      SAS/QC 14.3

    NOTE: 추가 호스트 정보:

      X64_10HOME WIN 10.0.16299 Workstation

json으로 불러오는 언어코드에 문제가 있어서 SAS 9.4 (유니코드 지원)으로 실행합니다.

---

- 국토교통부 통계누리
  - API를 이용한 테이블 추출의 경우 사이트 자체에서 제공하는 통계표를 다운받아 셀 분할 및 병합 등의 작업이 필요 없다는 장점이 있다. 하지만 개인적으로 데이터 자체가 난해하게 구성되어 있어서 통계 자료를 사용하는 것 자체에 어려움이 따른다.

- 열린재정

- 환경부 환경통계포털

- KOSIS
