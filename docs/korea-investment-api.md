# 한국투자증권 API 가이드

이 문서는 `chartists` 프로젝트에 필요한 한국투자증권 API의 사용법을 정리합니다.

## 1. 종목 코드 정보

전체 종목 코드는 API를 통해 실시간으로 조회하는 대신, 한국투자증권에서 제공하는 마스터 파일을 다운로드하여 사용하는 것이 효율적입니다.

### 마스터 파일 다운로드

- **코스피(KOSPI) 종목 코드:**
  - [kospi_code.mst.zip](https://new.real.download.dws.co.kr/common/master/kospi_code.mst.zip)
- **코스닥(KOSDAQ) 종목 코드:**
  - [kosdaq_code.mst.zip](https://new.real.download.dws.co.kr/common/master/kosdaq_code.mst.zip)

> `.mst` 파일은 확장자를 `.txt`로 변경하여 텍스트 편집기에서 열 수 있습니다.

### 샘플 코드

다운로드한 마스터 파일을 파싱하여 종목 코드를 추출하는 방법은 아래의 공식 Python 샘플 코드를 참고할 수 있습니다.

- **KOSPI 샘플 코드:** [kis_kospi_code_mst.py](https://github.com/koreainvestment/open-trading-api/blob/main/stocks_info/kis_kospi_code_mst.py)
- **KOSDAQ 샘플 코드:** [kis_kosdaq_code_mst.py](https://github.com/koreainvestment/open-trading-api/blob/main/stocks_info/kis_kosdaq_code_mst.py)

---

## 2. 국내주식 시가총액 상위 조회 API (참고용)

**주의: 이 API는 최대 30건만 조회 가능하여 프로젝트의 주 API로 사용하기에 부적합합니다. 대신 아래의 `3. 종목조건검색 API`를 사용해야 합니다.**

- **API ID:** `FHPST01740000`
- **API 경로:** `/uapi/domestic-stock/v1/ranking/market-cap`

### Endpoint

- **Method:** `GET`
- **URL:** `https://openapi.koreainvestment.com:9443/uapi/domestic-stock/v1/ranking/market-cap`

### Headers

| Key             | Value                               |
| --------------- | ----------------------------------- |
| `authorization` | `Bearer {ACCESS_TOKEN}`             |
| `appkey`        | 발급받은 앱 키                        |
| `appsecret`     | 발급받은 앱 시크릿                    |
| `tr_id`         | `FHPST01740000`                     |

---

## 3. 종목조건검색 API

HTS에서 저장한 조건식에 해당하는 종목 리스트를 조회합니다. (최대 100건)

### 사용 절차

1.  **(선행) HTS에서 조건식 저장:** 한국투자증권 HTS `[0110]` 화면에서 원하는 조건(예: 시가총액 1000억 이상)을 만들어 '나의 조건'으로 서버에 저장합니다.
2.  **조건식 목록 조회:** `종목조건검색 목록조회` API (`/uapi/domestic-stock/v1/quotations/psearch-title`)를 호출하여 저장된 조건식들의 `seq`(고유번호)와 `group_name`을 확인합니다.
3.  **조건식 결과 조회:** 아래의 `종목조건검색조회` API를 사용하여 2번에서 얻은 `seq` 값으로 해당 조건에 맞는 종목 리스트를 조회합니다.

### API 명세: 종목조건검색조회

- **API ID:** `HHKST03900400`
- **API 경로:** `/uapi/domestic-stock/v1/quotations/psearch-result`

#### Endpoint

- **Method:** `GET`
- **URL:** `https://openapi.koreainvestment.com:9443/uapi/domestic-stock/v1/quotations/psearch-result`

#### Headers

| Key             | Value                               |
| --------------- | ----------------------------------- |
| `authorization` | `Bearer {ACCESS_TOKEN}`             |
| `appkey`        | 발급받은 앱 키                        |
| `appsecret`     | 발급받은 앱 시크릿                    |
| `tr_id`         | `HHKST03900400`                     |

#### Query Parameters

| Parameter | 값 (예시) | 설명                                               |
| --------- | --------- | -------------------------------------------------- |
| `user_id` | (HTS ID)  | 사용자 HTS ID                                      |
| `seq`     | `0`       | `종목조건검색 목록조회` API로 확인한 사용자조건 키값 |

#### Response Body (output2)

응답의 `output2` 배열에 각 종목의 정보가 객체 형태로 포함됩니다.

| Key    | 한글명   | 설명                 |
| ------ | -------- | -------------------- |
| `code` | 종목코드 | 종목 코드 (예: 005930) |
| `name` | 종목명   | 종목명 (예: 삼성전자)  |
| `price`| 현재가   | 현재 가격            |