---
title: 지원되는 분류 목록
description: 이 페이지에는 Azure Purview에서 지원되는 시스템 분류가 나열되어 있습니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: reference
ms.date: 09/27/2021
ms.openlocfilehash: edd43fc34a1b94c3d389670c7417ea9123d1586f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214734"
---
# <a name="supported-classifications-in-azure-purview"></a>Azure Purview에서 지원되는 분류

이 문서에서는 Azure 부서의 범위에서 지원 되 고 정의 된 시스템 분류를 나열 합니다.


- **고유 데이터 임계값**: 스캐너가 데이터 패턴을 실행하기 전에 열에서 찾아야 하는 고유 데이터 값의 총 수입니다. 고유 데이터 임계값은 패턴 일치와 관련이 없지만 패턴 일치를 위한 필수 조건입니다. 시스템 분류 규칙에 따라 각 열에는 분류를 위해 최소 8개의 고유 값이 있어야 합니다. 스캐너가 정확히 분류할 수 있을 만큼 충분한 데이터가 열에 포함되었는지 확인하기 위해, 시스템에서 이 값이 필요합니다. 예를 들어 값 1만 포함하는 여러 행이 있는 열은 분류되지 않습니다. 한 행에 값이 있고 나머지 행은 null 값인 열도 분류되지 않습니다. 여러 패턴을 지정할 경우 이 값이 각각의 패턴에 적용됩니다.

- **최소 일치 임계값**: 분류를 적용하기 위해 스캐너로 검색되어야 하는 열에서 일치하는 데이터 값의 최소 백분율입니다. 시스템 분류 값은 60%로 설정됩니다.


## <a name="defined-system-classifications"></a>정의된 시스템 분류

Azure Purview는 [RegEx](https://wikipedia.org/wiki/Regular_expression) 및 [블룸 필터](https://wikipedia.org/wiki/Bloom_filter)로 데이터를 분류합니다. 다음 목록에서는 Azure Purview 정의된 시스템 분류의 형식, 패턴 및 키워드에 대해 설명합니다.

각 분류 이름에는 MICROSOFT라는 접두사가 붙습니다.

## <a name="bloom-filter-classifications"></a>블룸 필터 분류

## <a name="city-country-and-place"></a>도시, 국가 및 장소

도시, 국가 및 장소 필터는 데이터 준비에 사용할 수 있는 최상의 데이터 세트를 사용하여 준비되었습니다.

## <a name="person"></a>사람

사람 블룸 필터는 아래 두 가지 데이터 세트를 사용하여 준비되었습니다.

- [성에 대한 2010 미국 인구 조사(162-K 항목)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [인기있는 아기 이름(SSN), 1880-2019년 모두 사용(98-K 항목)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>RegEx 분류

## <a name="aba-routing-number"></a>ABA 라우팅 번호

### <a name="format"></a>서식

서식이 지정 되거나 지정 되지 않은 패턴에 있을 수 있는 9 자리 숫자

### <a name="pattern"></a>무늬

- 00-12, 21-32, 61-72 또는 80 범위의 두 자릿수
- 2자리 숫자
- 선택적 하이픈
- 4자리 숫자
- 선택적 하이픈
- 1자리 숫자


### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

- aba 번호
- aba #
- aba
- abarouting #
- abaroutingnumber
- americanbankassociationrouting #
- americanbankassociationroutingnumber
- bankrouting #
- bankroutingnumber
- 라우팅이 #
- 라우팅 안 함
- 라우팅 번호
- 라우팅 전송 번호
- 라우팅이 #
- RTN

## <a name="argentina-national-identity-dni-number"></a>아르헨티나 신분증(DNI) 번호

### <a name="format"></a>형식

마침표가 있거나 없는 8자리 숫자

### <a name="pattern"></a>패턴

8자리 숫자:
- 2자리 숫자
- 점(선택 사항)
- 3자리 숫자
- 점(선택 사항)
- 3자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

- 아르헨티나 국가 Id 번호
- cedula
- cédula
- dni
- documento nacional de identidad
- documento número
- documento numero
- registro nacional de las 가상 사용자
- rnp

## <a name="australia-bank-account-number"></a>오스트레일리아 은행 계좌 번호

### <a name="format"></a>형식

뱅크 상태 분기 번호를 사용 하거나 사용 하지 않는 6 ~ 10 자리

### <a name="pattern"></a>무늬

계정 번호는 6 ~ 10 자리입니다.

오스트레일리아 BSB(Bank State Branch) 번호:
- 3자리 숫자
- 하이픈
- 3자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

- swift 은행 코드
- 대응 bank
- 기본 통화
- usa 계정
- 홀더 주소
- 은행 주소
- 정보 계정
- 기금 전송
- 은행 요금
- 은행 정보
- 은행 정보
- 전체 이름
- Iaea

## <a name="australia-business-number"></a>오스트레일리아 회사 전화 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security


### <a name="format"></a>서식

11자리 숫자(선택적 구분 기호 포함)

### <a name="pattern"></a>패턴

11자리 숫자(선택적 구분 기호 포함):

- 2자리 숫자
- 선택적 하이픈 또는 공백
- 3자리 숫자
- 선택적 하이픈 또는 공백
- 3자리 숫자
- 선택적 하이픈 또는 공백
- 3자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_australia_business_number"></a>Keyword_australia_business_number

- 오스트레일리아 비즈니스 번호
- 비즈니스 번호
- Abn #
- businessid #
- 비즈니스 ID
- Abn
- businessno #

## <a name="australia-company-number"></a>오스트레일리아 회사 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

9자리 숫자(구분 기호 포함)

### <a name="pattern"></a>패턴

9자리 숫자(구분 기호 포함):

- 3자리 숫자
- 공백
- 3자리 숫자
- 공백
- 3자리 숫자


### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_australia_company_number"></a>Keyword_australia_company_number

- Cna
- 오스트레일리아 회사 아니요
- 오스트레일리아 회사 아니요 #
- 오스트레일리아 회사 번호
- 오스트레일리아 회사 아니요
- 오스트레일리아 회사 아니요 #
- 오스트레일리아 회사 번호

## <a name="australia-drivers-license-number"></a>오스트레일리아 운전 면허 번호

### <a name="format"></a>형식

9개의 문자와 숫자

### <a name="pattern"></a>패턴

9개의 문자와 숫자:

- 2자리 숫자 또는 문자(대/소문자 구분 안 함)
- 2자리 숫자
- 5자리 숫자 또는 문자(대/소문자 구분 안 함)

또는

- 1~2개의 선택적 문자(대/소문자 구분 안 함)
- 4~9자리 숫자

또는

- 9자리 숫자 또는 문자(대/소문자 구분 안 함)

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

- 국제 주행 허가
- 오스트레일리아 자동차 연결
- 국제 주행 허가
- DriverLicence
- DriverLicences
- Driver Lic
- 드라이버 라이선스
- 드라이버 라이선스
- DriversLic
- DriversLicence
- DriversLicences
- Drivers Lic
- 드라이버Lics
- 드라이버 라이선스
- 드라이버 라이선스
- Driver'Lic
- Driver'Lics
- 드라이버 라이선스
- 드라이버 라이선스
- Driver' Lic
- 드라이버의 Lics
- 드라이버 라이선스
- 드라이버 라이선스
- Driver'sLic
- Driver'sLics
- Driver'sLicence
- 드라이버의Licences
- Driver's Lic
- 드라이버의 Lics
- 드라이버 라이선스
- 운전 기사 라이선스
- DriverLic #
- DriverLics #
- DriverLicence #
- DriverLicences #
- Driver Lic #
- 드라이버 Lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- DriversLic #
- DriversLics #
- DriversLicence #
- DriversLicences #
- Drivers Lic #
- 드라이버Lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- Driver'Lic #
- Driver'Lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- Driver' Lic #
- 드라이버의 Lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- Driver'sLic #
- Driver'sLics #
- Driver'sLicence #
- 드라이버의Licences #
- Driver's Lic #
- 드라이버의 Lics #
- 드라이버 라이선스 #
- 운전 기사 라이선스 #

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

- aaa
- DriverLicense
- DriverLicenses
- 드라이버 라이선스
- 드라이버 라이선스
- DriversLicense
- DriversLicenses
- 드라이버 라이선스
- 드라이버 라이선스
- 운전면허
- 운전면허
- 운전면허
- 운전 기사 라이선스
- 드라이버의License
- 드라이버의License
- 운전면허
- 운전면허증
- DriverLicense #
- DriverLicenses #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- DriversLicense #
- DriversLicenses #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전면허 #
- 운전면허 #
- 운전면허 #
- 운전 기사 라이선스 #
- 드라이버의License #
- 드라이버의License #
- 운전면허 #
- 운전면허증 #

## <a name="australia-medical-account-number"></a>오스트레일리아 의료 계좌 번호

### <a name="format"></a>형식

10-11자리 숫자

### <a name="pattern"></a>패턴

10-11자리 숫자:
- 첫 번째 숫자는 2-6 범위입니다.
- 9자리는 확인 숫자입니다.
- 10번째 숫자는 문제 숫자입니다.
- 11번째 숫자(선택 사항)는 개별 숫자입니다.

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_australia_medical_account_number"></a>Keyword_Australia_Medical_Account_Number

- 은행 계좌 세부 정보
- 납입액
- (2)
- 은행 지불
- 정보 분기
- 신용 카드 대출
- 인적 서비스 부서
- 로컬 서비스
- 메디케어


## <a name="australia-passport-number"></a>오스트레일리아 여권 번호

### <a name="format"></a>형식

8자 또는 9자 영숫자

### <a name="pattern"></a>무늬

- 한 문자(N, E, D, F, A, C, U, X) 뒤에 7자리 또는
- 두 문자 (PA, PB, PC, PD, PE, PF, PU, PW, PX, PZ) 뒤에 7 자리가 있습니다.

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_australia_passport_number"></a>Keyword_australia_passport_number

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- 여권 번호
- passport 세부 정보
- immigration 및 참여
- 오스트레일리아
- immigration의 부서
- 국가 id 카드
- 여행 문서
- 발급 기관


## <a name="australia-tax-file-number"></a>오스트레일리아 세금 파일 번호

### <a name="format"></a>형식

8~9자리 숫자

### <a name="pattern"></a>패턴

공백과 함께 표시되는 8~9자리 숫자, 일반적으로 다음과 같이 표시됨:
- 3자리 숫자
- 공백(선택 사항)
- 3자리 숫자
- 공백(선택 사항)
- 2~3자리 숫자, 마지막 숫자는 확인 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_australia_tax_file_number"></a>Keyword_australia_tax_file_number

- 오스트레일리아 비즈니스 번호
- 한계 세율
- medicare 징 집
- 포트폴리오 번호
- 서비스 veterans
- 원천 세금
- 개별 세금 반환
- 세금 파일 번호
- tfn

## <a name="austria-drivers-license-number"></a>오스트리아 드라이버의 라이선스 번호

### <a name="format"></a>형식

공백 및 구분 기호가 없는 8자리 숫자

### <a name="pattern"></a>패턴

8자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- 드라이버 라이선스
- driverslicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- drivers 라이선스
- drivers 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- 드라이버 (o) #
- 드라이버 ' s 라이선스 ' #
- 드라이버 ' s 라이선스 ' #
- driver'slicence #
- driver'slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 추진 라이선스 
- 추진 라이선스
- dlno #
- 드라이브 lic
- 드라이브 licen
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이버 licen
- 드라이버 licen
- 드라이버의 licen
- lic 구동
- 주행
- 운전 라이선스
- 추진 라이선스
- 주행 라이선스
- 촉진 허용
- dl 아니요
- dlno
- dl 번호


#### <a name="keywords_austria_eu_drivers_license_number"></a>Keywords_austria_eu_driver ' s_license_number

- fuhrerschein
- führerschein
- Führerscheine
- Führerscheinnummer
- Führerscheinnummern

## <a name="austria-identity-card"></a>오스트리아 신분증
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

문자, 숫자 및 특수 문자의 24자 조합

### <a name="pattern"></a>패턴

24자:

-  22자(대/소문자 구분 안 함), 숫자, 백슬래시, 슬래시 또는 더하기 기호

- 2자(대/소문자 구분 안 함), 숫자, 백슬래시, 슬래시, 더하기 기호 또는 등호

### <a name="checksum"></a>체크섬

해당 없음

### <a name="keywords"></a>키워드

#### <a name="keywords_austria_eu_national_id_card"></a>Keywords_austria_eu_national_id_card

- id 번호
- 국가 id
- personalausweis republik österreich

## <a name="austria-passport-number"></a>오스트리아 여권 번호

### <a name="format"></a>서식

1개 문자 뒤에 선택적 공백 및 7자리 숫자

### <a name="pattern"></a>패턴

1개 문자, 7자리 숫자 및 1개 공백의 조합:

- 1개 문자(대/소문자 구분 안 함)
- 1개 공백(선택 사항)
- 7자리 숫자

### <a name="checksum"></a>체크섬

적용할 수 없음

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- 여권 번호

#### <a name="keywords_austria_eu_passport_number"></a>Keywords_austria_eu_passport_number

- reisepassnummer
- reisepasse
- No-Reisepass
- Nr-Reisepass
- Reisepass-Nr
- Passnummer
- reisepässe

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜

## <a name="austria-social-security-number"></a>오스트리아 사회 보장 번호

### <a name="format"></a>서식

지정된 형식의 10자리 숫자

### <a name="pattern"></a>패턴

10자리 숫자:

- 일련 번호에 해당하는 3자리 숫자
- 1자리 확인 번호
- 생년월일에 해당하는 6자리 숫자(DDMMYY)

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_austria_eu_ssn_or_equivalent"></a>Keywords_austria_eu_ssn_or_equivalent

- 오스트리아 ssn
- ehic 번호
- ehic
- 보험 코드
- insurancecode #
- 보험 번호
- 보험 없음
- krankenkassennummer
- krankenversicherung
- 사회 alsecurityno
- 사회 alsecurityno #
- 소셜 보안 아니요
- 사회 보장 번호
- 소셜 보안 코드
- sozialversicherungsnummer
- sozialversicherungsnummer #
- soziale sicherheit kein
- sozialesicherheitkein #
- ssn #
- ssn
- versicherungscode
- versicherungsnummer
- zdravstveno zavarovanje

## <a name="austria-tax-identification-number"></a>오스트리아 납세자 번호

### <a name="format"></a>서식

9자리 숫자(선택적 하이픈 및 슬래시 포함)

### <a name="pattern"></a>패턴

9자리 숫자(선택적 하이픈 및 슬래시 포함):

- 2자리 숫자
- 하이픈(선택 사항)
- 3자리 숫자
- 슬래시(선택 사항)
- 4자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_austria_eu_tax_file_number"></a>Keywords_austria_eu_tax_file_number

- österreich
- st.nr.
- steuernummer
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #
- 세금 번호

## <a name="austria-value-added-tax"></a>징수 부가가치세
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

11자 영숫자 패턴

### <a name="pattern"></a>패턴

11자 영숫자 패턴:

- A 또는 a
- T 또는 t
- 선택적 공백
- U 또는 u
- 선택적 공백
- 2~3자리 숫자
- 선택적 공백
- 4자리 숫자
- 선택적 공백
- 1~2자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_austria_value_added_tax"></a>Keyword_austria_value_added_tax

- vat 번호
- 부가 가치세 #
- vat vat number
- vat 아니요.
- vatno #
- 부가가치세 번호
- vat vat
- mwst
- umsatzsteuernummer
- mwstnummer
- ust.-identifikationsnummer
- umsatzsteuer-identifikationsnummer
- vat ID 번호
- atu number
- uid 번호

## <a name="belgium-drivers-license-number"></a>벨기에 운전 면허 번호

### <a name="format"></a>형식

10자리 숫자, 공백 및 구분 기호 없음

### <a name="pattern"></a>패턴

10개의 자릿수

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- 드라이버
- driverlicense
- driverlicense
- driverlicence
- driverlicences
- driver lic
- 드라이버 lics
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'lic
- driver'lics
- 드라이버 라이선스
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- driver' lic
- 드라이버의 lics
- 운전면허증
- 운전 기사 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'slic
- driver'slics
- 드라이버의license
- 드라이버의 slicenses
- 드라이버의licence
- 드라이버의 slicences
- 드라이버의 lic
- 드라이버의 lic
- 운전면허증
- 운전 기사의 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- Dl #
- Dl #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- 드라이버 (o) #
- 드라이버 ' s 라이선스 ' #
- 드라이버 ' s 라이선스 ' #
- driver'slicence #
- driver'slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 추진 라이선스 
- 추진 라이선스
- dlno #
- 드라이브 lic
- 드라이브 licen
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이버 licen
- 드라이버 licen
- 드라이버의 licen
- lic 구동
- 주행
- 운전 라이선스
- 추진 라이선스
- 주행 라이선스
- 촉진 허용
- dl 아니요
- dlno
- dl 번호

#### <a name="keywords_belgium_eu_drivers_license_number"></a>Keywords_belgium_eu_driver ' s_license_number

- rijbewijs
- rijbewijsnummer
- führerschein
- führerscheinnummer
- füehrerscheinnummer
- fuhrerschein
- fuehrerschein
- fuhrerscheinnummer
- fuehrerscheinnummer
- permis de conduire
- numéro permis conduire


## <a name="belgium-national-number"></a>벨기에 신원 번호

### <a name="format"></a>형식

11자리 숫자와 선택적 구분 기호

### <a name="pattern"></a>패턴

11자리 숫자와 구분 기호:
- 6자리 숫자와 선택적 마침표 2개, YY.MM.DD 형식의 생년월일
- 점, 대시, 공백 등의 선택적 구분 기호
- 연속 3자리 숫자(남성은 홀수, 여성은 짝수)
- 점, 대시, 공백 등의 선택적 구분 기호
- 2자리 확인 숫자

### <a name="checksum"></a>체크섬

Yes


### <a name="keywords"></a>키워드

#### <a name="keyword_belgium_national_number"></a>Keyword_belgium_national_number

- belasting aantal
- bnn #
- bnn
- 맞춤형 d'identité
- identifiant 국가
- identifiantnational #
- identificatie
- identification
- identifikation
- identifikationsnummer
- identifizierung
- identité
- identiteit
- identiteit skart
- identity
- 비문
- 국가 번호
- national register
- nationalnumber #
- nationalnumber
- nif #
- nif
- numéro d'assué
- numéro de registre national
- numéro de sécurité
- numéro d'identification
- numéro d'immatriculation
- numéro national
- numéronational #
- 개인 ID 번호
- personalaus
- personalidnumber #
- 등록자
- 등록
- registrationsnumme
- registrierung
- 사회 보장 번호
- Ssn #
- Ssn
- steuernummer
- 세금 ID
- 세금 식별 번호
- 세금 ID 번호
- 세금 번호 #
- 세금 번호
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin no
- 주석 #

## <a name="belgium-passport-number"></a>2016년 10월 12월

### <a name="format"></a>서식

2개 문자 뒤에 6자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

2개 문자 뒤에 6자리 숫자

### <a name="checksum"></a>체크섬

적용할 수 없음

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- 여권 #
- 여권 #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keywords_belgium_eu_passport_number"></a>Keywords_belgium_eu_passport_number

- numéro passeport
- paspoort nr
- paspoort-nr
- paspoortnummer
- paspoortnummers
- 패스포트 카트
- Passeport livre
- Pass-Nr
- Passnummer
- reisepass kein

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜

## <a name="belgium-value-added-tax-number"></a>10진수 부가가치세 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

12자 영숫자 패턴

### <a name="pattern"></a>패턴

12자 영숫자 패턴:

- B 또는 b 문자
- E 또는 e 문자
- 0의 1자리 숫자
- 1~9의 1자리 숫자
- 선택적 점 또는 하이픈 또는 공백
- 4자리 숫자
- 선택적 점 또는 하이픈 또는 공백
- 4자리 숫자


### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_belgium_value_added_tax_number"></a>Keyword_belgium_value_added_tax_number

- n) tva
- vat 번호
- vat 아니요
- numéro t.v.a
- umsatzsteuer-identifikationsnummer
- umsatzsteuernummer
- Btw
- Btw #
- 부가 가치세 #


## <a name="brazil-cpf-number"></a>브라질 CPF 번호

### <a name="format"></a>형식

11자리 숫자, 확인 숫자 포함, 형식이 있거나 없을 수 있음

### <a name="pattern"></a>패턴

형식 있음:
- 3자리 숫자
- 마침표
- 3자리 숫자
- 마침표
- 3자리 숫자
- 하이픈
- 확인 숫자인 두 자리

서식 없음:
- 11자리 숫자, 마지막 2자리는 확인 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_brazil_cpf"></a>Keyword_brazil_cpf

- CPF
- Identification(식별)
- 등록
- 수익
- Cadastro de Pessoas F cassicas
- Imposto
- Identificaçço
- Inscriçço
- Receita


## <a name="brazil-legal-entity-number-cnpj"></a>브라질 법인 번호(CNPJ)

### <a name="format"></a>형식

14자리 숫자, 등록 번호, 지점 번호 및 확인 번호와 구분 기호 포함

### <a name="pattern"></a>패턴

14자리 숫자 및 구분 기호:

- 2자리 숫자
- 마침표
- 3자리 숫자
- 마침표
- 3자리 숫자(처음 8자리는 등록 번호)
- 슬래시(/)
- 4자리 분기 번호
- 하이픈
- 확인 숫자인 두 자리

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_brazil_cnpj"></a>Keyword_brazil_cnpj

- CNPJ
- CNPJ/MF
- CNPJ-MF
- 법인의 국가 레지스트리
- Taxpayers Registry
- 법인
- 법인
- 등록 상태
- 비즈니스
- 회사
- CNPJ
- Cadastro Nacional da Pessoa Jur을dica
- Cadastro된 De Contribuintes
- CGC
- Pessoa jur교차
- Pessoas jur쓰기
- Situaço cadastral
- Inscriçço
- Empresa


## <a name="brazil-national-identification-card-rg"></a>브라질 국가 신분증(RG)

### <a name="format"></a>형식

Registro 레지스트리(이전 형식): 9자리

Registro de Identidade(RIC)(새 형식): 11자리

### <a name="pattern"></a>무늬

Registro 레지스트리(이전 형식):
- 2자리 숫자
- 마침표
- 3자리 숫자
- 마침표
- 3자리 숫자
- 하이픈
- 확인 숫자인 한 자리

Registro de Identidade (RIC)(새 형식):
- 10개의 자릿수
- 하이픈
- 확인 숫자인 한 자리

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_brazil_rg"></a>Keyword_brazil_rg

- Cédula de identidade
- id 카드
- 국가 id
- número de rregistro
- registro de Iidentidade
- registro geral
- RG (이 키워드는 대/소문자를 구분 함)
- RIC (이 키워드는 대/소문자를 구분 함)


## <a name="bulgaria-drivers-license-number"></a>불가리아 드라이버의 라이선스 번호

### <a name="format"></a>서식

9자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

9자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- 드라이버 라이선스
- driverslicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- drivers 라이선스
- drivers 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lic #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전 운전 라이선스 
- 운전 라이선스
- dlno #
- driv lic
- driv licen
- driv 라이선스
- driv 라이선스
- driv 라이선스
- driv 라이선스
- 드라이버 사용 허가
- 드라이버 라이선스
- 드라이버의 사용 허가
- driving lic
- 운전 허가
- 운전 라이선스
- 운전 운전 라이선스
- 운전 라이선스
- 운전 허가
- dl 아니요
- dlno
- dl number


#### <a name="keywords_bulgaria_eu_drivers_license_number"></a>Keywords_bulgaria_eu_driver's_license_number

- свидетелство за управление на мпс
- свидетелство за управление на моторно превозно средство
- сумпс
- шофьорска книжка
- шофьорски книжки

## <a name="bulgaria-uniform-civil-number"></a>1000001년 10월
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>형식

10자리 숫자, 공백 및 구분 기호 없음

### <a name="pattern"></a>패턴

10자리 숫자, 공백 및 구분 기호 없음

- 생년월일에 해당하는 6자리 숫자(YYMMDD)
- 출생 순위에 해당하는 2자리 숫자
- 성별에 해당하는 1자리 숫자(남성은 짝수, 여성은 홀수)
- 1자리 확인 번호

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_bulgaria_eu_national_id_card"></a>Keywords_bulgaria_eu_national_id_card

- bnn #
- bnn
- bucn #
- bucn
- edinenski nomer
- egn #
- egn
- ID 번호
- national id
- 국가 번호
- nationalnumber #
- nationalnumber
- 개인 ID
- 개인 번호
- 개인 번호
- personalidnumber #
- 사회 보장 번호
- Ssn #
- Ssn
- 균일한 시민 ID
- uniform uniform no
- uniform uniform number
- uniformci no #
- uniformci no
- uniformci #
- uniformci
- 고유 고유 고유의 고유 고유 번호
- егн #
- егн
- единен граждански номер
- идентификационен номер
- личен номер
- лична идентификация
- лично не
- национален номер
- номер на гражданството
- униформ ID
- униформ граждански ID
- униформ граждански не
- униформ граждански номер
- униформгражданскиid #
- униформгражданскине. #


## <a name="bulgaria-passport-number"></a>2016년 10월

### <a name="format"></a>서식

9자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

9자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- 여권 #
- 여권 #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keywords_bulgaria_eu_passport_number"></a>Keywords_bulgaria_eu_passport_number

- номер на паспорта
- номер на паспорт
- 을(를) 추가합니다.

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜

## <a name="canada-bank-account-number"></a>캐나다 은행 계좌 번호

### <a name="format"></a>형식

7 또는 12자리 숫자

### <a name="pattern"></a>패턴

캐나다 은행 계좌 번호는 7자리 또는 12자리입니다.

캐나다 은행 계좌 송금 코드 번호는 다음과 같습니다.
- 5자리 숫자
- 하이픈
- 3자리 숫자 또는
- 0 "0"
- 8자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_canada_bank_account_number"></a>Keyword_canada_bank_account_number

- 캐나다 절감액
- 캐나다 수익 기관
- 캐나다 금융 기관
- 직접 입금 양식
- 캐나다 시민
- 법률 담당자
- 공증인
- 선서에 대한 은(는)
- 자식 의료 혜택
- 범용 자식 관리
- 캐나다 자식 세금 혜택
- 소득 세금 혜택
- 판매세
- 사회 보험 번호
- 소득 세금 환불
- 자식 세금 혜택
- 납입액
- 기관 번호
- 입금 요청
- 은행 정보
- 직접 입금


## <a name="canada-drivers-license-number"></a>캐나다 운전 면허 번호

### <a name="format"></a>형식

지방 마다 다름

### <a name="pattern"></a>패턴

다음을 다루는 다양한 패턴:
- Alberta
- British Columbia
- Manitoba
- New Brunswick
- Newfoundland/Labr도구
- Nova Scotia
- Ontario
- Prince Edward Island
- Quebec
- Saskatchewan

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_province_name_drivers_license_name"></a>Keyword_[province_name]_drivers_license_name

- 지방 약어(예: AB)
- 지방 이름(예: 앨버타)

#### <a name="keyword_canada_drivers_license"></a>Keyword_canada_drivers_license

- DL
- DLS
- CDL
- CDLS
- DriverLic
- DriverLics
- DriverLicense
- DriverLicenses
- DriverLicence
- DriverLicences
- Driver Lic
- 드라이버 Lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- DriversLic
- DriversLics
- 드라이버 라이선스
- DriversLicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 Lic
- 드라이버 Lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- Drivero
- Driverlics
- Drivers 라이선스
- Drivers 라이선스
- Drivers 라이선스
- Driver'Licences
- 드라이버 ' Lic
- 드라이버의 Lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- Driver'sLic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- Driver'sLicence
- Driver'sLicences
- 드라이버의 Lic
- 드라이버의 Lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- Permis de Conduire
- id
- ids
- idcard 번호
- idcard 번호
- idcard #
- idcard #s
- idcard 카드
- idcard 카드
- idcard
- 식별 번호
- ID 번호
- 등록 #
- id #s
- 식별 카드
- 식별 카드
- identification
- DL #
- 된다 #
- CDL #
- CDLS #
- DriverLic #
- DriverLics #
- DriverLicense #
- DriverLicenses #
- DriverLicence #
- DriverLicences #
- 드라이버 Lic #
- 드라이버 Lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- DriversLic #
- DriversLics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- DriversLicences #
- 드라이버 Lic #
- 드라이버 Lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- Drivero #
- Driverlics #
- Drivers 라이선스 #
- Drivers 라이선스 #
- Drivers 라이선스 #
- Driver'Licences #
- 드라이버 ' Lic #
- 드라이버의 Lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- Driver'sLic #
- 드라이버 (o) #
- 드라이버 ' s 라이선스 ' #
- 드라이버 ' s 라이선스 ' #
- Driver'sLicence #
- Driver'sLicences #
- Driver's Lic #
- 드라이버의 Lics #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- Permis de Conduire #
- 자료 #
- Id #
- idcard card #
- idcard cards #
- idcard #
- ID 카드 #
- ID 카드 #
- 식별 #


## <a name="canada-health-service-number"></a>캐나다 의료 서비스 번호

### <a name="format"></a>형식

 10개의 자릿수

### <a name="pattern"></a>패턴

10개의 자릿수

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_canada_health_service_number"></a>Keyword_canada_health_service_number

- 개인 상태 번호
- 환자 정보
- 상태 관리 서비스
- 전문 서비스
- 자동차 사고
- 환자 병원
- 정신과 의사
- 작업자 보상
- 장애


## <a name="canada-passport-number"></a>캐나다 여권 번호

### <a name="format"></a>형식

대문자 2개 뒤에 6자리 숫자

### <a name="pattern"></a>패턴

대문자 2개 뒤에 6자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_canada_passport_number"></a>Keyword_canada_passport_number

- 캐나다 권주
- 캐나다 passport
- passport 애플리케이션
- Passport 사진
- 인증된 번역기
- 캐나다 시민
- 처리 시간
- 갱신 애플리케이션

#### <a name="keyword_passport"></a>Keyword_passport

- 여권 번호
- Passport 번호
- 여권 #
- 여권 #
- PassportID
- Passportno
- passportnumber
- パスポート
- パスポート番号
- ಖಖ絰
- パスポート＃
- Numéro de passeport
- Passeport n °
- Passeport Non
- Passeport #
- Passeport #
- PasseportNon
- Passeportn °


## <a name="canada-personal-health-identification-number-phin"></a>캐나다 PHIN(개인 건강 식별 번호)

### <a name="format"></a>형식

9자리 숫자

### <a name="pattern"></a>패턴

9자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_canada_phin"></a>Keyword_canada_phin

- 사회 보험 번호
- 상태 정보 행위
- 소득 세금 정보
- 매니토바 상태
- 상태 등록
- 2018년 3월
- 혜택 자격
- 개인 상태
- 강력한 전력
- 등록 번호
- 개인 상태 번호
- 전문가 추천
- wellness professional
- 환자 조회
- 상태 및 건강

#### <a name="keyword_canada_provinces"></a>Keyword_canada_provinces

- Nunavut
- Quebec
- Northwest Territories
- Ontario
- British Columbia
- Alberta
- Saskatchewan
- Manitoba
- Yukon
- Newfoundland and Labrador
- New Brunswick
- Nova Scotia
- Prince Edward Island
- 캐나다


## <a name="canada-social-insurance-number"></a>캐나다 사회 보험 번호

### <a name="format"></a>형식

하이픈 또는 공백을 선택적으로 사용하는 9 자리 숫자

### <a name="pattern"></a>패턴

형식 있음:
- 3자리 숫자
- 하이픈 또는 공백
- 3자리 숫자
- 하이픈 또는 공백
- 3자리 숫자

형식 없음: 9자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_sin"></a>Keyword_sin

- sin
- 사회 보험
- 다양한 d'assurance 소셜
- 죄
- Ssn
- ssns
- 사회 보장
- 수많은 d'assurance 소셜
- 국가별 ID 번호
- national id
- 죄 #
- soc ins
- 소셜 ins

#### <a name="keyword_sin_collaborative"></a>Keyword_sin_collaborative

- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 생년월일
- Birthdate
- Birthday
- Date of Birth

## <a name="chile-identity-card-number"></a>칠레 신분증 번호

### <a name="format"></a>형식

7~8자리 숫자와 구분 기호 확인 숫자 또는 문자

### <a name="pattern"></a>패턴

7~8자리 숫자와 구분 기호:
- 1~2자리 숫자
- 점(선택 사항)
- 3자리 숫자
- 점(선택 사항)
- 3자리 숫자
- 대시
- 한 자리 또는 문자(대/소문자 구분 안 함) 즉, 확인 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_chile_id_card"></a>Keyword_chile_id_card

- cédula de identidad
- identificación
- 국가별 식별
- 국가별 ID 번호
- national id
- número de identificación nacional
- rol único nacional
- rol único tributario
- 실행
- 상 습
- tar tar tara de identificación
- Rol Unico Nacional
- Rol Unico Tributario
- 실행 #
- 상 습 #
- nationaluniqueroleID #
- nacional identidad
- número identificación
- identidad número
- numero identificacion
- identidad numero
- 헝가리어 ID 아니요.
- 헝가리어 ID 번호
- 헝가리어 ID #
- 고유 세금 레지스트리
- 고유한 3부 역할
- 고유 세금 역할
- 고유한 3진수
- 고유 국가 번호
- 고유한 국가 역할
- 국가 고유 역할
- 실체가 없습니다.
- 헝가리어 ID 번호
- 헝가리어 ID #


## <a name="china-resident-identity-card-prc-number"></a>중국 중국 상주 ID 카드(PRC) 번호

### <a name="format"></a>형식

18자리 숫자

### <a name="pattern"></a>패턴

18자리 숫자:
- 주소 코드인 6자리 숫자
- 8자리 숫자(YYYYMMDD 형식의 생년월일)
- 주문 코드인 3자리 숫자
- 확인 숫자인 한 자리

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

### <a name="keyword_china_resident_id"></a>Keyword_china_resident_id

- 상주 ID 카드
- PRC
- 국가별 ID 카드
- 身份证
- 居民 身份证
- 居民身份证
- 鉴定
- 身分證
- 居民 身份證
- 鑑定


## <a name="credit-card-number"></a>신용 카드 번호

### <a name="format"></a>형식

형식이 지정되거나 형식이 지정되지 않을 수 있고(dddddd) Luhn 테스트를 통과해야 하는 14~16자리 숫자입니다.

### <a name="pattern"></a>무늬

표시, 마스터 카드, 검색 카드, JCB, American Express, 상품 카드, 징표 카드, Rupay 및 중국 UnionPay를 비롯한 전 세계 모든 주요 브랜드의 카드를 검색합니다.

### <a name="checksum"></a>체크섬

예, Luhn 체크섬

### <a name="keywords"></a>키워드

#### <a name="keyword_cc_verification"></a>Keyword_cc_verification

- 카드 확인
- 카드 ID
- Cvn
- cid
- cvc2
- cvv2
- 핀 블록
- 보안 코드
- 보안 번호
- security no
- 문제 번호
- 문제 없음
- cryptogramme
- numéro de sécurité
- numero de securite
- kreditkartenprüfnummer
- kreditkartenprufnummer
- prüfziffer
- prufziffer
- sicherheits Kode
- sicherheitscode
- sicherheitsnummer
- verfalldatum
- codice di verifica
- 사인. sicurezza
- cod sicurezza
- n autorizzazione
- código
- codigo
- 사인. seg
- cod seg
- código de segurança
- codigo de seguranca
- codigo de segurança
- código de seguranca
- cód. segurança
- 사인. seguranca
- 사인. segurança
- cód. seguranca
- cód segurança
- cod seguranca
- cod segurança
- cód seguranca
- número de verificaçço
- numero de verificacao
- ablauf
- gültig bis
- gültigkeitsdatum
- gultig bis
- gultigkeitsdatum
- scadenza
- data scad
- fecha de expiracion
- fecha de venc
- vencimiento
- vlido hasta
- valido hasta
- vto
- data de expiraçço
- data de expiracao
- data em que expira
- validade
- 용 맹
- vencimento
- 트랜잭션
- 트랜잭션 번호
- 참조 번호
- セキュリティコード
- セキュリティ コード
- セキュリティナンバー
- セキュリティ ナンバー
- セキュリティ番号

#### <a name="keyword_cc_name"></a>Keyword_cc_name

- amex
- 아메리카 익스프레스
- americanexpress
- americano espresso
- Visa
- mastercard
- 마스터 카드
- mc
- mastercards
- 마스터 카드
- diner의 클럽
- diners 클럽
- dinersclub
- 검색
- 카드 검색
- discovercard
- 카드 검색
- JCB
- BrandSmart
- 일본어 카드 기관
- 맞춤형 전권
- carteblanche
- 신용 카드
- 사람과 #
- 참조 #:
- 만료 날짜
- exp 날짜
- 만료 날짜
- 날짜 d'expiration
- 날짜 d'exp
- 만료 날짜
- 뱅크 카드
- bankcard
- 카드 번호
- 카드 번호
- 전화 번호
- 전화 번호
- 카드 번호
- creditcard
- 신용 카드
- creditcards
- ccn
- 카드 소유자
- 카드 소유자
- 카드 소유자
- 고 홀더
- 카드 확인
- checkcard
- 카드 확인
- checkcards
- 직불 카드
- debitcard
- 직불 카드
- debitcards
- atm 카드
- atmcard
- atm 카드
- atmcards
- 이동 중
- en 경로
- 카드 유형
- 회원 계정 계정
- 회원 계정
- 이상 번호
- 회사 카드
- 회사 카드
- 카드 유형
- 카드 계정 번호
- 카드 구성원 계정
- 회원 계정.
- 카드 번호.
- 카드 번호
- 카드 번호
- 맞춤형 bancaire
- 맞춤형 de crédit
- 맞춤형 de 크레딧
- numéro de 맞춤형
- numero de 맞춤형
- n º de la 맞춤형
- n º de 맞춤형
- kreditkarte
- karte
- karteninhaber
- karteninhabers
- kreditkarteninhaber
- kreditkarteninstitut
- kreditkartentyp
- eigentümername
- kartennr
- kartennummer
- kreditkartennummer
- kreditkarten-nummer
- carta di credito
- carta credito
- n. Carta
- n carta
- Nr. Carta
- nr carta
- 수많은 carta
- 다양한 della carta
- numero di carta
- tar도a credito
- tar tar tara de credito
- tar tar tara crédito
- tar tar tara de crédito
- tar tar tara de atm
- tar tar tara atm
- tar tar도a debito
- tar도a de debito
- tar tar tara débito
- tar tar tara de débito
- n) de tar도라
- 번호 de tar도라
- no de tar도마
- numero de tar도라
- número de tar도마
- tar tar tara no
- tar도라하비언트
- carto de crédito
- carto de credito
- cartao de crédito
- cartao de credito
- cartoo de débito
- cartao de débito
- carto de debito
- cartao de debito
- débito automético
- debito automatico
- número do cartúo
- numero do cart 연산자
- número do cartao
- numero do cartao
- número de cartúo
- numero de carto
- número de cartao
- numero de cartao
- n 은 카트를 수행합니다.
- n) do cartao
- n 입니다. do carto
- no do cart 연산자
- no do cartao
- 번호 do carto
- 번호 do cartao
- rupay
- union pay
- unionpay
- 은(는)
- 다 이너 스
- クレジットカード番号
- クレジットカードナンバー
- クレジットカード＃
- クレジットカード
- クレジット
- クレカ
- カード番号
- カードナンバー
- カード＃
- アメックス
- アメリカンエクスプレス
- アメリカン エクスプレス
- 2016년 3월
- 2016년 3월
- マスターカード
- マスター カード
- マスター
- ダイナースクラブ
- ダイナース クラブ
- ダイナース
- 有効期限
- 期限
- キャッシュカード
- キャッシュ カード
- カード名義人
- カードの名義人
- カードの名義
- デビット カード
- デビットカード
- 中国银联
- 银联



## <a name="croatia-drivers-license-number"></a>크로아티아 운전 면허 번호

### <a name="format"></a>형식

공백 및 구분 기호가 없는 8자리 숫자

### <a name="pattern"></a>패턴

8자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- 드라이버 라이선스
- driverslicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- drivers 라이선스
- drivers 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- 드라이버 (o) #
- 드라이버 ' s 라이선스 ' #
- 드라이버 ' s 라이선스 ' #
- driver'slicence #
- driver'slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 추진 라이선스 
- 추진 라이선스
- dlno #
- driv lic
- driv licen
- driv 라이선스
- driv 라이선스
- driv 라이선스
- driv 라이선스
- 드라이버 사용 허가
- 드라이버 라이선스
- 드라이버의 사용 허가
- driving lic
- 운전 허가
- 운전 라이선스
- 운전 운전 라이선스
- 운전 라이선스
- 운전 허가
- dl 아니요
- dlno
- dl number


#### <a name="keywords_croatia_eu_drivers_license_number"></a>Keywords_croatia_eu_driver's_license_number

- vozazka dozvola
- vozazvoke dozvole


## <a name="croatia-identity-card-number"></a>크로아티아 신분증 번호
이 엔터티는 EU 국가 식별 번호 중요 정보 형식에 포함됩니다. 독립 실행형 중요한 정보 형식 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

9자리 숫자

### <a name="pattern"></a>패턴

연속된 숫자 9개

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_croatia_id_card"></a>Keyword_croatia_id_card

- tvstorski broj graeana
- 마스터 시민 번호
- nacionalni identifikacijski broj
- 국가별 ID 번호
- oib #
- oib
- osobna iskaznica
- osobni ID
- osobni identifikacijski broj
- 개인 ID 번호
- broezni broj
- bunezni identifikacijski broj
- 세금 ID
- 세금 식별 번호
- 세금 ID 번호
- 세금 번호 #
- 세금 번호
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin no
- 주석 #


## <a name="croatia-passport-number"></a>크로아티아 여권 번호

### <a name="format"></a>서식

9자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

9자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- 여권 #
- 여권 #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keywords_croatia_eu_passport_number"></a>Keywords_croatia_eu_passport_number

- broj putovnice
- Br. Putovnice
- br putovnice

## <a name="croatia-personal-identification-oib-number"></a>크로아티아 개인 식별(OIB) 번호

### <a name="format"></a>형식

11자리 숫자

### <a name="pattern"></a>패턴

11자리 숫자:
- 10개의 자릿수
- 마지막 숫자는 확인 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_croatia_oib_number"></a>Keyword_croatia_oib_number

- tvstorski broj graeana
- 마스터 시민 번호
- nacionalni identifikacijski broj
- 국가별 ID 번호
- oib #
- 있는 oib
- osobna iskaznica
- osobni id
- osobni identifikacijski broj
- 개인 식별 번호
- porezni broj
- porezni identifikacijski broj
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #

## <a name="cyprus-drivers-license-number"></a>키프로스 드라이버 라이선스 번호

### <a name="format"></a>서식

12자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

12자리

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- 드라이버 라이선스
- driverslicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- drivers 라이선스
- drivers 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- drivers lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'lic #
- driver'lics #
- 드라이버 라이선스 #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver' lic #
- 드라이버의 lics #
- 운전면허증 #
- 운전 기사 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lic #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전 운전 라이선스 
- 운전 라이선스
- dlno #
- driv lic
- driv licen
- driv 라이선스
- driv 라이선스
- driv 라이선스
- driv 라이선스
- 드라이버 사용 허가
- 드라이버 라이선스
- 드라이버의 사용 허가
- driving lic
- 운전 허가
- 운전 라이선스
- 운전 운전 라이선스
- 운전 라이선스
- 운전 허가
- dl 아니요
- dlno
- dl number

#### <a name="keywords_cyprus_eu_drivers_license_number"></a>Keywords_cyprus_eu_driver's_license_number

- άδεια οδήγησης
- αριθμό άδειας οδήγησης
- άδειες οδήγησης


## <a name="cyprus-identity-card"></a>키프로스 신분증
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>형식

10자리 숫자, 공백 및 구분 기호 없음

### <a name="pattern"></a>패턴

10개의 자릿수

### <a name="checksum"></a>체크섬

적용할 수 없음

### <a name="keywords"></a>키워드

#### <a name="keywords_cyprus_eu_national_id_card"></a>Keywords_cyprus_eu_national_id_card

- ID 카드 번호
- ID 카드 번호
- karlik karti
- 국가별 ID 번호
- 개인 ID 번호
- ταυτοτητασ


## <a name="cyprus-passport-number"></a>키프로스 여권 번호

### <a name="format"></a>서식

1개 문자 뒤에 6~8자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

1개 문자 뒤에 6~8자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- 여권 #
- 여권 #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keywords_cyprus_eu_passport_number"></a>Keywords_cyprus_eu_passport_number

- αριθμό διαβατηρίου
- pasaportu
- Αριθμός Διαβατηρίου
- κυπριακό διαβατήριο
- διαβατήριο #
- διαβατήριο
- αριθμός διαβατηρίου
- Kimliği aport
- numarası aport
- 필요 합니다.
- Αρ. Διαβατηρίου

#### <a name="keywords_cyprus_eu_passport_date"></a>Keywords_cyprus_eu_passport_date

- 만료 날짜
- 발급 날짜


## <a name="cyprus-tax-identification-number"></a>키프로스 납세자 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

지정된 패턴의 8자리 숫자 및 1개 문자

### <a name="pattern"></a>패턴

8자리 숫자 및 1개 문자:

- "0" 또는 "9"
- 7자리 숫자
- 1개 문자(대/소문자 구분 안 함)

### <a name="checksum"></a>체크섬

적용할 수 없음

### <a name="keywords"></a>키워드

#### <a name="keywords_cyprus_eu_tax_file_number"></a>Keywords_cyprus_eu_tax_file_number

- 세금 id
- 세금 식별 코드
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- 3 목 #
- 3 목
- tin id
- tin
- tin #
- vergi kimlik kodu
- vergi kimlik numarası
- αριθμός φορολογικού μητρώου
- κωδικός φορολογικού μητρώου
- φορολογική ταυτότητα
- φορολογικού κωδικού


## <a name="czech-republic-drivers-license-number"></a>체코 공화국 운전 면허 번호

### <a name="format"></a>서식

2개 문자 뒤에 6자리 숫자

### <a name="pattern"></a>패턴

8개 문자 및 숫자:

- 'E' 문자(대/소문자 구분 안 함)
- 1개 문자
- 공백(선택 사항)
- 6자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- 드라이버 라이선스
- driverslicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- drivers 라이선스
- drivers 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- 드라이버 (o) #
- 드라이버 ' s 라이선스 ' #
- 드라이버 ' s 라이선스 ' #
- driver'slicence #
- driver'slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 추진 라이선스 
- 추진 라이선스
- dlno #
- 드라이브 lic
- 드라이브 licen
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이버 licen
- 드라이버 licen
- 드라이버의 licen
- lic 구동
- 주행
- 운전 라이선스
- 추진 라이선스
- 주행 라이선스
- 촉진 허용
- dl 아니요
- dlno
- dl 번호

#### <a name="keywords_czech_republic_eu_drivers_license_number"></a>Keywords_czech_republic_eu_driver ' s_license_number

- řidičský prúkaz
- řidičské průkazy
- číslo řidičského průkazu
- čísla řidičských průkazů


## <a name="czech-passport-number"></a>체코어 passport 번호

### <a name="format"></a>서식

8자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

8자리 숫자(공백 또는 구분 기호 비포함)

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keywords_czech_republic_eu_passport_number"></a>Keywords_czech_republic_eu_passport_number

- cestovn을 파스
- 을(를) 파스로
- cestovn지 pasu
- passeport 아니요
- 을(를) 파스(pasu)

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="czech-national-identity-card-number"></a>체코어 국가 ID 카드 번호

### <a name="format"></a>서식

선택적 슬래시(이전 형식)가 있는 9자리 10자리 슬래시(새 형식)

### <a name="pattern"></a>무늬

9자리(이전 형식):
- 생년월일을 나타내는 6자리 숫자
- 선택적 슬래시
- 3자리 숫자

10자리(새 형식):
- 생년월일을 나타내는 6자리 숫자
- 선택적 슬래시
- 마지막 숫자가 확인 숫자인 4자리

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_czech_id_card"></a>Keyword_czech_id_card

- 생년월일 번호
- 체코 공화국 ID
- 체코어idno #
- daéové 을(를)
- identifika의 을(를) 식별합니다.
- ID 번호
- ID 번호
- identityno #
- identityno
- 보험 번호
- 국가별 ID 번호
- nationalnumber #
- 국가 번호
- osobn 번째 을(를)
- personalidnumber #
- 개인 ID 번호
- 개인 ID 번호
- 개인 번호
- Pid #
- pid
- pojijit의 경우 을(를) 둡니다.
- r이(가)
- rodne cislo
- rodné 을(를)
- Ssn
- Ssn #
- 사회 보장 번호
- 세금 ID
- 세금 식별 번호
- 세금 ID 번호
- 세금 번호 #
- 세금 번호
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin no
- 주석 #
- 고유 ID 번호


## <a name="denmark-drivers-license-number"></a>덴마크 드라이버의 라이선스 번호

### <a name="format"></a>형식

공백 및 구분 기호가 없는 8자리 숫자

### <a name="pattern"></a>패턴

8자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- 드라이버
- driverlicense
- driverlicense
- driverlicence
- driverlicences
- driver lic
- 드라이버 lics
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- 드라이버 라이선스
- driverslicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- drivers 라이선스
- drivers 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- 드라이버 (o) #
- 드라이버 ' s 라이선스 ' #
- 드라이버 ' s 라이선스 ' #
- driver'slicence #
- driver'slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 추진 라이선스 
- 추진 라이선스
- dlno #
- 드라이브 lic
- 드라이브 licen
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이버 licen
- 드라이버 licen
- 드라이버의 licen
- lic 구동
- 주행
- 운전 라이선스
- 추진 라이선스
- 주행 라이선스
- 촉진 허용
- dl 아니요
- dlno
- dl 번호

#### <a name="keywords_denmark_eu_drivers_license_number"></a>Keywords_denmark_eu_driver ' s_license_number

- kørekort
- kørekortnummer


## <a name="denmark-passport-number"></a>덴마크 여권 번호

### <a name="format"></a>서식

9자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

9자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- 여권 번호

#### <a name="keywords_denmark_eu_passport_number"></a>Keywords_denmark_eu_passport_number

- pasnummer
- 포트 n °
- 고 numre

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="denmark-personal-identification-number"></a>덴마크 개인 식별 번호

### <a name="format"></a>형식

10자리 숫자, 하이픈 포함

### <a name="pattern"></a>패턴

10자리 숫자:
- 6자리 숫자(DDMMYY 형식의 생년월일)
- 하이픈
- 4자리 숫자, 마지막 숫자는 확인 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_denmark_id"></a>Keyword_denmark_id

- centrale personregister
- civilt registreringssystem
- cpr.log
- cpr.log #
- gesundheitskarte nummer
- gesundheitsversicherungkarte nummer
- 상태 카드
- 의료 보험 카드 번호
- 의료 보험 수
- 식별 번호
- identifikationsnummer
- identifikationsnummer #
- id 번호
- krankenkassennummer
- nationalid #
- nationalnumber #
- 국가 번호
- personalidnumber #
- personalidentityno #
- 개인 id 번호
- personnummer
- personnummer #
- reisekrankenversicherungskartenummer
- rejsesygesikringskort
- ssn
- ssn #
- 고가 id
- kode
- nummer
- skattenummer
- 사회 보장 번호
- sundhedsforsikringskort
- sundhedsforsikringsnummer
- sundhed, ort
- sundhedskortnummer
- sygesikring
- sygesikringkortnummer
- 세금 코드
- 여행 건강 보험 카드
- uniqueidentityno #
- 세금 번호
- 세금 등록 번호
- 세금 id
- 세금 식별 번호
- taxid #
- taxnumber #
- 세금 없음
- taxno #
- taxnumber
- 세금 식별 번호
- tin #
- taxidno #
- taxidnumber #
- 세금 없음 #
- tin id
- tin no
- cpr.nr
- cprnr
- cprnummer
- personnr
- personregister
- sygesikringsbevis
- sygesikringsbevisnr
- sygesikringsbevisnummer
- sygesikringskort
- sygesikringskortnr
- sygesikringskortnummer
- sygesikringsnr
- sygesikringsnummer


## <a name="drug-enforcement-agency-dea-number"></a>DEA(약품 적용 기관) 번호

### <a name="format"></a>형식

2개 문자 뒤에 7자리 숫자

### <a name="pattern"></a>패턴

패턴은 다음을 모두 포함해야 합니다.
- 가능한 이 문자 집합의 한 문자(대/소문자 구분 안 함) : 등록자 코드인 abcdefghjklmnprstux
- 등록자의 성 또는 숫자 '9'의 첫 글자인 한 문자(대/소문자 구분 안 함)
- 7자리 숫자이며, 마지막 숫자는 확인 숫자입니다.

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_dea_number"></a>Keyword_dea_number

- Dea
- Dea #
- 약품 적용 관리
- 약품 적용 기관


## <a name="estonia-drivers-license-number"></a>트럭 기사의 라이선스 번호

### <a name="format"></a>서식

2개 문자 뒤에 6자리 숫자

### <a name="pattern"></a>패턴

2개 문자 및 6자리 숫자:

- "ET" 문자(대/소문자 구분 안 함)
- 6자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- 드라이버
- driverlicense
- driverlicense
- driverlicence
- driverlicences
- driver lic
- 드라이버 lics
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'lic
- driver'lics
- 드라이버 라이선스
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- driver' lic
- 드라이버의 lics
- 운전면허증
- 운전 기사 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'slic
- driver'slics
- 드라이버의license
- 드라이버의 slicenses
- 드라이버의licence
- 드라이버의 slicences
- 드라이버의 lic
- 드라이버의 lic
- 운전면허증
- 운전 기사의 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- Dl #
- Dl #
- driverlic #
- 드라이버 #
- driverlicense #
- driverlicense #
- driverlicence #
- driverlicences #
- driver lic #
- 드라이버 lics #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- driverslicense #
- driverslicenses #
- driverslicence #
- driverslicences #
- drivers lic #
- drivers lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'lic #
- driver'lics #
- 운전면허증 #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver' lic #
- 드라이버의 lics #
- 운전면허증 #
- 운전 기사 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lic #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전 운전 라이선스 
- 운전 라이선스
- dlno #
- driv lic
- driv licen
- driv 라이선스
- driv 라이선스
- driv 라이선스
- driv 라이선스
- 드라이버 사용 허가
- 드라이버 라이선스
- 드라이버의 사용 허가
- driving lic
- 운전 허가
- 운전 라이선스
- 운전 운전 라이선스
- 운전 라이선스
- 운전 허가
- dl 아니요
- dlno
- dl number

#### <a name="keywords_estonia_eu_drivers_license_number"></a>Keywords_estonia_eu_driver's_license_number

-- permis de conduire
- juubade(주부바데)
- juoaoa number
- Juubauba


## <a name="estonia-personal-identification-code"></a>규약 개인 식별 코드
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

11자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

11자리 숫자:

- 성별 및 출생 세기에 해당하는 1자리 숫자(홀수는 남성, 짝수는 여성, 1~2는 19세기, 3~4는 20세기, 5~6은 21세기)
- 생년월일에 해당하는 6자리 숫자(YYMMDD)
- 같은 날짜에 출생한 사람을 구분하는 일련 번호에 해당하는 3자리 숫자
- 1자리 확인 번호

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_estonia_eu_national_id_card"></a>Keywords_estonia_eu_national_id_card

- id-kaart
- Ik
- isikukood #
- isikukood
- maksu id
- maksukohustuslase identifitseerimisnumber
- maksunumber
- 국가별 ID 번호
- 국가 번호
- 개인 코드
- 개인 ID 번호
- 개인 식별 코드
- 개인 ID 번호
- personalidnumber #
- 세금 ID
- 세금 식별 번호
- 세금 ID 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #


## <a name="estonia-passport-number"></a>에스토니아 passport 번호

### <a name="format"></a>서식

1개 문자 뒤에 7자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

1개 문자 뒤에 7자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- 여권 번호

#### <a name="keywords_estonia_eu_passport_number"></a>Keywords_estonia_eu_passport_number

- eesti kodaniku pass
- 가 수
- passinumbrid
- 문서 번호
- 문서 번호
- dokumendi nr

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="eu-debit-card-number"></a>EU 직불 카드 번호

### <a name="format"></a>형식

16자리

### <a name="pattern"></a>패턴

복잡하고 견고한 패턴

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_eu_debit_card"></a>Keyword_eu_debit_card

- 계정 번호
- 카드 번호
- 카드 번호.
- 보안 번호
- 사람과 #

#### <a name="keyword_card_terms_dict"></a>Keyword_card_terms_dict

- 계정 nbr
- 계정 번호
- 계정 없음
- 아메리카 익스프레스
- americanexpress
- americano espresso
- amex
- atm 카드
- atm 카드
- atm kaart
- atmcard
- atmcards
- atmkaart
- atmkaarten
- bancontact
- 뱅크 카드
- bankkaart
- 카드 소유자
- 카드 소유자
- 카드 번호
- 카드 번호
- 카드 번호
- 카드 유형
- cardano numerico
- 카드 소유자
- 고 홀더
- 전화 번호
- 전화 번호
- carta biit
- carta credito
- carta di credito
- cartao de credito
- cartao de crédito
- cartao de
- cartao de débito
- 맞춤형 bancaire
- 맞춤형 전권
- 맞춤형 bleue
- 맞춤형 de 크레딧
- 맞춤형 de crédit
- 맞춤형 di credito
- carteblanche
- cartão de credito
- cartão de crédito
- cartão de
- cartão de débito
- cb
- ccn
- 카드 확인
- 카드 확인
- checkcard
- checkcards
- chequekaart
- cirrus
- cirrus-edc-maestro
- controlekaart
- controlekaarten
- 신용 카드
- 신용 카드
- creditcard
- creditcards
- debetkaart
- debetkaarten
- 직불 카드
- 직불 카드
- debitcard
- debitcards
- automatico
- diners 클럽
- dinersclub
- 검색
- 카드 검색
- 카드 검색
- discovercard
- discovercards
- débito automático
- edc
- eigentümername
- 유럽 직불 카드
- hoofdkaart
- hoofdkaarten
- viaggid o에서
- 일본어 카드 기관
- japanse kaartdienst
- jcb
- kaart
- kaart num
- kaartaantal
- kaartaantallen
- kaarthouder
- kaarthouders
- karte
- karteninhaber
- karteninhabers
- kartennr
- kartennummer
- kreditkarte
- kreditkarten-nummer
- kreditkarteninhaber
- kreditkarteninstitut
- kreditkartennummer
- kreditkartentyp
- maestro
- 마스터 카드
- 마스터 카드
- mastercard
- mastercards
- mc
- mister 현금
- n carta
- carta
- de tarjeta 없음
- 안 함 cartao
- no do cartão
- 번호 de tarjeta
- 번호 cartao
- 번호 cartão do
- nr carta
- nr. carta
- numeri di scheda
- numero carta
- numero de
- numero de 맞춤형
- numero de cartão
- numero de tarjeta
- numero della carta
- numero di carta
- numero di scheda
- numero do caro
- numero do cartão
- numéro de 맞춤형
- n º 카 ta
- n º de 맞춤형
- n º de la 맞춤형
- n º de tarjeta
- n º do caro
- n º do cartão
- n º. cartão do
- número de
- número de cartão
- número de tarjeta
- número do caro
- scheda dell'assegno
- scheda dell'atmosfera
- scheda dell'atmosfera
- scheda della bit
- scheda di controllo
- scheda di debito
- scheda matrice
- schede dell'atmosfera
- schede di controllo
- schede di debito
- schede matrici
- scoprono la scheda
- scoprono le schede
- 모바일
- supporti di scheda
- supporto di scheda
- switch
- tarjeta atm
- tarjeta credito
- tarjeta de atm
- tarjeta de credito
- tarjeta de
- tarjeta debito
- tarjeta
- tarjetahabiente
- tipo della scheda
- ufficio gid apponese della
- scheda
- v 종 량 제
- v-지불
- visa
- 및
- 전자
- visto
- visum
- vpay 량 제

#### <a name="keyword_card_security_terms_dict"></a>Keyword_card_security_terms_dict

- 카드 id 번호
- 카드 확인
- cardi la verifica
- cid
- cod seg
- cod seguranca
- cod segurança
- cod sicurezza
- cod. seg
- cod. seguranca
- cod. segurança
- cod. sicurezza
- codice di sicurezza
- codice di verifica
- codigo
- codigo de seguranca
- codigo de segurança
- crittogramma
- cryptogram
- cryptogramme
- cv2
- cvc
- cvc2
- cvn
- cvv
- cvv2
- cód seguranca
- cód segurança
- cód. seguranca
- cód. segurança
- código
- código de seguranca
- código de segurança
- de kaart controle
- geeft nr uit
- 문제 없음
- 문제 번호
- kaartidentificatienummer
- kreditkartenprufnummer
- kreditkartenprüfnummer
- kwestieaantal
- 번호 dell'edizione
- 번호 di sicurezza
- numero de securite
- numero de verificacao
- numero dell'edizione
- numero di identificazione della
- scheda
- numero di sicurezza
- numero van veiligheid
- numéro de sécurité
- n º autorizzazione
- número de verificação
- perno il blocco
- 블록 고정
- prufziffer
- prüfziffer
- 보안 코드
- 보안 아니요
- 보안 번호
- sicherheits kode
- sicherheitscode
- sicherheitsnummer
- speldblok
- veiligheid nr
- veiligheidsaantal
- veiligheidscode
- veiligheidsnummer
- verfalldatum

#### <a name="keyword_card_expiration_terms_dict"></a>Keyword_card_expiration_terms_dict

- ablauf
- 데이터 de expiracao
- 데이터 de expiração
- 데이터 삭제 exp
- 데이터 di exp
- 데이터 di scadenza
- data em 쿼리 expira
- 데이터 scad
- 데이터 scadenza
- 날짜 제거
- 데이텀 afloop
- 데이텀 van exp
- de 루프
- espira
- espira
- exp 날짜
- exp 데이텀
- expiration
- 임박한
- 기간이
- expiry
- fecha de expiracion
- fecha de venc
- gultig bis
- gultigkeitsdatum
- gültig bis
- gültigkeitsdatum
- la scadenza
- scadenza
- valable
- 유효한 ade
- valido hasta
- valor
- venc
- vencimento
- vencimiento
- verloopt
- vervaldag
- vervaldatum
- vto
- válido hasta


## <a name="eu-drivers-license-number"></a>EU 운전 면허 번호

이러한 엔터티는 EU 드라이버의 라이선스 번호에 있으며 중요 한 정보 형식입니다.

- [오스트리아](#austria-drivers-license-number)
- [벨기에](#belgium-drivers-license-number)
- [불가리아](#bulgaria-drivers-license-number)
- [크로아티아](#croatia-drivers-license-number)
- [키프로스](#cyprus-drivers-license-number)
- [체코어](#czech-republic-drivers-license-number)
- [덴마크](#denmark-drivers-license-number)
- [에스토니아](#estonia-drivers-license-number)
- [핀란드](#finland-drivers-license-number)
- [프랑스](#france-drivers-license-number)
- [독일](#germany-drivers-license-number)
- [그리스](#greece-drivers-license-number)
- [헝가리](#hungary-drivers-license-number)
- [아일랜드](#ireland-drivers-license-number)
- [이탈리아](#italy-drivers-license-number)
- [라트비아](#latvia-drivers-license-number)
- [리투아니아](#lithuania-drivers-license-number)
- [룩셈부르크](#luxemburg-drivers-license-number)
- [몰타](#malta-drivers-license-number)
- [네덜란드](#netherlands-drivers-license-number)
- [폴란드](#poland-drivers-license-number)
- [포르투갈](#portugal-drivers-license-number)
- [루마니아](#romania-drivers-license-number)
- [슬로바키아](#slovakia-drivers-license-number)
- [슬로베니아](#slovenia-drivers-license-number)
- [스페인](#spain-drivers-license-number)
- [스웨덴](#sweden-drivers-license-number)
- [영국](#uk-drivers-license-number)


## <a name="eu-national-identification-number"></a>EU 국가 식별 번호

이러한 엔터티는 EU 국가 식별 번호 이며 중요 한 정보 유형입니다.

- [오스트리아](#austria-identity-card)
- [벨기에](#belgium-national-number)
- [불가리아](#bulgaria-uniform-civil-number)
- [크로아티아](#croatia-identity-card-number)
- [키프로스](#cyprus-identity-card)
- [체코어](#czech-national-identity-card-number)
- [덴마크](#denmark-personal-identification-number)
- [에스토니아](#estonia-personal-identification-code)
- [핀란드](#finland-national-id)
- [프랑스](#france-national-id-card-cni)
- [독일](#germany-identity-card-number)
- [그리스](#greece-national-id-card)
- [헝가리](#hungary-personal-identification-number)
- [아일랜드](#ireland-personal-public-service-pps-number)
- [이탈리아](#italy-fiscal-code)
- [라트비아](#latvia-personal-code)
- [리투아니아](#lithuania-personal-code)
- [룩셈부르크](#luxemburg-national-identification-number-natural-persons)
- [몰타](#malta-identity-card-number)
- [네덜란드](#netherlands-citizens-service-bsn-number)
- [폴란드](#poland-national-id-pesel)
- [포르투갈](#portugal-citizen-card-number)
- [루마니아](#romania-personal-numeric-code-cnp)
- [슬로바키아](#slovakia-personal-number)
- [슬로베니아](#slovenia-unique-master-citizen-number)
- [스페인](#spain-dni)
- [영국](#uk-national-insurance-number-nino)


## <a name="eu-passport-number"></a>EU 여권 번호

이러한 엔터티는 EU Passport 번호에 있으며 중요한 정보 유형입니다. 이러한 엔터티는 EU Passport 번호 번들에 있습니다.

- [오스트리아](#austria-passport-number)
- [벨기에](#belgium-passport-number)
- [불가리아](#bulgaria-passport-number)
- [크로아티아](#croatia-passport-number)
- [키프로스](#cyprus-passport-number)
- [체코어](#czech-passport-number)
- [덴마크](#denmark-passport-number)
- [에스토니아](#estonia-passport-number)
- [핀란드](#finland-passport-number)
- [프랑스](#france-passport-number)
- [독일](#germany-passport-number)
- [그리스](#greece-passport-number)
- [헝가리](#hungary-passport-number)
- [아일랜드](#ireland-passport-number)
- [이탈리아](#italy-passport-number)
- [라트비아](#latvia-passport-number)
- [리투아니아](#lithuania-passport-number)
- [룩셈부르크](#luxemburg-passport-number)
- [몰타](#malta-passport-number)
- [네덜란드](#netherlands-passport-number)
- [폴란드](#poland-passport-number)
- [포르투갈](#portugal-passport-number)
- [루마니아](#romania-passport-number)
- [슬로바키아](#slovakia-passport-number)
- [슬로베니아](#slovenia-passport-number)
- [스페인](#spain-passport-number)
- [스웨덴](#sweden-passport-number)
- [영국](#us--uk-passport-number)


## <a name="eu-social-security-number-or-equivalent-identification"></a>EU SSN(사회 보장 번호) 또는 동등한 ID

이러한 엔터티는 EU 사회 보장 번호 또는 이와 동등한 ID에 있으며 중요한 정보 유형입니다.

- [오스트리아](#austria-social-security-number)
- [벨기에](#belgium-national-number)
- [크로아티아](#croatia-personal-identification-oib-number)
- [체코어](#czech-national-identity-card-number)
- [덴마크](#denmark-personal-identification-number)
- [핀란드](#finland-national-id)
- [프랑스](#france-social-security-number-insee)
- [독일](#germany-identity-card-number)
- [그리스](#greece-national-id-card)
- [헝가리](#hungary-social-security-number-taj)
- [포르투갈](#portugal-citizen-card-number)
- [스페인](#spain-social-security-number-ssn)
- [스웨덴](#sweden-national-id)


## <a name="eu-tax-identification-number"></a>EU TIN(세금 식별 번호)

EU TIN(세금 식별 번호) 중요 정보 유형의 엔터티입니다.

- [오스트리아](#austria-tax-identification-number)
- [벨기에](#belgium-national-number)
- [불가리아](#bulgaria-uniform-civil-number)
- [크로아티아](#croatia-identity-card-number)
- [키프로스](#cyprus-tax-identification-number)
- [체코어](#czech-national-identity-card-number)
- [덴마크](#denmark-personal-identification-number)
- [에스토니아](#estonia-personal-identification-code)
- [핀란드](#finland-national-id)
- [프랑스](#france-tax-identification-number)
- [독일](#germany-tax-identification-number)
- [그리스](#greece-tax-identification-number)
- [헝가리](#hungary-tax-identification-number)
- [아일랜드](#ireland-personal-public-service-pps-number)
- [이탈리아](#italy-fiscal-code)
- [라트비아](#latvia-personal-code)
- [리투아니아](#lithuania-personal-code)
- [룩셈부르크](#luxemburg-national-identification-number-non-natural-persons)
- [몰타](#malta-tax-identification-number)
- [네덜란드](#netherlands-tax-identification-number)
- [폴란드](#poland-tax-identification-number)
- [포르투갈](#portugal-tax-identification-number)
- [루마니아](#romania-personal-numeric-code-cnp)
- [슬로바키아](#slovakia-personal-number)
- [슬로베니아](#slovenia-tax-identification-number)
- [스페인](#spain-tax-identification-number)
- [스웨덴](#sweden-tax-identification-number)
- [영국](#uk-unique-taxpayer-reference-number)


## <a name="finland-drivers-license-number"></a>핀란드 기사의 라이선스 번호

### <a name="format"></a>형식

10자리 숫자, 하이픈 포함

### <a name="pattern"></a>패턴

10자리 숫자(하이픈 포함):

- 6자리 숫자
- 하이픈
- 3자리 숫자
- 1자리 숫자 또는 문자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- 드라이버
- driverlicense
- driverlicense
- driverlicence
- driverlicences
- driver lic
- 드라이버 lics
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'lic
- driver'lics
- 드라이버 라이선스
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- driver' lic
- 드라이버의 lics
- 운전면허증
- 운전 기사 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'slic
- driver'slics
- 드라이버의license
- 드라이버의 slicenses
- 드라이버의licence
- 드라이버의 slicences
- 드라이버의 lic
- 드라이버의 lic
- 운전면허증
- 운전 기사의 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- Dl #
- Dl #
- driverlic #
- 드라이버 #
- driverlicense #
- driverlicense #
- driverlicence #
- driverlicences #
- driver lic #
- 드라이버 lics #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- driverslicense #
- driverslicenses #
- driverslicence #
- driverslicences #
- drivers lic #
- drivers lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'lic #
- driver'lics #
- 드라이버 라이선스 #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver' lic #
- 드라이버의 lics #
- 운전면허증 #
- 운전 기사 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 추진 라이선스 
- 추진 라이선스
- dlno #
- 드라이브 lic
- 드라이브 licen
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이버 licen
- 드라이버 licen
- 드라이버의 licen
- lic 구동
- 주행
- 운전 라이선스
- 추진 라이선스
- 주행 라이선스
- 촉진 허용
- dl 아니요
- dlno
- dl 번호


#### <a name="keywords_finland_eu_drivers_license_number"></a>Keywords_finland_eu_driver ' s_license_number

- ajokortti
- permis de conduire
- ajokortin numero
- kuljettaja lic.
- körkort
- körkortnummer
- förare lic.
- ajokortit
- ajokortin numerot


## <a name="finland-european-health-insurance-number"></a>핀란드 유럽 건강 보험 수
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

20자리 숫자

### <a name="pattern"></a>패턴

20 자리 숫자:

- 10자리 숫자 - 8024680246
- 선택적 공백 또는 하이픈
- 10개의 자릿수

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_finland_european_health_insurance_number"></a>Keyword_finland_european_health_insurance_number

- ehic #
- ehic
- finlandehicnumber #
- finsjukförsäkringskort a
- 상태 카드
- 의료 보험 카드
- 의료 보험 수
- hälsokort
- sairaanhoitokortin
- sairausvakuutuskortti
- sairausvakuutusnumero
- sjukförsäkring nummer
- sjukförsäkringskort
- suomen sairausvakuutuskortti
- terveyskortti


## <a name="finland-national-id"></a>핀란드 신분증

### <a name="format"></a>형식

6자리 숫자 + 세기를 나타내는 문자 + 3자리 숫자 + 확인 숫자

### <a name="pattern"></a>패턴

패턴은 다음을 모두 포함해야 합니다.
- 6자리 숫자(DDMMYY 형식의 생년월일)
- 세기 표식('-', '+' 또는 'a')
- 3자리 개인 식별 번호
- 1자리 숫자 또는 문자(대/소문자 구분 안 함), 확인 숫자임

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

- ainutlaatuinen henkilökohtainen tunnus
- henkilökohtainen tunnus
- henkilötunnus
- henkilötunnusnumero #
- henkilötunnusnumero
- hetu
- id 번호
- id 번호
- 식별 번호
- identiteetti numero
- id 번호
- idnumber
- kansallinen henkilötunnus
- kansallisen henkilökortin
- 국가 id 카드
- 국가 id 아니요.
- 개인 id
- 개인 id 코드
- personalidnumber #
- personbeteckning
- personnummer
- 사회 보장 번호
- sosiaaliturvatunnus
- 세금 ID
- 세금 식별 번호
- 세금 ID 번호
- 세금 번호 #
- 세금 번호
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin no
- 주석 #
- tunnistenumero
- tunnus numero
- tunnusluku
- tunnusnumero
- ver vertti
- veronumero
- verotunniste
- verotunnus


## <a name="finland-passport-number"></a>핀란드 여권 번호

이 엔터티는 EU Passport 번호 중요 정보 유형에서 사용할 수 있으며 독립 실행형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식
9개 문자와 숫자의 조합

### <a name="pattern"></a>패턴
9개 문자와 숫자의 조합:
- 두 문자(대/소문자 구분 안 함)
- 7자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- 여권 #
- 여권 #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keyword_finland_passport_number"></a>Keyword_finland_passport_number

- suomalainen passi
- passin numero
- passin numero. #
- passin numero #
- passin numero.
- passi #
- passi 번호

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜

## <a name="france-drivers-license-number"></a>프랑스 운전 면허 번호

이 엔터티는 EU 드라이버의 라이선스 번호 중요한 정보 유형에서 사용할 수 있으며 독립 실행형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

12자리

### <a name="pattern"></a>패턴

12자리, 프랑스 전화 번호와 같은 비슷한 패턴을 할인하는 유효성 검사 포함

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_french_drivers_license"></a>Keyword_french_drivers_license

- driverlic
- 드라이버
- driverlicense
- driverlicense
- driverlicence
- driverlicences
- driver lic
- 드라이버 lics
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'lic
- driver'lics
- 드라이버 라이선스
- 운전면허증
- 드라이버 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- 드라이버 (o) #
- 드라이버 ' s 라이선스 ' #
- 드라이버 ' s 라이선스 ' #
- driver'slicence #
- driver'slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 추진 라이선스 
- 추진 라이선스
- dlno #
- 드라이브 lic
- 드라이브 licen
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이버 licen
- 드라이버 licen
- 드라이버의 licen
- lic 구동
- 주행
- 운전 라이선스
- 추진 라이선스
- 주행 라이선스
- 촉진 허용
- dl 아니요
- dlno
- dl 번호
- permis de conduire
- 라이선스 번호
- 라이선스 번호
- 라이선스 번호
- 라이선스 번호
- numéros de 라이선스


## <a name="france-health-insurance-number"></a>프랑스 건강 보험 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

21자리 숫자

### <a name="pattern"></a>패턴

21자리 숫자:

- 10개의 자릿수
- 공백(선택 사항)
- 10개의 자릿수
- 공백(선택 사항)
- 1자리 숫자


### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_france_health_insurance_number"></a>Keyword_France_health_insurance_number

- 보험 카드
- 맞춤형 vitale
- 맞춤형 d'assuré 소셜


## <a name="france-national-id-card-cni"></a>프랑스 국가 id 카드 (CNI)

### <a name="format"></a>형식

12자리

### <a name="pattern"></a>패턴

12자리

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_france_eu_national_id_card"></a>Keywords_france_eu_national_id_card

- 카드 번호
- 맞춤형 nationale d'identité
- 맞춤형 nationale d'idenite no
- cni #
- cni
- compte bancaire
- 국가 식별 번호
- 국가 id
- nationalidno #
- numéro d'assurance maladie
- numéro de 맞춤형 vitale


## <a name="france-passport-number"></a>프랑스 여권 번호
이 엔터티는 EU Passport 번호 중요 정보 형식으로 제공 됩니다. 독립 실행형 중요 한 정보 형식 엔터티로도 사용할 수 있습니다.

### <a name="format"></a>형식

9개의 숫자와 문자

### <a name="pattern"></a>패턴

9개의 숫자와 문자:
- 2자리 숫자
- 두 문자 (대/소문자 구분 안 함)
- 5자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- 여권 번호

#### <a name="keywords_france_eu_passport_number"></a>Keywords_france_eu_passport_number

- numéro de 포트
- 포트 n °
- 가 아닌 포트
- 포트 #
- 포트 #
- 고가 아닌 포트
- 고가
- français
- livre
- 맞춤형
- numéro
- 포트 n °
- n ° du 및 seport
- n °의 포트

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="france-social-security-number-insee"></a>프랑스 주민 등록 번호 (INSEE)

### <a name="format"></a>형식

15개의 자릿수

### <a name="pattern"></a>패턴

다음 두 패턴 중 하나와 일치해야 합니다.
- 13 개 숫자 뒤에 공백 하나, 숫자 2 개<br/>
또는
- 연속된 숫자 15개

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_fr_insee"></a>Keyword_fr_insee

- 코드 sécu
- d'identité nationale
- insee
- fssn #
- le numéro d'identification nationale
- le 코드 de la sécurité 사회 ale
- 국가 id
- 국가 식별
- d'identité 없음
- 번호 d'identité
- numéro d'assurance
- numéro d'identité
- numero d'identite
- numéro de sécu
- numéro de sécurité 사회 ale
- d'identite 없음
- 번호 d'identite
- ssn
- ssn #
- sécurité 사회 ale
- securité 사회 ale
- securite 사회 ale
- 보안
- 사회 보장 번호
- 소셜 보안 코드
- 사회 보험 번호


## <a name="france-tax-identification-number"></a>프랑스 세금 식별 번호

### <a name="format"></a>형식

13자리

### <a name="pattern"></a>패턴

13자리

- 1자리 숫자(0, 1, 2 또는 3 이어야 함)
- 1자리 숫자
- 1개 공백(선택 사항)
- 2자리 숫자
- 1개 공백(선택 사항)
- 3자리 숫자
- 1개 공백(선택 사항)
- 3자리 숫자
- 1개 공백(선택 사항)
- 3자리 확인 번호


### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_france_eu_tax_file_number"></a>Keywords_france_eu_tax_file_number

- numéro d'identification fiscale
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #


## <a name="france-value-added-tax-number"></a>프랑스 값 부가 세금 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

13자 영숫자 패턴

### <a name="pattern"></a>패턴

13자 영숫자 패턴:

- 2 개의 문자-FR (대/소문자 구분 안 함)
- 선택적 공백 또는 하이픈
- 2개 문자 또는 숫자
- 선택적 공백, 점, 하이픈 또는 쉼표
- 3자리 숫자
- 선택적 공백, 점, 하이픈 또는 쉼표
- 3자리 숫자
- 선택적 공백, 점, 하이픈 또는 쉼표
- 3자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_france_value_added_tax_number"></a>Keyword_France_value_added_tax_number

- vat 번호
- vat 아니요
- vat #
- 부가 가치 세금
- 사이렌 id 없음 numéro d'identification taxe 성 valeur ajoutée
- taxe valeur ajoutée
- taxe 성 la valeur ajoutée
- n ° tva
- numéro de
- numéro d'identification 사이렌


## <a name="germany-drivers-license-number"></a>독일 운전 면허 번호

이 중요 한 정보 형식 엔터티는 EU 드라이버의 라이선스 번호 중요 정보 형식에 포함 되어 있습니다. 독립 실행형 중요 한 정보 형식 엔터티로도 사용할 수 있습니다.

### <a name="format"></a>형식

11개 문자와 숫자의 조합

### <a name="pattern"></a>패턴

11 자리 숫자 및 문자 (대/소문자 구분 안 함):
- 1자리 숫자 또는 문자
- 2자리 숫자
- 6자리 숫자 또는 문자
- 1자리 숫자
- 1자리 숫자 또는 문자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_german_drivers_license_number"></a>Keyword_german_drivers_license_number

- ausstel도스datum
- ausstel
- ausstellende behöde
- ausstellende behorde
- ausstellende behoerde
- führerschein
- fuhrerschein
- fuehrerschein
- führerscheinnummer
- fuhrerscheinnummer
- fuehrerscheinnummer
- führerschein- 
- fuhrerschein- 
- fuehrerschein- 
- führerscheinnummernr
- fuhrerscheinnummernr
- fuehrerscheinnummernr
- führerscheinnummerklasse
- fuhrerscheinnummerklasse
- fuehrerscheinnummerklasse
- nr-führerschein
- nr-fuhrerschein
- nr-fuehrerschein
- no-führerschein
- no-fuhrerschein
- no-fuehrerschein
- n-führerschein
- n-fuhrerschein
- n-fuehrerschein
- permis de conduire
- driverlic
- 드라이버
- driverlicense
- driverlicense
- driverlicence
- driverlicences
- driver lic
- 드라이버 lics
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'lic
- driver'lics
- 드라이버 라이선스
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버의 lic
- 드라이버의 lics
- 운전면허
- 운전 기사 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'slic
- driver'slics
- 드라이버의license
- 드라이버의 slicenses
- 드라이버의licence
- 드라이버의 slicences
- 드라이버의 lic
- 드라이버의 lic
- 운전면허증
- 운전 기사의 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- Dl #
- Dl #
- driverlic #
- 드라이버 #
- driverlicense #
- driverlicense #
- driverlicence #
- driverlicences #
- driver lic #
- 드라이버 lics #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- driverslicense #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- 드라이버 (o) #
- 드라이버 ' s 라이선스 ' #
- 드라이버 ' s 라이선스 ' #
- driver'slicence #
- driver'slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 추진 라이선스 
- 추진 라이선스
- dlno #
- 드라이브 lic
- 드라이브 licen
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이버 licen
- 드라이버 licen
- 드라이버의 licen
- lic 구동
- 주행
- 운전 라이선스
- 추진 라이선스
- 주행 라이선스
- 촉진 허용
- dlno


## <a name="germany-identity-card-number"></a>독일 신분증 번호

### <a name="format"></a>형식

2010년 11월 1일 이후: 9개의 문자와 숫자

1987년 4월 1일부터 2010년 10월 31일까지: 10 자리 숫자

### <a name="pattern"></a>패턴

2010년 11월 1일 이후:
- 1개 문자(대/소문자 구분 안 함)
- 8자리 숫자

1987년 4월 1일부터 2010년 10월 31일까지:
- 10개의 자릿수

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_germany_id_card"></a>Keyword_germany_id_card

- ausweis
- gpid
- identification
- identifikation
- identifizierungsnummer
- id 카드
- id 번호
- id-nummer
- 개인 id
- personalausweis
- persönliche id nummer
- persönliche identifikationsnummer
- persönliche-id-nummer


## <a name="germany-passport-number"></a>독일 여권 번호

이 엔터티는 EU Passport 번호 중요 정보 형식에 포함 되어 있으며 독립 실행형 중요 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

10자리 숫자 또는 문자

### <a name="pattern"></a>패턴

패턴은 다음을 모두 포함해야 합니다.
- 첫 번째 문자는 이 세트(C, F, G, H, J, K)의 숫자 또는 문자
- 3자리 숫자
- 이 집합(C, -H, J-N, P, R, T, V-Z)의 5자리 숫자 또는 문자
- 1자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_german_passport"></a>Keyword_german_passport

- reisepasse
- reisepassnummer
- No-Reisepass
- Nr-Reisepass
- Reisepass-Nr
- Passnummer
- reisepässe
- 는 포트 번호입니다.
- 는 포트 번호

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- 여권 번호


## <a name="germany-tax-identification-number"></a>독일 세금 식별 번호

### <a name="format"></a>서식

11자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

11자리 숫자

- 2자리 숫자
- 선택적 공백
- 3자리 숫자
- 선택적 공백
- 3자리 숫자
- 선택적 공백
- 2자리 숫자
- 1자리 확인 번호

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_germany_eu_tax_file_number"></a>Keywords_germany_eu_tax_file_number

- identifikationsnummer
- steuer id
- steueridentifikationsnummer
- steuernummer
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #
- zinn #
- zinn
- zinnnummer


## <a name="germany-value-added-tax-number"></a>독일 부가 세금 번호를 추가 했습니다.
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

11자 영숫자 패턴

### <a name="pattern"></a>패턴

11자 영숫자 패턴:

- D 또는 d 문자
- E 또는 e 문자
- 공백(선택 사항)
- 3자리 숫자
- 선택적 공백 또는 쉼표
- 3자리 숫자
- 선택적 공백 또는 쉼표
- 3자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_germany_value_added_tax_number"></a>Keyword_germany_value_added_tax_number

- vat 번호
- vat 아니요
- vat #
- vat # mehrwertsteuer
- mwst
- mehrwertsteuer identifikationsnummer
- mehrwertsteuer nummer


## <a name="greece-drivers-license-number"></a>그리스 운전 면허 번호

이 엔터티는 EU 드라이버의 라이선스 번호 중요 정보 형식에 포함 되어 있습니다. 또한 독립 실행형 중요 한 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>서식

9자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

9자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- driver lic
- 드라이버 lics
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'lic
- driver'lics
- 드라이버 라이선스
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- driver' lic
- 드라이버의 lics
- 운전면허증
- 운전 기사 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'slic
- driver'slics
- 드라이버의license
- 드라이버의 slicenses
- 드라이버의licence
- 드라이버의 slicences
- 드라이버의 lic
- 드라이버의 lic
- 운전면허증
- 운전 기사의 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- Dl #
- Dl #
- driverlic #
- 드라이버 #
- driverlicense #
- driverlicense #
- driverlicence #
- driverlicences #
- driver lic #
- 드라이버 lics #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- driverslicense #
- driverslicenses #
- driverslicence #
- driverslicences #
- drivers lic #
- drivers lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'lic #
- driver'lics #
- 드라이버 라이선스 #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver' lic #
- 드라이버의 lics #
- 운전면허증 #
- 운전 기사 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lic #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전 운전 라이선스 
- 운전 라이선스
- dlno #
- driv lic
- driv licen
- driv 라이선스
- driv 라이선스
- driv 라이선스
- driv 라이선스
- 드라이버 사용 허가
- 드라이버 라이선스
- 드라이버의 사용 허가
- driving lic
- 운전 허가
- 운전 라이선스
- 운전 운전 라이선스
- 운전 라이선스
- 운전 허가
- dl 아니요
- dlno
- dl number


#### <a name="keywords_greece_eu_drivers_license_number"></a>Keywords_greece_eu_driver's_license_number

- δεια οδήγησης
- Adeia
- Άδεια οδήγησης
- Δίπλωμα οδήγησης


## <a name="greece-national-id-card"></a>그리스 신분증 ID 카드

### <a name="format"></a>형식

7-8개 문자와 숫자의 조합에 대시 추가

### <a name="pattern"></a>패턴

7개 문자와 숫자(이전 형식):
- 1개 문자(그리스어 알파벳 임의의 문자)
- 대시
- 6자리 숫자

8개 문자와 숫자(새 형식):
- 2개 문자, 그리스 및 라틴 알파벳(ABEZHIKMNOPTYX) 모두의 대문자 포함
- 대시
- 6자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_greece_id_card"></a>Keyword_greece_id_card

- 그리스어 ID
- 그리스어 국가 ID
- 그리스어 개인 ID 카드
- 그리스어 경비 ID
- ID 카드
- 2018년 3월
- ταυτότητα
- ταυτότητας


## <a name="greece-passport-number"></a>그리스 여권 번호

### <a name="format"></a>서식

2개 문자 뒤에 7자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

2개 문자 뒤에 7자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- 여권 #
- 여권 #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keywords_greece_eu_passport_number"></a>Keywords_greece_eu_passport_number

- αριθμός διαβατηρίου
- αριθμούς διαβατηρίου
- αριθμός διαβατηριο


## <a name="greece-social-security-number-amka"></a>그리스 사회 보장 번호(AMKA)
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

11자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

- 생년월일의 6자리 숫자(YYMMDD)
- 4자리 숫자
- 1자리 확인 번호

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_greece_eu_ssn_or_equivalent"></a>Keywords_greece_eu_ssn_or_equivalent

- Ssn
- Ssn #
- 사회 보장 아니요
- socialsecurityno #
- 사회 보장 번호
- amka
- a.m.k.a.
- Αριθμού Μητρώου Κοινωνικής Ασφάλισης


## <a name="greece-tax-identification-number"></a>그리스 납세자 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

9자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

9자리 숫자

### <a name="checksum"></a>체크섬

해당 없음

### <a name="keywords"></a>키워드

#### <a name="keywords_greece_eu_tax_file_number"></a>Keywords_greece_eu_tax_file_number

- Afm #
- Afm
- a이(|a) αριθμός
- aμμ
- 세금 ID
- 세금 식별 번호
- 세금 ID 번호
- 세금 번호 #
- 세금 번호
- 세금 번호
- 세금 등록 번호
- 세금 레지스트리 아니요
- 세금 레지스트리 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- taxregistryno #
- tin id
- tin no
- 주석 #
- αριθμός φορολογικού μητρώου
- τον αριθμό φορολογικού μητρώου
- φορολογικού μητρώου νο


## <a name="hong-kong-identity-card-hkid-number"></a>홍콩 신분증(HKID) 번호

### <a name="format"></a>형식

8-9개 문자와 숫자의 조합 및 마지막 문자를 괄호로 묶기(선택 사항)

### <a name="pattern"></a>패턴

8-9개 문자의 조합:
- 1-2자(대/소문자 구분 안 함)
- 6자리 숫자
- 마지막 문자(임의의 숫자 또는 문자 A), 확인 숫자이며 선택적으로 괄호로 묶여 있음

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_hong_kong_id_card"></a>Keyword_hong_kong_id_card

- hkid
- 홍콩 ID 카드
- HKIDC
- ID 카드
- ID 카드
- hk ID 카드
- 홍콩 ID
- 香港身份證
- 香港永久性居民身份證
- 身份證
- 身份証
- 身分證
- 身分証
- 香港身份証
- 香港身分證
- 香港身分証
- 香港身份證
- 香港居民身份證
- 香港居民身份証
- 香港居民身分證
- 香港居民身分証
- 香港永久性居民身份証
- 香港永久性居民身分證
- 香港永久性居民身分証
- 香港永久性居民身份證
- 香港非永久性居民身份證
- 香港非永久性居民身份証
- 香港非永久性居民身分證
- 香港非永久性居民身分証
- 香港特別行政區永久性居民身份證
- 香港特別行政區永久性居民身份証
- 香港特別行政區永久性居民身分證
- 香港特別行政區永久性居民身分証
- 香港特別行政區非永久性居民身份證
- 香港特別行政區非永久性居民身份証
- 香港特別行政區非永久性居民身分證
- 香港特別行政區非永久性居民身分証


## <a name="hungary-drivers-license-number"></a>운수 기사의 라이선스 번호

### <a name="format"></a>서식

2개 문자 뒤에 6자리 숫자

### <a name="pattern"></a>패턴

2개 문자 및 6자리 숫자:

- 2개 문자(대/소문자 구분 안 함)
- 6자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- 드라이버
- driverlicense
- driverlicense
- driverlicence
- driverlicences
- driver lic
- 드라이버 lics
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'lic
- driver'lics
- 운전면허증
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- driver' lic
- 드라이버의 lics
- 운전면허증
- 운전 기사 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'slic
- driver'slics
- 드라이버의license
- 드라이버의 slicenses
- 드라이버의licence
- 드라이버의 slicences
- 드라이버의 lic
- 드라이버의 lic
- 운전면허증
- 운전 기사의 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- Dl #
- Dl #
- driverlic #
- 드라이버 #
- driverlicense #
- driverlicense #
- driverlicence #
- driverlicences #
- driver lic #
- 드라이버 lics #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- driverslicense #
- driverslicenses #
- driverslicence #
- driverslicences #
- drivers lic #
- drivers lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'lic #
- driver'lics #
- 운전면허증 #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver' lic #
- 드라이버의 lics #
- 운전면허증 #
- 운전 기사 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lic #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전 운전 라이선스 
- 운전 라이선스
- dlno #
- driv lic
- driv licen
- driv 라이선스
- driv 라이선스
- driv 라이선스
- driv 라이선스
- 드라이버 사용 허가
- 드라이버 라이선스
- 드라이버의 사용 허가
- driving lic
- 운전 허가
- 운전 라이선스
- 운전 운전 라이선스
- 운전 라이선스
- 운전 허가
- dl 아니요
- dlno
- dl number


#### <a name="keywords_hungary_eu_drivers_license_number"></a>Keywords_hungary_eu_driver's_license_number

- vezetoi engedely
- vezetéi engedély
- vezetéi engedélyek


## <a name="hungary-personal-identification-number"></a>개인 식별 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>형식

11자리 숫자

### <a name="pattern"></a>패턴

11자리 숫자:

- 성별에 해당하는 1자리 숫자(남성은 1, 여성은 2). 1900년 이전에 출생한 시민 또는 이중 국적을 가진 시민의 경우 다른 번호도 가능합니다.
- 생년월일에 해당하는 6자리 숫자(YYMMDD)
- 일련 번호에 해당하는 3자리 숫자
- 1자리 확인 번호

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_hungary_eu_national_id_card"></a>Keywords_hungary_eu_national_id_card

- ID 번호
- ID 번호
- sz ig
- Sz. Ig.
- sz.ig.
- személyazonosótó igazolvlvy
- személyi igazolvlvlvy


## <a name="hungary-passport-number"></a>헝가리어 Passport 번호

### <a name="format"></a>서식

2개 문자 뒤에 6~7자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

2개 문자 뒤에 6~7자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- 여권 #
- 여권 #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keywords_hungary_eu_passport_number"></a>Keywords_hungary_eu_passport_number

- útlevél szúma
- Útlevelek szúma
- útlevél szúm

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="hungary-social-security-number-taj"></a>TAJ(사회 보장 번호)

### <a name="format"></a>서식

9자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

9자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_hungary_eu_ssn_or_equivalent"></a>Keywords_hungary_eu_ssn_or_equivalent

- 헝가리어 사회 보장 번호
- 사회 보장 번호
- socialsecuritynumber #
- hssn #
- socialsecuritynno
- hssn
- 타지
- 타지 #
- Ssn
- Ssn #
- 사회 보장 아니요
- 을(를)
- közösségi adószóm
- ltalicoos forgalmi adó szóm
- hozzóadottérték adó
- 을(를)
- magyar 을(를)


## <a name="hungary-tax-identification-number"></a>세금 ID 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

10자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

10자리 숫자:

- 1자리 숫자("8"이어야 함)
- 8자리 숫자
- 1자리 확인 번호

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_hungary_eu_tax_file_number"></a>Keywords_hungary_eu_tax_file_number

- adóazonosító szám
- adóhatóság szám
- adószám
- 헝가리어 tin
- hungatiantin #
- 세금 기관 아니요
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #
- vat 번호


## <a name="hungary-value-added-tax-number"></a>헝가리 값이 추가 된 세금 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

10자 영숫자 패턴

### <a name="pattern"></a>패턴

10자 영숫자 패턴:

- 2개 문자 - HU 또는 hu
- 선택적 공백
- 8자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_hungary_value_added_tax_number"></a>Keyword_Hungary_value_added_tax_number

- vat
- 값이 추가 된 세금 번호
- vat #
- vatno #
- hungarianvatno #
- 세금 번호.
- 부가 세율 áfa
- közösségi adószám
- általános forgalmi adbaszám
- hozzáadottérték adó
- áfa szám

## <a name="india-permanent-account-number-pan"></a>인도 PAN(영구 계좌 번호)

### <a name="format"></a>형식

10자리 문자 또는 숫자

### <a name="pattern"></a>패턴

10개 문자 또는 숫자:
- 세 문자 (대/소문자 구분 안 함)
- C, P, H, F, A, T, B, L, J, G의 문자 (대/소문자 구분 안 함)
- 문자
- 4자리 숫자
- 영문자를 확인 하는 문자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_india_permanent_account_number"></a>Keyword_india_permanent_account_number

- 영구 계정 번호
- PAN

## <a name="india-unique-identification-aadhaar-number"></a>인도 신분증(Aadhaar) 번호

### <a name="format"></a>형식

12자리 숫자, 선택적 공백 또는 대시 포함

### <a name="pattern"></a>패턴

12자리 숫자:
- 0 또는 1이 아닌 숫자입니다.
- 3자리 숫자
- 선택적 공백 또는 대시
- 4자리 숫자
- 선택적 공백 또는 대시
- 마지막 숫자, 확인 숫자임

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_india_aadhar"></a>Keyword_india_aadhar
- aadhaar
- aadhar
- aadhar #
- uid
- आधार
- uidai

## <a name="indonesia-identity-card-ktp-number"></a>인도네시아 신분증(KTP) 번호

### <a name="format"></a>형식

16자리 숫자, 선택적 마침표 포함

### <a name="pattern"></a>패턴

16자리 숫자:
- 2자리 지방 코드
- 마침표(선택 사항)
- 2자리 숫자 리젠시 또는 도시 코드
- 2 자리 숫자 하위 구역 코드
- 마침표(선택 사항)
- 6자리 숫자(DDMMYY 형식의 생년월일)
- 마침표(선택 사항)
- 4자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_indonesia_id_card"></a>Keyword_indonesia_id_card

- KTP
- Kartu Tanda Penduduk
- Nomor Induk Kependudukan

## <a name="international-banking-account-number-iban"></a>IBAN(국제 은행 계좌 번호)

### <a name="format"></a>형식

국가 코드 (두 문자) + 검사 숫자 (두 자리)와 bban 숫자 (최대 30 자)

### <a name="pattern"></a>패턴

패턴은 다음을 모두 포함해야 합니다.

- 2개 문자 국가 번호
- 2자리 확인 숫자(다음에 선택적인 공백)
- 4개 문자 또는 숫자로 구성된 1-7 그룹(공백으로 구분 가능)
- 1-3개 문자 또는 숫자

국가별 형식은 약간 다름 IBAN 중요 정보 유형에 다음 60개 국가 포함

- ad
- ae
- al
- at
- az
- ba
- 있을
- bg
- bh
- ch
- cr
- cy
- cz
- de
- 진한
- do
- ee
- es
- fi
- fo
- fr
- gb
- ge
- gi
- gl
- gr
- 시간
- hu
- ie
- il
- is
- it
- kw
- kz
- lb
- li
- lt
- l
- lv
- mc
- md
- 나
- 프로토콜로
- mr
- mt
- mu
- nl
- no
- pl
- pt
- ro
- rs
- sa
- se
- si
- sk
- sm
- tn
- tr
- vg

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

없음

## <a name="ip-address"></a>IP 주소

### <a name="format"></a>형식

#### <a name="ipv4"></a>IPv4:
형식이 지정 된 (기간) 및 형식이 지정 되지 않은 (기간 없음) 버전의 IPv4 주소에 대해 계정을 지정 하는 복합 패턴

#### <a name="ipv6"></a>IPv6:
복잡한 패턴, 형식화된 IPv6 번호(콜론 포함)를 나타냄

### <a name="pattern"></a>패턴

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_ipaddress"></a>Keyword_ipaddress

- IP(이 키워드는 대/소문자 구분)
- ip 주소
- ip 주소
- 인터넷 프로토콜
- IP כתובת ה


## <a name="ip-address-v4"></a>IP 주소 v4

### <a name="format"></a>서식

형식이 지정된(마침표) 및 형식이 지정되지 않은(마침표 없음) 버전의 IPv4 주소를 고려하는 복잡한 패턴

### <a name="pattern"></a>무늬


### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_ipaddress"></a>Keyword_ipaddress

- IP(대/소문자 구분)
- ip 주소
- ip 주소
- 인터넷 프로토콜
- IP כתובת ה


## <a name="ip-address-v6"></a>IP 주소 v6

### <a name="format"></a>서식

복잡한 패턴, 형식화된 IPv6 번호(콜론 포함)를 나타냄

### <a name="pattern"></a>패턴


### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_ipaddress"></a>Keyword_ipaddress

- IP(대/소문자 구분)
- ip 주소
- ip 주소
- 인터넷 프로토콜
- IP כתובת ה


## <a name="ireland-drivers-license-number"></a>아일랜드 운전 면허 번호

### <a name="format"></a>서식

6자리 숫자 뒤 4개 문자

### <a name="pattern"></a>패턴

6자리 숫자 및 4개 문자:

- 6자리 숫자
- 4개 문자(대/소문자 구분 안 함)

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- 드라이버
- driverlicense
- driverlicense
- driverlicence
- driverlicences
- driver lic
- 드라이버 lics
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'lic
- driver'lics
- 운전면허증
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- driver' lic
- 드라이버의 lics
- 운전면허증
- 운전 기사 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'slic
- driver'slics
- 드라이버의license
- 드라이버의 slicenses
- 드라이버의licence
- 드라이버의 slicences
- 드라이버의 lic
- 드라이버의 lic
- 운전면허증
- 운전 기사의 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- Dl #
- Dl #
- driverlic #
- 드라이버 #
- driverlicense #
- driverlicense #
- driverlicence #
- driverlicences #
- driver lic #
- 드라이버 lics #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- driverslicense #
- driverslicenses #
- driverslicence #
- driverslicences #
- drivers lic #
- drivers lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'lic #
- driver'lics #
- 운전면허증 #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver' lic #
- 드라이버의 lics #
- 운전면허증 #
- 운전 기사 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lic #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전 운전 라이선스 
- 운전 라이선스
- dlno #
- driv lic
- driv licen
- driv 라이선스
- driv 라이선스
- driv 라이선스
- driv 라이선스
- 드라이버 사용 허가
- 드라이버 라이선스
- 드라이버의 사용 허가
- driving lic
- 운전 허가
- 운전 라이선스
- 운전 운전 라이선스
- 운전 라이선스
- 운전 허가
- dl 아니요
- dlno
- dl number


#### <a name="keywords_ireland_eu_drivers_license_number"></a>Keywords_ireland_eu_driver's_license_number

- ceadúnas tiom도a
- ceadúnais tiom도a

## <a name="ireland-passport-number"></a>아일랜드 여권 번호

### <a name="format"></a>서식

2개 문자 또는 숫자 뒤에 7자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

2개 문자 또는 숫자 뒤에 7자리 숫자:

- 2자리 숫자 또는 문자(대/소문자 구분 안 함)
- 7자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- 여권 #
- 여권 #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keywords_ireland_eu_passport_number"></a>Keywords_ireland_eu_passport_number

- 많은 패스포트
- uimhreacha pasanna
- uimhir pas
- uimhir phas
- uimhreacha pas
- uimhir c임rta
- uimhir chhirrta

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="ireland-personal-public-service-pps-number"></a>아일랜드 개별 공공 서비스(PPS) 번호

### <a name="format"></a>형식

이전 형식 (12 2012 월 31 일까 지):
- 7자리 숫자 뒤 1-2개 문자

새 형식 (1 월 2013 일 이후):
- 7자리 숫자 뒤 2개 문자

### <a name="pattern"></a>패턴

이전 형식 (12 2012 월 31 일까 지):
- 7자리 숫자
- 1 ~ 2 개 문자 (대/소문자 구분 안 함)

새 형식 (1 월 2013 일 이후):
- 7자리 숫자
- 영문자 확인 문자인 문자 (대/소문자 구분 안 함)
- A-I 또는 "W" 범위의 선택적 문자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_ireland_eu_national_id_card"></a>Keywords_ireland_eu_national_id_card

- 클라이언트 id 서비스
- 식별 번호
- 개인 id 번호
- 개인 공용 서비스 번호
- 개인 서비스 번호
- phearsanta seirbhíse poiblí
- pps 아니요
- pps 번호
- pps num
- pps 서비스 번호
- ppsn
- ppsno #
- ppsno
- psp
- 공용 서비스 번호
- publicserviceno #
- publicserviceno
- 수익 및 사회 보험 번호
- rsi 아니요
- rsi 번호
- rsin
- seirbhís aitheantais 클라이언트
- uimh
- uimhir aitheantais chánach
- uimhir aitheantais phearsanta
- uimhir phearsanta seirbhíse poiblí
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #


## <a name="israel-bank-account-number"></a>이스라엘 은행 계좌 번호

### <a name="format"></a>형식

13자리

### <a name="pattern"></a>패턴

형식 있음:
- 2자리 숫자
- 대시
- 3자리 숫자
- 대시
- 8자리 숫자

서식 없음:
- 연속된 숫자 13개

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_israel_bank_account_number"></a>Keyword_israel_bank_account_number

- 은행 계좌 번호
- 은행 계좌
- 계정 번호
- מספר חשבון בנק

## <a name="israel-national-identification-number"></a>이스라엘 국가 식별 번호

### <a name="format"></a>형식

9자리 숫자

### <a name="pattern"></a>패턴

연속된 숫자 9개

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_israel_national_id"></a>Keyword_Israel_National_ID

-   מספר זהות
-   מספר זיה וי
-   מספר זיהוי ישר אלי      
-   זהותישר אלית
-   هو ية اسرائيل ية عدد
-   هوية إسرائ يلية
-   رقم الهوية
-   عدد هوية فريدة من نوعها
-   idnumber #
-   id 번호
-   id 아니요        
-   identitynumber #
-   id 번호
-   israeliidentitynumber       
-   개인 id
-   고유 id  


## <a name="italy-drivers-license-number"></a>이탈리아 운전 면허 번호

이 형식 엔터티는 EU 드라이버의 라이선스 번호 중요 정보 형식에 포함 되어 있습니다. 또한 독립 실행형 중요 한 정보 유형 엔터티로 사용할 수 있습니다.

### <a name="format"></a>형식

10개 문자와 숫자의 조합

### <a name="pattern"></a>패턴

10개 문자와 숫자의 조합:
- 1개 문자(대/소문자 구분 안 함)
- 문자 "A" 또는 "V" (대/소문자 구분 안 함)
- 7자리 숫자
- 1개 문자(대/소문자 구분 안 함)

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- 드라이버 라이선스
- driverslicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- drivers 라이선스
- drivers 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 운전면허 #
- 운전 기사 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lic #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전 운전 라이선스 
- 운전 라이선스
- dlno #
- driv lic
- driv licen
- driv 라이선스
- driv 라이선스
- driv 라이선스
- driv 라이선스
- 드라이버 사용 허가
- 드라이버 라이선스
- 드라이버의 사용 허가
- driving lic
- 운전 허가
- 운전 라이선스
- 운전 운전 라이선스
- 운전 라이선스
- 운전 허가
- dl 아니요
- dlno
- dl number

#### <a name="keyword_italy_drivers_license_number"></a>Keyword_italy_drivers_license_number

- numero di patente
- patente di guida
- patente guida
- patenti di guida
- patenti guida


## <a name="italy-fiscal-code"></a>이탈리아 회계 코드
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

지정된 패턴의 문자 및 숫자의 16자 조합

### <a name="pattern"></a>패턴

문자와 숫자의 16자 조합:
- 성의 처음 세 자음에 해당하는 3개 문자
- 이름의 첫 번째, 세 번째 및 네 번째 자음에 해당하는 3개 문자
- 출생 연도의 마지막 숫자에 해당하는 2자리 숫자
- 출생 월의 문자에 해당하는 1개 문자 - 문자는 사전순으로 사용되지만 A~E, H, L, M, P, R~T 문자만 사용됨(따라서 1월은 A, 10월은 R)
- 성별을 구분하기 위해 출생 월의 일에 해당하는 2자리 숫자(여성의 경우 40을 출생 일에 추가)
- 사람이 출생한 지방자치단체의 특정 지역 코드에 해당하는 4자리 숫자(외국의 경우 국가 차원 코드 사용)
- 1자리 패리티 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_italy_eu_national_id_card"></a>Keywords_italy_eu_national_id_card

- codice fiscal
- codice fiscale
- codice id personale
- codice personale
- 회계 코드
- 수많은 certificato personale
- numero di identificazione fiscale
- numero id personale
- 수많은 개인
- 개인 인증서 번호
- 개인 코드
- 개인 ID 코드
- 개인 ID 번호
- personalcodeno #
- 세금 코드
- 세금 ID
- 세금 식별 번호
- 세금 ID 번호
- 세금 ID 번호
- 세금 번호 #
- 세금 번호
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin no
- 주석 #


## <a name="italy-passport-number"></a>이탈리아 Passport 번호

### <a name="format"></a>서식

2개 문자 또는 숫자 뒤에 7자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

2개 문자 또는 숫자 뒤에 7자리 숫자:

- 2자리 숫자 또는 문자(대/소문자 구분 안 함)
- 7자리 숫자

### <a name="checksum"></a>체크섬

적용할 수 없음

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- 여권 #
- 여권 #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keywords_italy_eu_passport_number"></a>Keywords_italy_eu_passport_number

- 이탈리아어 passaporto
- passaporto 이탈리아어
- passaporto numero
- numéro passeport
- numero di passaporto
- numeri del passaporto
- passeport italien

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="italy-value-added-tax-number"></a>이탈리아 부가가치세 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

13자 영숫자 패턴(선택적 구분 기호 포함)

### <a name="pattern"></a>패턴

13자 영숫자 패턴(선택적 구분 기호 포함):

- I 또는 i
- T 또는 t
- 선택적 공백, 점, 하이픈 또는 쉼표
- 11자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_italy_value_added_tax_number"></a>Keyword_italy_value_added_tax_number

- vat 번호
- vat 아니요
- 부가 가치세 #
- Iva
- Iva #


## <a name="japan-bank-account-number"></a>일본 은행 계좌 번호

### <a name="format"></a>형식

7자리 또는 8자리 숫자

### <a name="pattern"></a>패턴

은행 계좌 번호:
- 7자리 또는 8자리 숫자
- 은행 계좌 지점 코드:
- 4자리 숫자
- 공백 또는 대시(선택 사항)
- 3자리 숫자

체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_bank_account"></a>Keyword_jp_bank_account

- 계정 번호 확인
- 계정 확인 중
- 계정 확인 #
- Acct 번호 확인
- Acct 확인 #
- Acct 아니요를 선택합니다.
- 계정 번호 확인
- 은행 계좌 번호
- 은행 계좌
- 은행 계좌 #
- 은행 누적 번호
- Bank Acct #
- Bank Acct 아니요.
- 은행 계좌 번호
- 절감액 계좌 번호
- 보통 예금
- 절감액 계정 #
- 절감액 수
- 절감액 #
- Savings Acct No.
- Savings Account 아니요.
- 직불 계정 번호
- 직불 계정
- 직불 계정 #
- 직불 Acct 번호
- 직불 Acct #
- Debit Acct 아니요.
- 직불 계정 번호
- 口座番号
- 銀行口座
- 銀行口座番号
- 総合口座
- 普通預金口座
- 普通口座
- 当座預金口座
- 当座口座
- 預金口座
- 振替口座
- 銀行
- バンク

#### <a name="keyword_jp_bank_branch_code"></a>Keyword_jp_bank_branch_code

- 支店番号
- 支店コード
- 店番号

## <a name="japan-drivers-license-number"></a>일본 운전 면허 번호

### <a name="format"></a>형식

12자리

### <a name="pattern"></a>패턴

연속된 숫자 12개

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_drivers_license_number"></a>Keyword_jp_drivers_license_number

- driverlicense
- driverslicense
- 드라이버의license
- driverslicenses
- 드라이버의 slicenses
- driverlicense
- Dl #
- Dl #
- Lic #
- lics #
- 運転免許証
- 運転免許
- 免許証
- 免許
- 運転免許証番号
- 運転免許番号
- 免許証番号
- 免許番号
- 運転免許証ナンバー
- 運転免許ナンバー
- 免許証ナンバー
- 運転免許証no
- 運転免許no
- 免許証no
- 免許no
- 運転経歴証明書番号
- 運転経歴証明書
- 運転免許証No.
- 運転免許No.
- 免許証No.
- 免許No.
- 運転免許証 #
- 運転免許 #
- 免許証 #
- 免許 #


## <a name="japan-my-number---corporate"></a>Japan My Number - Corporate
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

13자리 숫자

### <a name="pattern"></a>패턴

13자리 숫자:

- 1~9의 1자리 숫자
- 12자리

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_japan_my_number_corporate"></a>Keyword_japan_my_number_corporate

- 회사 번호
- マイナンバー
- 共通番号
- マイナンバーカード
- マイナンバーカード番号
- 個人番号カード
- 個人識別番号
- 個人識別ナンバー
- 法人番号
- 指定通知書


## <a name="japan-my-number---personal"></a>일본 내 번호 - 개인
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

12자리 숫자

### <a name="pattern"></a>패턴

12자리 숫자:

- 4자리 숫자
- 선택적 공백, 점 또는 하이픈
- 4자리 숫자
- 선택적 공백, 점 또는 하이픈
- 4자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_japan_my_number_personal"></a>Keyword_japan_my_number_personal

- 내 번호
- マイナンバー
- 個人番号
- 共通番号
- マイナンバーカード
- マイナンバーカード番号
- 個人番号カード
- 個人識別番号
- 個人識別ナンバー
- 通知カード


## <a name="japan-passport-number"></a>일본 여권 번호

### <a name="format"></a>형식

2개 문자 뒤에 7자리 숫자

### <a name="pattern"></a>패턴

두 문자 (대/소문자 구분 안 함)와 일곱 자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_passport"></a>Keyword_jp_passport

- Passport
- 여권 번호
- Passport 아니요.
- Passport #
- パスポート
- パスポート番号
- パスポートナンバー
- パスポート＃
- パスポート #
- パスポートNo.
- 旅券番号
- 旅券番号＃
- 旅券番号 ♯
- 旅券ナンバー


## <a name="japan-residence-card-number"></a>일본 거주증 번호

### <a name="format"></a>형식

12개의 문자와 숫자

### <a name="pattern"></a>패턴

12개의 문자와 숫자:
- 두 문자 (대/소문자 구분 안 함)
- 8자리 숫자
- 두 문자 (대/소문자 구분 안 함)

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_residence_card_number"></a>Keyword_jp_residence_card_number

- 거주지 카드 번호
- 거주지 카드 번호
- 거주지 카드 #
- 在留カード番号
- 在留カード
- 在留番号

## <a name="japan-resident-registration-number"></a>일본 상주 등록 번호

### <a name="format"></a>형식

11자리 숫자

### <a name="pattern"></a>패턴

연속된 숫자 11개

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_resident_registration_number"></a>Keyword_jp_resident_registration_number

- 상주 등록 번호
- 지역 기본 레지스트리 번호
- 상주 등록 아니요.
- 상주 레지스터 아니요.
- 지역 기본 레지스트리 번호.
- 기본 상주 레지스터 아니요.
- 外国人登録証明書番号
- 証明書番号
- 登録番号
- 外国人登録証


## <a name="japan-social-insurance-number-sin"></a>일본 SIN(사회 보장 번호)

### <a name="format"></a>형식

7-12자리 숫자

### <a name="pattern"></a>패턴

7-12자리 숫자:
- 4자리 숫자
- 하이픈(선택 사항)
- 6자리 숫자 또는
- 연속된 숫자 7-12개

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_jp_sin"></a>Keyword_jp_sin

- 소셜 보험 아니요.
- 소셜 보험 번호
- 사회 보험 번호
- 健康保険被保険者番号
- 健保番号
- 基礎年金番号
- 雇用保険被保険者番号
- 雇用保険番号
- 保険証番号
- 社会保険番号
- 社会保険No.
- 社会保険
- 介護保険
- 介護保険被保険者番号
- 健康保険被保険者整理番号
- 雇用保険被保険者整理番号
- 厚生年金
- 厚生年金被保険者整理番号


## <a name="latvia-drivers-license-number"></a>라트비아 운전 면허 번호

### <a name="format"></a>서식

3개 문자 뒤에 6자리 숫자

### <a name="pattern"></a>패턴

3개 문자 및 6자리 숫자:

- 3개 문자(대/소문자 구분 안 함)
- 6자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- 드라이버 라이선스
- driverslicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- drivers 라이선스
- drivers 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lic #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전 운전 라이선스 
- 운전 라이선스
- dlno #
- driv lic
- driv licen
- driv 라이선스
- driv 라이선스
- driv 라이선스
- driv 라이선스
- 드라이버 사용 허가
- 드라이버 라이선스
- 드라이버의 사용 허가
- driving lic
- 운전 허가
- 운전 라이선스
- 운전 운전 라이선스
- 운전 라이선스
- 운전 허가
- dl 아니요
- dlno
- dl number


#### <a name="keywords_latvia_eu_drivers_license_number"></a>Keywords_latvia_eu_driver's_license_number

- autovad을tja apliec을ba
- autovad교tja apliec을bas
- vad부tja apliec을ba

## <a name="latvia-personal-code"></a>2018년 3월 2

### <a name="format"></a>서식

11자리 숫자 및 선택적 하이픈

### <a name="pattern"></a>패턴

이전 형식

11자리 숫자 및 1개 하이픈:

- 생년월일에 해당하는 6자리 숫자(DDMMYY)
- 하이픈
- 출생 세기에 해당하는 1자리 숫자(19세기는 "0", 20세기는 "1", 21세기는 "2")
- 임의로 생성된 4자리 숫자

새 형식

11자리 숫자

- 2자리 숫자("32")
- 9자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_latvia_eu_national_id_card"></a>Keywords_latvia_eu_national_id_card

- 관리 번호
- alvas n을
- 생년월일 번호
- 시민 번호
- 2018년 3
- 전자 인구 조사 번호
- 전자 번호
- 회계 코드
- 의료 사용자 번호
- 자료 #
- id-code
- ID 번호
- identifikscijas nu
- id-number
- 개별 번호
- latvija alva
- nacion은 id입니다.
- national id
- national identifying number
- 국가 ID 번호
- 국가 보험 번호
- 국가 등록 번호
- nu nodokļa
- nodokļu ID
- nodokļu 식별자
- 개인 인증서 번호
- 개인 코드
- 개인 ID 코드
- 개인 ID 번호
- 개인 식별 코드
- 개인 식별자
- 개인 ID 번호
- 개인 번호
- 개인 숫자 코드
- personalcodeno #
- personas kods
- 인구 식별 코드
- 공용 서비스 번호
- 등록 번호
- 수익 번호
- 사회 보험 번호
- 사회 보장 번호
- 주 세금 코드
- 세금 파일 번호
- 세금 ID
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #
- 동안 유권자 수

## <a name="latvia-passport-number"></a>라트비아 passport 번호

### <a name="format"></a>서식

2개 문자 또는 숫자 뒤에 7자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

2개 문자 또는 숫자 뒤에 7자리 숫자:

- 2자리 숫자 또는 문자(대/소문자 구분 안 함)
- 7자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number_common"></a>Keywords_eu_passport_number_common

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- 여권 번호

#### <a name="keywords_latvia_eu_passport_number"></a>Keywords_latvia_eu_passport_number

- pase numurs
- pase numur
- 고가, numuri
- nr
- 는 포트 번호
- n ° du 및 Seport

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="lithuania-drivers-license-number"></a>리투아니아 드라이버의 라이선스 번호

### <a name="format"></a>형식

공백 및 구분 기호가 없는 8자리 숫자

### <a name="pattern"></a>패턴

8자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- 드라이버 라이선스
- driverslicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- drivers 라이선스
- drivers 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- 드라이버 (o) #
- 드라이버 ' s 라이선스 ' #
- 드라이버 ' s 라이선스 ' #
- driver'slicence #
- driver'slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 추진 라이선스 
- 추진 라이선스
- dlno #
- 드라이브 lic
- 드라이브 licen
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이버 licen
- 드라이버 licen
- 드라이버의 licen
- lic 구동
- 주행
- 운전 라이선스
- 추진 라이선스
- 주행 라이선스
- 촉진 허용
- dl 아니요
- dlno
- dl 번호


#### <a name="keywords_lithuania_eu_drivers_license_number"></a>Keywords_lithuania_eu_driver ' s_license_number

- vairuotojo pažymėjimas
- vairuotojo pažymėjimo numeris
- vairuotojo pažymėjimo numeriai

## <a name="lithuania-personal-code"></a>리투아니아 개인 코드
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

11자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

11자리 숫자(공백 및 구분 기호 비포함):

- 사람의 성별 및 출생 세기에 해당하는 1자리 숫자(1~6)
- 생년월일에 해당하는 6자리 숫자(YYMMDD)
- 생년월일의 일련 번호에 해당하는 3자리 숫자
- 1자리 확인 번호

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_lithuania_eu_national_id_card"></a>Keywords_lithuania_eu_national_id_card

- asmeninis kodas aitubinis
- asmens kodas
- 시민 서비스 번호
- mokesčių id
- mokesčių identifikavimas numeris
- mokesčių identifikavimo numeris
- 숫자 mokesčių
- 국가별 ID 번호
- 개인 코드
- 개인 숫자 코드
- 2018년 10월 20일
- 세금 ID
- 세금 식별 번호
- 세금 ID 번호
- 세금 번호 #
- 세금 번호
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin no
- 주석 #
- unikalus identifikavimo kodas
- unikalus identifikavimo numeris
- 고유 ID 번호
- 고유 ID 번호
- uniqueidentityno #

## <a name="lithuania-passport-number"></a>2016년 10월

### <a name="format"></a>서식

8개 숫자 또는 문자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

8개 숫자 또는 문자(대/소문자 구분 안 함)

### <a name="checksum"></a>체크섬

적용할 수 없음

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- 여권 #
- 여권 #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keywords_lithuania_eu_passport_number"></a>Keywords_lithuania_eu_passport_number

- 파소 숫자
- paso numeriai
- paso nr

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="luxemburg-drivers-license-number"></a>사용권 번호

### <a name="format"></a>서식

6자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

6자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- 드라이버
- driverlicense
- driverlicense
- driverlicence
- driverlicences
- driver lic
- 드라이버 lics
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'lic
- driver'lics
- 드라이버 라이선스
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- driver' lic
- 드라이버의 lics
- 운전면허증
- 운전 기사 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'slic
- driver'slics
- 드라이버의license
- 드라이버의 slicenses
- 드라이버의licence
- 드라이버의 slicences
- 드라이버의 lic
- 드라이버의 lic
- 운전면허증
- 운전 기사의 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- Dl #
- Dl #
- driverlic #
- 드라이버 #
- driverlicense #
- driverlicense #
- driverlicence #
- driverlicences #
- driver lic #
- 드라이버 lics #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- driverslicense #
- driverslicenses #
- driverslicence #
- driverslicences #
- drivers lic #
- drivers lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'lic #
- driver'lics #
- 운전면허증 #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver' lic #
- 드라이버의 lics #
- 운전면허증 #
- 운전 기사 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lic #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전 운전 라이선스 
- 운전 라이선스
- dlno #
- driv lic
- driv licen
- driv 라이선스
- driv 라이선스
- driv 라이선스
- driv 라이선스
- 드라이버 사용 허가
- 드라이버 라이선스
- 드라이버의 사용 허가
- driving lic
- 운전 허가
- 운전 라이선스
- 운전 운전 라이선스
- 운전 라이선스
- 운전 허가
- dl 아니요
- dlno
- dl number


#### <a name="keywords_luxemburg_eu_drivers_license_number"></a>Keywords_luxemburg_eu_driver's_license_number

- fahrerlaubnis
- Führerschin

## <a name="luxemburg-national-identification-number-natural-persons"></a>가메메가 국가 식별 번호 자연인
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

13자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

13자리 숫자:

- 11자리 숫자
- 2자리 확인 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_luxemburg_eu_national_id_card"></a>Keywords_luxemburg_eu_national_id_card

- eindeutige id
- eindeutige id-nummer
- eindeutigeid #
- id personnelle
- idpersonnelle #
- idpersonnelle
- 개별 코드
- 개별 id
- 개별 id
- 개별 id
- numéro d'identification 담당자
- 개인 id
- 개인 식별
- 개인 id
- personalidno #
- personalidnumber #
- persönliche identifikationsnummer
- 고유 id
- 고유 id
- uniqueidkey #

## <a name="luxemburg-national-identification-number-non-natural-persons"></a>비 자연 스러운 국제 id 번호

### <a name="format"></a>형식

11자리 숫자

### <a name="pattern"></a>패턴

11자리 숫자

- 2자리 숫자
- 공백(선택 사항)
- 3자리 숫자
- 공백(선택 사항)
- 3자리 숫자
- 공백(선택 사항)
- 2자리 숫자
- 1자리 확인 번호

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_luxemburg_eu_tax_file_number"></a>Keywords_luxemburg_eu_tax_file_number

- 맞춤형 de sécurité 사회 ale
- étain non
- étain #
- identifiant d'impôt
- 룩셈부르크 세금 identifikatiounsnummer
- numéro d'étain
- numéro d'identification 회계 luxembourgeois
- numéro d'identification fiscale
- 소셜 보안
- sozialunterstützung
- sozialversécherung
- sozialversicherungsausweis
- steier id
- steier identifikatiounsnummer
- steier nummer
- steuer id
- steueridentifikationsnummer
- steuernummer
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #
- zinn #
- zinn
- zinnzahl

## <a name="luxemburg-passport-number"></a>룩셈부르크 여권 번호

### <a name="format"></a>서식

8개 숫자 또는 문자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

8개 숫자 또는 문자(대/소문자 구분 안 함)

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- 여권 번호

#### <a name="keywords_luxemburg_eu_passport_number"></a>Keywords_luxemburg_eu_passport_number
- ausweisnummer
- 룩셈부르크 pass
- 룩셈부르크 포트
- 룩셈부르크 passport
- de-de 포트 없음
- reisepass
- nr-reisepass
- numéro de 포트
- 네트워크 통과
- nr 전달
- passnummer
- nombre
- reisepässe
- reisepass-nr
- reisepassnummer

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="luxemburg-national-identification-number-non-natural-persons"></a>룩셈부르크 국가 식별 번호 (비 자연 담당자)

### <a name="format"></a>형식

11자리 숫자

### <a name="pattern"></a>패턴

11자리 숫자

- 2자리 숫자
- 공백(선택 사항)
- 3자리 숫자
- 공백(선택 사항)
- 3자리 숫자
- 공백(선택 사항)
- 2자리 숫자
- 1자리 확인 번호

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_luxemburg_eu_tax_file_number"></a>Keywords_luxemburg_eu_tax_file_number

- 맞춤형 de sécurité 사회 ale
- étain non
- étain #
- identifiant d'impôt
- 룩셈부르크 세금 identifikatiounsnummer
- numéro d'étain
- numéro d'identification 회계 luxembourgeois
- numéro d'identification fiscale
- 소셜 보안
- sozialunterstützung
- sozialversécherung
- sozialversicherungsausweis
- steier id
- steier identifikatiounsnummer
- steier nummer
- steuer id
- steueridentifikationsnummer
- steuernummer
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #
- zinn #
- zinn
- zinnzahl


## <a name="malaysia-identification-card-number"></a>말레이시아 신분증 번호

### <a name="format"></a>형식

12자리 숫자, 선택적 하이픈 포함

### <a name="pattern"></a>패턴

12자리 숫자:
- 6자리 숫자(YYMMDD 형식의 생년월일)
- 대시(선택 사항)
- 2개 문자 출생지 코드
- 대시(선택 사항)
- 임의의 3자리 숫자
- 1자리 숫자 성별 코드

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_malaysia_id_card_number"></a>Keyword_malaysia_id_card_number

- 디지털 응용 프로그램 카드
- i/c
- i/c 아니요
- 소
- ic 아니요
- id 카드
- 식별 카드
- id 카드
- k/p
- k/p 아니요
- kad akuan diri
- kad aplikasi digital
- kad pengenalan 말레이시아
- kp
- kp 아니요
- mykad
- mykas
- mykid
- mypr
- mytentera
- 말레이시아 id 카드
- 말레이시아 id 카드
- nric
- 개인 식별 카드

## <a name="malta-drivers-license-number"></a>몰타 드라이버의 라이선스 번호

### <a name="format"></a>서식

지정된 패턴의 2개 문자 및 6자리 숫자의 조합

### <a name="pattern"></a>패턴

2개 문자 및 6자리 숫자의 조합:

- 2개 문자(숫자 또는 문자, 대/소문자 구분 안 함)
- 공백(선택 사항)
- 3자리 숫자
- 공백(선택 사항)
- 3자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- 드라이버 라이선스
- driverslicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- drivers 라이선스
- drivers 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 운전면허증 #
- 운전 기사 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lic #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전 운전 라이선스 
- 운전 라이선스
- dlno #
- driv lic
- driv licen
- driv 라이선스
- driv 라이선스
- driv 라이선스
- driv 라이선스
- 드라이버 사용 허가
- 드라이버 라이선스
- 드라이버의 사용 허가
- driving lic
- 운전 허가
- 운전 라이선스
- 운전 운전 라이선스
- 운전 라이선스
- 운전 허가
- dl 아니요
- dlno
- dl number


#### <a name="keywords_malta_eu_drivers_license_number"></a>Keywords_malta_eu_driver's_license_number

- liċenzja tas-sewqan
- liċenzji tas-sew liċenzji


## <a name="malta-identity-card-number"></a>10진수 ID 카드 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

7자리 숫자 뒤에 1개 문자

### <a name="pattern"></a>패턴

7자리 숫자 뒤에 1개 문자:

- 7자리 숫자
- "M, G, A, P, L, H, B, Z"의 1개 문자(대/소문자 구분 안 함)

### <a name="checksum"></a>체크섬

해당 없음

### <a name="keywords"></a>키워드

#### <a name="keywords_malta_eu_national_id_card"></a>Keywords_malta_eu_national_id_card

- 시민 서비스 번호
- id도 tax-taxxa
- identifika numru이(가)
- numerali personali kodiċi
- numru ta 'identifikazzjoni personali
- numru ta 'identifikazzjoni에서 taxxa
- numru ta 'identifikazzjoni uniku
- numru ta' identit을 uniku
- numru tas-servizz taċ-ċittadin
- numru-taxxa
- 개인 숫자 코드
- 고유 ID 번호
- 고유 ID 번호
- uniqueidentityno #


## <a name="malta-passport-number"></a>2016년 10월

### <a name="format"></a>서식

7자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

7자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- 여권 #
- 여권 #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keywords_malta_eu_passport_number"></a>Keywords_malta_eu_passport_number

- numru# -passaport
- numri도구-passaport
- Nru# passaport

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="malta-tax-identification-number"></a>납세 ID 번호

### <a name="format"></a>서식

몰타 국민의 경우:
- 지정된 패턴의 7자리 숫자 및 1개 문자

몰타 국민 또는 몰타 자주 독립체가 아닌 경우:
- 9자리 숫자

### <a name="pattern"></a>패턴

몰타 국민의 경우: 7자리 숫자 및 1개 문자

- 7자리 숫자
- 1개 문자(대/소문자 구분 안 함)

몰타 국민 또는 몰타 자주 독립체가 아닌 경우: 9자리 숫자

- 9자리 숫자

### <a name="checksum"></a>체크섬

해당 없음

### <a name="keywords"></a>키워드

#### <a name="keywords_malta_eu_tax_file_number"></a>Keywords_malta_eu_tax_file_number

- 시민 서비스 번호
- id tat-taxxa
- identifika numru-biljett
- kodiċi numerali personali
- numru ta ' identifikazzjoni personali
- numru ta ' tat-taxxa
- numru ta ' identifikazzjoni uniku
- numru ta ' identità uniku
- numru taċ-ċittadin
- numru tat-
- 개인 숫자 코드
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #
- 고유 id 번호
- 고유 id 번호
- uniqueidentityno #

## <a name="netherlands-citizens-service-bsn-number"></a>네덜란드 시민 서비스(BSN) 번호

### <a name="format"></a>형식

선택적 공백을 포함 하는 8 자리 또는 9 자리 숫자

### <a name="pattern"></a>패턴

8-9자리 숫자:
- 3자리 숫자
- 공백(선택 사항)
- 3자리 숫자
- 공백(선택 사항)
- 2-3자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_netherlands_eu_national_id_card"></a>Keywords_netherlands_eu_national_id_card

- bsn #
- bsn
- burgerservicenummer
- 시민 서비스 번호
- 사용자 번호
- 개인 번호
- 개인 숫자 코드
- 사용자 관련 숫자
- persoonlijk nummer
- persoonlijke 코드
- persoonsgebonden
- persoonsnummer
- sociaal-fiscaal nummer
- 소셜-회계 번호
- sofi
- sofinummer
- identificatienummer ek
- identiteitsnummer ek
- 고유 id 번호
- 고유 id 번호
- uniqueidentityno #

## <a name="netherlands-drivers-license-number"></a>네덜란드 운전 면허 번호

### <a name="format"></a>형식

10자리 숫자, 공백 및 구분 기호 없음

### <a name="pattern"></a>패턴

10개의 자릿수

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'lic
- driver'lics
- 운전면허증
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- driver' lic
- 드라이버의 lics
- 운전면허증
- 운전 기사 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'slic
- driver'slics
- 드라이버의license
- 드라이버의 slicenses
- 드라이버의licence
- 드라이버의 slicences
- 드라이버의 lic
- 드라이버의 lic
- 운전면허증
- 운전 기사의 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- Dl #
- Dl #
- driverlic #
- 드라이버 #
- driverlicense #
- driverlicense #
- driverlicence #
- driverlicences #
- driver lic #
- 드라이버 lics #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- driverslicense #
- driverslicenses #
- driverslicence #
- driverslicences #
- drivers lic #
- drivers lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'lic #
- driver'lics #
- 운전면허증 #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver' lic #
- 드라이버의 lics #
- 운전면허증 #
- 운전 기사 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lic #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전 운전 라이선스 
- 운전 라이선스
- dlno #
- driv lic
- driv licen
- driv 라이선스
- driv 라이선스
- driv 라이선스
- driv 라이선스
- 드라이버 사용 허가
- 드라이버 라이선스
- 드라이버의 사용 허가
- driving lic
- 운전 허가
- 운전 라이선스
- 운전 운전 라이선스
- 운전 라이선스
- 운전 허가
- dl 아니요
- dlno
- dl number


#### <a name="keywords_netherlands_eu_drivers_license_number"></a>Keywords_netherlands_eu_driver's_license_number

- permis de conduire
- rijbewijs
- rijbewijsnummer
- rijbewijzen
- rijbewijs nummer
- rijbewijsnummers


## <a name="netherlands-passport-number"></a>네덜란드의 Passport 번호

### <a name="format"></a>서식

9개 문자 또는 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

9개 문자 또는 숫자

### <a name="checksum"></a>체크섬

적용할 수 없음

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- 여권 #
- 여권 #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keywords_netherlands_eu_passport_number"></a>Keywords_netherlands_eu_passport_number

- paspoort nummer
- paspoortnummers
- paspoortnummer
- paspoort nr

## <a name="netherlands-tax-identification-number"></a>네덜란드 납세자 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

9자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

9자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_netherlands_eu_tax_file_number"></a>Keywords_netherlands_eu_tax_file_number

- btw nummer
- hollânske 세금 식별
- hulandes impuesto id number
- hulandes impuesto identification
- identificatienummer belasting
- identificatienummer van belasting
- impuesto ID 번호
- impuesto number
- nederlands belasting id nummer
- nederlands belasting identificatie
- nederlands belasting identificatienummer
- nederlands belastingnummer
- nederlandse belasting identificatie
- 네덜란드 세금 식별
- 네덜란드의 세금 식별
- 네덜란드 주석
- 네덜란드의 주석
- 세금 ID
- 세금 식별 번호
- 세금 ID 번호
- 세금 식별자
- 세금 번호 #
- 세금 번호
- 세금 번호
- 세금 등록 번호
- 세금 신고서
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin no
- 주석 #


## <a name="netherlands-value-added-tax-number"></a>네덜란드 부가가치세 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

14자 영숫자 패턴

### <a name="pattern"></a>패턴

14자 영숫자 패턴:

- N 또는 n
- L 또는 l
- 선택적 공백, 점 또는 하이픈
- 9자리 숫자
- 선택적 공백, 점 또는 하이픈
- B 또는 b
- 2자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_netherlands_value_added_tax_number"></a>Keyword_netherlands_value_added_tax_number

- vat 번호
- vat 아니요
- vat #
- wearde tafoege 세금 getal
- 만들었으므로 nûmer
- 만들었으므로-nummer


## <a name="new-zealand-bank-account-number"></a>뉴질랜드 은행 계좌 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

14자리~16자리 숫자 패턴(선택적 구분 기호 포함)

### <a name="pattern"></a>패턴

14자리~16자리 숫자 패턴(선택적 구분 기호 포함):

- 2자리 숫자
- 선택적 하이픈 또는 공백
- 3~4자리 숫자
- 선택적 하이픈 또는 공백
- 7자리 숫자
- 선택적 하이픈 또는 공백
- 2~3자리 숫자
- 선택적 하이픈 또는 공백

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_new_zealand_bank_account_number"></a>Keyword_new_zealand_bank_account_number

- 계정 번호
- 은행 계좌
- bank_acct_id
- bank_acct_branch
- bank_acct_nbr


## <a name="new-zealand-drivers-license-number"></a>뉴질랜드 드라이버의 라이선스 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

8자 영숫자 패턴

### <a name="pattern"></a>패턴

8자 영숫자 패턴

- 2개 문자
- 6자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_new_zealand_drivers_license_number"></a>Keyword_new_zealand_drivers_license_number

- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- 드라이버 라이선스
- driverslicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- driverlic #
- driverlics #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- 드라이버 (o) #
- driver'slicence #
- driver'slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 국제 주행 허용
- 국제 주행 허가
- nz 자동차 연결
- 뉴질랜드 자동차 연결


## <a name="new-zealand-inland-revenue-number"></a>뉴질랜드 내국세 수입 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

8~9자리 숫자(선택적 구분 기호 포함)

### <a name="pattern"></a>패턴

8~9자리 숫자(선택적 구분 기호 포함)

- 2~3자리 숫자
- 선택적 공백 또는 하이픈
- 3자리 숫자
- 선택적 공백 또는 하이픈
- 3자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_new_zealand_inland_revenue_number"></a>Keyword_new_zealand_inland_revenue_number

- ird 아니요.
- ird #
- nz ird
- 뉴질랜드 ird
- ird 번호
- inland 수익 번호


## <a name="new-zealand-ministry-of-health-number"></a>뉴질랜드 보건부 번호

### <a name="format"></a>형식

3개의 문자, 공백(선택 사항), 4자리 숫자

### <a name="pattern"></a>패턴

- ' I ' 및 ' O '를 제외한 세 문자 (대/소문자 구분 안 함)
- 공백(선택 사항)
- 4자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_nz_terms"></a>Keyword_nz_terms

- NHI
- 뉴질랜드
- 의료
- 처리
- 국가별 상태 인덱스 번호
- nhi
- nhi
- NHI #
- 국가별 상태 인덱스 번호입니다.
- 국가별 상태 인덱스 Id
- 국가별 상태 인덱스 #

## <a name="new-zealand-social-welfare-number"></a>뉴질랜드 사회 복지 번호

이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>형식

9자리 숫자

### <a name="pattern"></a>패턴

9자리 숫자

- 3자리 숫자
- 선택적 하이픈
- 3자리 숫자
- 선택적 하이픈
- 3자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_new_zealand_social_welfare_number"></a>Keyword_new_zealand_social_welfare_number

- 소셜 welfare #
- 소셜 welfare #
- 소셜 welfare 아니요.
- 소셜 welfare 번호
- swn #


## <a name="norway-identification-number"></a>노르웨이 식별 번호

### <a name="format"></a>형식

11자리 숫자

### <a name="pattern"></a>패턴

11자리 숫자:
- 6자리 숫자(DDMMYY 형식의 생년월일)
- 3자리 숫자 개별 번호
- 2자리 확인 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_norway_id_number"></a>Keyword_norway_id_number

- 개인 ID 번호
- 노르웨이어 ID 번호
- ID 번호
- Identification(식별)
- Personnummer
- F되드셀nummer


## <a name="philippines-unified-multi-purpose-identification-number"></a>필리핀 통합 다목적 식별 번호

### <a name="format"></a>형식

12자리 숫자, 하이픈으로 구분됨

### <a name="pattern"></a>패턴

12자리 숫자:
- 4자리 숫자
- 하이픈
- 7자리 숫자
- 하이픈
- 1자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_philippines_id"></a>Keyword_philippines_id

- 통합 다목적 ID
- UMID
- ID 카드
- Pinag-isang Multi-Layunin ID

## <a name="poland-drivers-license-number"></a>폴란드 운전 면허 번호

### <a name="format"></a>서식

14자리 숫자(2개 슬래시 포함)

### <a name="pattern"></a>패턴

14자리 숫자 및 2개 슬래시:

- 5자리 숫자
- 슬래시(/)
- 2자리 숫자
- 슬래시(/)
- 7자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- 드라이버
- driverlicense
- driverlicense
- driverlicence
- driverlicences
- driver lic
- 드라이버 lics
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'lic
- driver'lics
- 운전면허증
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- driver' lic
- 드라이버의 lics
- 운전면허증
- 운전 기사 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'slic
- driver'slics
- 드라이버의license
- 드라이버의 slicenses
- 드라이버의licence
- 드라이버의 slicences
- 드라이버의 lic
- 드라이버의 lic
- 운전면허증
- 운전 기사의 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- Dl #
- Dl #
- driverlic #
- 드라이버 #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- 드라이버 (o) #
- 드라이버 ' s 라이선스 ' #
- 드라이버 ' s 라이선스 ' #
- driver'slicence #
- driver'slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 추진 라이선스 
- 추진 라이선스
- dlno #
- 드라이브 lic
- 드라이브 licen
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이버 licen
- 드라이버 licen
- 드라이버의 licen
- lic 구동
- 주행
- 운전 라이선스
- 추진 라이선스
- 주행 라이선스
- 촉진 허용
- dl 아니요
- dlno
- dl 번호


#### <a name="keywords_poland_eu_drivers_license_number"></a>Keywords_poland_eu_driver ' s_license_number

- prawo jazdy
- prawa jazdy

## <a name="poland-identity-card"></a>폴란드 신분증 카드

### <a name="format"></a>형식

3개의 문자 및 6자리 숫자

### <a name="pattern"></a>패턴

세 문자 (대/소문자 구분 안 함)와 6 자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_poland_national_id_passport_number"></a>Keyword_poland_national_id_passport_number

- Dowód osobisty
- 숫자로 dowodu osobistego
- Nazwa i 숫자로 dowodu osobistego
- Nazwa i nr dowodu osobistego
- Nazwa i nr dowodu tożsamości
- Dowód Tożsamości
- dow. 부팅.


## <a name="poland-national-id-pesel"></a>폴란드 신분증(PESEL)

### <a name="format"></a>형식

11자리 숫자

### <a name="pattern"></a>패턴

- YYMMDD 형식의 생년월일을 나타내는 6 자리 숫자
- 4자리 숫자
- 1자리 확인 번호

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_pesel_identification_number"></a>Keyword_pesel_identification_number

- dowód osobisty
- dowódosobisty
- niepowtarzalny 숫자로
- niepowtarzalnynumer
- nr pesel
- nr-pesel
- 숫자로 identyfikacyjny
- pesel
- tożsamości narodowej


## <a name="poland-passport-number"></a>폴란드 여권 번호
이 중요 한 정보 형식 엔터티는 EU Passport 번호 중요 정보 형식에 포함 되어 있습니다. 독립 실행형 중요 한 정보 형식 엔터티로도 사용할 수 있습니다.

### <a name="format"></a>형식

2개의 문자 및 7자리 숫자

### <a name="pattern"></a>패턴

두 문자 (대/소문자 구분 안 함)와 일곱 자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- 여권 번호

#### <a name="keyword_polish_national_passport_number"></a>Keyword_polish_national_passport_number

- 숫자로
- numery paszportów
- numery
- nr
- nr. 고가 포트
- nr paszportów
- n °의 포트
- 포트 n °

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="poland-regon-number"></a>폴란드 REGON 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

9자리 또는 14자리 숫자

### <a name="pattern"></a>패턴

9자리 또는 14자리 숫자:

- 9자리 숫자 또는
- 9자리 숫자
- 하이픈
- 5자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_poland_regon_number"></a>Keywords_poland_regon_number

- regon id
- 통계 번호
- 통계 id
- 통계 번호
- regon 수
- regonid #
- regonno #
- 회사 id
- companyid #
- companyidno #
- 숫자로 statystyczny
- numeru regon
- numerstatystyczny #
- numeruregon #


## <a name="poland-tax-identification-number"></a>폴란드 납세자 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

11자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

11자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_poland_eu_tax_file_number"></a>Keywords_poland_eu_tax_file_number

- nip #
- nip
- 숫자로 identyfikacji podatkowej
- numeridentyfikacjipodatkowej #
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin no
- 주석 #
- vat id #
- vat id
- vat 아니요
- vat 번호
- vatid #
- vatid
- vatno #


## <a name="portugal-citizen-card-number"></a>포르투갈 시민증 번호

### <a name="format"></a>형식

8자리 숫자

### <a name="pattern"></a>패턴

8자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_portugal_citizen_card"></a>Keyword_portugal_citizen_card

- bilhete de identidade
- cartoo de cidad부
- 시민 카드
- 문서 번호
- documento de identificaço
- ID 번호
- 식별 번호
- ID 번호
- ID 카드 없음
- ID 카드 번호
- 국가 ID 카드
- Nic
- número bi de
- número de identificaço 시어머니
- número de identificaço fiscal
- número do documento
- 포르투갈 bi 번호


## <a name="portugal-drivers-license-number"></a>포르투갈 운전 면허 번호

### <a name="format"></a>형식

2가지 패턴 - 2개 문자 뒤에 특수 문자가 있는 5-8자리 숫자

### <a name="pattern"></a>패턴

패턴 1: 2개 문자 뒤에 특수 문자가 있는 5/6자:
- 2개 문자(대/소문자 구분 안 함)
- 하이픈
- 5자리 또는 6자리 숫자
- 공백
- 1자리 숫자

패턴 2: 1개 문자 뒤에 특수 문자가 있는 6/8자리 숫자:
- 1개 문자(대/소문자 구분 안 함)
- 하이픈
- 6자리 또는 8자리 숫자
- 공백
- 1자리 숫자


### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- 드라이버
- driverlicense
- driverlicense
- driverlicence
- driverlicences
- driver lic
- 드라이버 lics
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'lic
- driver'lics
- 드라이버 라이선스
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버의 lic
- 드라이버의 lics
- 운전면허
- 운전 기사 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'slic
- driver'slics
- 드라이버의license
- 드라이버의 slicenses
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- 드라이버 (o) #
- 드라이버 ' s 라이선스 ' #
- 드라이버 ' s 라이선스 ' #
- driver'slicence #
- driver'slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 추진 라이선스 
- 추진 라이선스
- dlno #
- 드라이브 lic
- 드라이브 licen
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이버 licen
- 드라이버 licen
- 드라이버의 licen
- lic 구동
- 주행
- 운전 라이선스
- 추진 라이선스
- 주행 라이선스
- 촉진 허용
- dl 아니요
- dlno
- dl 번호


#### <a name="keywords_portugal_eu_drivers_license_number"></a>Keywords_portugal_eu_driver ' s_license_number

- carteira de motorista
- carteira motorista
- carteira de habilitação
- carteira habilitação
- número de licença
- número licença
- permissão de condução
- permissão condução
- Licença condução 포르투갈
- carta de condução

## <a name="portugal-passport-number"></a>포르투갈 여권 번호

### <a name="format"></a>서식

1개 문자 뒤에 6자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

1개 문자 뒤에 6자리 숫자:

- 1개 문자(대/소문자 구분 안 함)
- 6자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- 여권 번호

#### <a name="keywords_portugal_eu_passport_number"></a>Keywords_portugal_eu_passport_number

- número do passaporte
- 포르투갈어 passport
- 포르투갈어의 포트
- 포르투갈어 passaporte
- passaporte n º
- 포트 n º
- números de passaporte
- 포르투갈어 여권
- número passaporte
- números passaporte

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="portugal-tax-identification-number"></a>포르투갈 납세자 번호

### <a name="format"></a>서식

9자리 숫자(선택적 공백 포함)

### <a name="pattern"></a>패턴

- 3자리 숫자
- 공백(선택 사항)
- 3자리 숫자
- 공백(선택 사항)
- 3자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_portugal_eu_tax_file_number"></a>Keywords_portugal_eu_tax_file_number

- cpf #
- cpf
- 않으려면 #
- 않으려면
- número de identificação fisca
- numero 회계
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #


## <a name="romania-drivers-license-number"></a>루마니아 운전 면허 번호

### <a name="format"></a>서식

1개 문자 뒤에 8자리 숫자

### <a name="pattern"></a>패턴

1개 문자 뒤에 8자리 숫자:
- 1개 문자(대/소문자 구분 안 함) 또는 숫자
- 8자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- 드라이버 라이선스
- driverslicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- drivers 라이선스
- drivers 라이선스
- driver'licences
- driver' lic
- 드라이버의 lics
- 운전면허증
- 운전 기사 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'slic
- driver'slics
- 드라이버의license
- 드라이버의 slicenses
- 드라이버의licence
- 드라이버의 slicences
- 드라이버의 lic
- 드라이버의 lic
- 운전면허증
- 운전 기사의 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- Dl #
- Dl #
- driverlic #
- 드라이버 #
- driverlicense #
- driverlicense #
- driverlicence #
- driverlicences #
- driver lic #
- 드라이버 lics #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- driverslicense #
- driverslicenses #
- driverslicence #
- driverslicences #
- drivers lic #
- drivers lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'lic #
- driver'lics #
- 드라이버 라이선스 #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver' lic #
- 드라이버의 lics #
- 운전면허증 #
- 운전 기사 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lic #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전 운전 라이선스 
- 운전 라이선스
- dlno #
- driv lic
- driv licen
- driv 라이선스
- driv 라이선스
- driv 라이선스
- driv 라이선스
- 드라이버 사용 허가
- 드라이버 라이선스
- 드라이버의 사용 허가
- driving lic
- 운전 허가
- 운전 라이선스
- 운전 운전 라이선스
- 운전 라이선스
- 운전 허가
- dl 아니요
- dlno
- dl number


#### <a name="keywords_romania_eu_drivers_license_number"></a>Keywords_romania_eu_driver's_license_number

- permis deducere
- permisului de conducere
- permisului conducere
- permisele de conducere
- permisele conducere
- permis conducere

## <a name="romania-personal-numeric-code-cnp"></a>루마니아 개인 숫자 코드(CNP)
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

13자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

- 1~9의 1자리 숫자
- 생년월일을 나타내는 6자리 숫자(YYMMDD)
- 2자리 숫자(01~52 또는 99 일 수 있음)
- 4자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_romania_eu_national_id_card"></a>Keywords_romania_eu_national_id_card

- cnp #
- cnp
- cod identificare 개인
- cod 숫자 개인
- cod unic identificare
- codnumericpersonal #
- codul 회계 nr.
- identificarea fiscală nr #
- id-ul taxei
- 보험 번호
- insurancenumber #
- 국가 id #
- 국가 id
- 국가 식별 번호
- număr identificare 개인
- număr identitate
- număr 개인 unic
- număridentitate #
- număridentitate
- numărpersonalunic #
- numărpersonalunic
- număru de identificare fiscală
- numărul de identificare fiscală
- 개인 숫자 코드
- 번호 #
- 고정
- 세금 파일 번호
- 세금 파일 번호
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #
- 고유 id 번호
- 고유 id 번호
- uniqueidentityno #
- uniqueidentityno

## <a name="romania-passport-number"></a>루마니아 여권 번호

### <a name="format"></a>서식

8~9자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

8~9자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- 여권 번호

#### <a name="keywords_romania_eu_passport_number"></a>Keywords_romania_eu_passport_number

- numărul pașaportului
- numarul pasaportului
- numerele pașaportului
- Pașaport nr

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="russia-passport-number-domestic"></a>러시아 여권 번호(국내)
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

10자리 숫자

### <a name="pattern"></a>패턴

10자리 숫자:

- 2자리 숫자
- 선택적 공백 또는 하이픈
- 2자리 숫자
- 공백(선택 사항)
- 6자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_russia_passport_number_domestic"></a>Keyword_russia_passport_number_domestic

- 여권 번호
- passport 아니요
- passport #
- passport id
- passportno #
- passportnumber #
- паспорт нет
- паспорт id
- pоссийской паспорт
- pусский номер паспорта
- паспорт #
- паспортid #
- номер паспорта
- номерпаспорта #


## <a name="russia-passport-number-international"></a>러시아 여권 번호(국제)
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

9자리 숫자

### <a name="pattern"></a>패턴

9자리 숫자:

- 2자리 숫자
- 선택적 공백 또는 하이픈
- 7자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_russia_passport_number_international"></a>Keywords_russia_passport_number_international

- 여권 번호
- passport 아니요
- passport #
- passport id
- passportno #
- passportnumber #
- паспорт нет
- паспорт id
- pоссийской паспорт
- pусский номер паспорта
- паспорт #
- паспортid #
- номер паспорта
- номерпаспорта #


## <a name="saudi-arabia-national-id"></a>사우디아라비아 신분증 ID

### <a name="format"></a>형식

10개의 자릿수

### <a name="pattern"></a>패턴

연속된 숫자 10개

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_saudi_arabia_national_id"></a>Keyword_saudi_arabia_national_id

- 식별 카드
- I 카드 번호
- ID 번호
- الوطنية الهوية بطاقة رقم


## <a name="singapore-national-registration-identity-card-nric-number"></a>싱가포르 공민 신분증(NRIC) 번호

### <a name="format"></a>형식

9개의 문자와 숫자

### <a name="pattern"></a>패턴

- 9개의 문자와 숫자:
- 문자 "F", "G", "S" 또는 "T" (대/소문자 구분 안 함)
- 7자리 숫자
- 1개 영문자 확인 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_singapore_nric"></a>Keyword_singapore_nric

- 국가별 등록 Id 카드
- Id 카드 번호
- NRIC
- IC
- 외부 Id
- FIN
- 身份证
- 身份證

## <a name="slovakia-drivers-license-number"></a>슬로바키아 운전 면허 번호

### <a name="format"></a>서식

1개 문자 뒤에 7자리 숫자

### <a name="pattern"></a>패턴

1개 문자 뒤에 7자리 숫자

- 1개 문자(대/소문자 구분 안 함) 또는 숫자
- 7자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicense
- driverlicence
- driverlicences
- driver lic
- 드라이버 lics
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'lic
- driver'lics
- 드라이버 라이선스
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버의 lic
- 드라이버의 lics
- 운전면허
- 운전 기사 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'slic
- driver'slics
- 드라이버의license
- 드라이버의 slicenses
- 드라이버의licence
- 드라이버의 slicences
- 드라이버의 lic
- 드라이버의 lic
- 운전면허증
- 운전 기사의 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- Dl #
- Dl #
- driverlic #
- 드라이버 #
- driverlicense #
- driverlicense #
- driverlicence #
- driverlicences #
- driver lic #
- 드라이버 lics #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- driverslicense #
- driverslicenses #
- driverslicence #
- driverslicences #
- drivers lic #
- drivers lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'lic #
- driver'lics #
- 드라이버 라이선스 #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버의 lic #
- 드라이버의 lics #
- 운전면허 #
- 운전 기사 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lic #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전 운전 라이선스 
- 운전 라이선스
- dlno #
- driv lic
- driv licen
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이버 licen
- 드라이버 licen
- 드라이버의 licen
- lic 구동
- 주행
- 운전 라이선스
- 추진 라이선스
- 주행 라이선스
- 촉진 허용
- dl 아니요
- dlno
- dl 번호


#### <a name="keywords_slovakia_eu_drivers_license_number"></a>Keywords_slovakia_eu_driver ' s_license_number

- vodičský preukaz
- vodičské preukazy
- vodičského preukazu
- vodičských preukazov

## <a name="slovakia-personal-number"></a>슬로바키아 개인 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

9~10자리 숫자(선택적 공백 포함)

### <a name="pattern"></a>패턴

- 생년월일을 나타내는 6자리 숫자
- 선택적 슬래시(/)
- 3자리 숫자
- 선택적 1자리 확인 번호

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_slovakia_eu_national_id_card"></a>Keywords_slovakia_eu_national_id_card

- azonosító szám
- 생년월일
- číslo národnej identifikačnej karty
- číslo občianského preukazu
- daňové číslo
- id 번호
- 식별 안 함
- 식별 번호
- identifikačná karta č
- identifikačné číslo
- id 카드 번호
- id 카드 번호
- národná identifikačná značka č
- 국가 번호
- nationalnumber #
- nemzeti személyazonosító igazolvány
- personalidnumber #
- rč
- rodne cislo
- rodné číslo
- 사회 보장 번호
- ssn #
- ssn
- személyi igazolvány szám
- személyi igazolvány száma
- személyigazolvány szám
- 세금 파일 번호
- 세금 파일 번호
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #

## <a name="slovakia-passport-number"></a>슬로바키아 여권 번호

### <a name="format"></a>서식

공백 또는 구분 기호 없이 한 자릿수 또는 문자 다음에 일곱 자리 숫자

### <a name="pattern"></a>무늬

한 자리 또는 문자 (대/소문자 구분 안 함) 다음에 7 자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keywords_slovakia_eu_passport_number"></a>Keywords_slovakia_eu_passport_number

- 을(를) 파스로
- 을(를) 파스포프
- pas 을(를)
- Passeport n°
- n° Passeport

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="slovenia-drivers-license-number"></a>슬로베니아 운전 면허 번호

### <a name="format"></a>서식

9자리 숫자(공백 및 구분 기호 비포함)

### <a name="pattern"></a>패턴

9자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver's_license_number

- driverlic
- 드라이버
- driverlicense
- driverlicense
- driverlicence
- driverlicences
- driver lic
- 드라이버 lics
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- driverslicence
- driverslicences
- driverslicense
- driverslicenses
- drivers lic
- drivers lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'lic
- driver'lics
- 운전면허증
- 운전면허증
- 드라이버 라이선스
- 드라이버 라이선스
- driver' lic
- 드라이버의 lics
- 운전면허증
- 운전 기사 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driver'slic
- driver'slics
- 드라이버의license
- 드라이버의 slicenses
- 드라이버의licence
- 드라이버의 slicences
- 드라이버의 lic
- 드라이버의 lic
- 운전면허증
- 운전 기사의 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- Dl #
- Dl #
- driverlic #
- 드라이버 #
- driverlicense #
- driverlicense #
- driverlicence #
- driverlicences #
- driver lic #
- 드라이버 lics #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- driverslicense #
- driverslicenses #
- driverslicence #
- driverslicences #
- drivers lic #
- drivers lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'lic #
- driver'lics #
- 운전면허증 #
- 운전면허증 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver' lic #
- 드라이버의 lics #
- 운전면허증 #
- 운전 기사 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driver'slic #
- driver'slics #
- 드라이버의license #
- 드라이버의 slicenses #
- 드라이버의licence #
- 드라이버의 slicences #
- 드라이버의 lic #
- 드라이버의 lic #
- 운전면허증 #
- 운전 기사의 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 운전 운전 라이선스 
- 운전 라이선스
- dlno #
- driv lic
- driv licen
- driv 라이선스
- driv 라이선스
- driv 라이선스
- driv 라이선스
- 드라이버 사용 허가
- 드라이버 라이선스
- 드라이버의 사용 허가
- driving lic
- 운전 허가
- 운전 라이선스
- 운전 운전 라이선스
- 운전 라이선스
- 운전 허가
- dl 아니요
- dlno
- dl number


#### <a name="keywords_slovenia_eu_drivers_license_number"></a>Keywords_slovenia_eu_driver's_license_number

- voznizko dovo
- voznizka 을(를) 사용 하 고 라이선스
- voznizkih dovo
- zteka voznizkega dovoja
- ztekih dovo

## <a name="slovenia-unique-master-citizen-number"></a>슬로베니아 고유 마스터 시민 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

13자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

지정된 패턴의 13자리 숫자:

- 생년월일에 해당하는 7자리 숫자(DDMMLLL)(여기서 "LLL"은 생년월일의 마지막 3자리 숫자에 해당)
- "50" 출생 지역에 해당하는 2자리 숫자
- 같은 날에 출생한 사람의 성별과 일련 번호의 조합에 해당하는 3자리 숫자. 남성은 000~499, 여성은 500~999입니다.
- 1자리 확인 번호

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_slovenia_eu_national_id_card"></a>Keywords_slovenia_eu_national_id_card

- edinstvena 을(를) 통해 glavnega dr을
- emoo
- enotna maticna 을(를) 통해 enotna maticna obcana
- ID 카드
- ID 번호
- identifikacijka
- ID 카드
- nacionalna ID
- nacionalni 포트니 목록
- national id
- osebna izkaznica
- osebni koda
- osebni ne
- osebni 을(를)
- 개인 코드
- 개인 번호
- 개인 숫자 코드
- 을(를)
- 고유 시민 번호
- 고유 ID 번호
- 고유 ID 번호
- 고유 마스터 시민 번호
- 고유한 등록 번호
- uniqueidentityno #
- uniqueidentityno #

## <a name="slovenia-passport-number"></a>슬로베니아 여권 번호

### <a name="format"></a>서식

2개 문자 뒤에 7자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

2개 문자 뒤에 7자리 숫자:

- "P" 문자
- 1개 대문자
- 7자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- 여권 번호

#### <a name="keywords_slovenia_eu_passport_number"></a>Keywords_slovenia_eu_passport_number

- številka potnega lista
- potek veljavnosti
- potni 목록 #
- 데이텀 rojstva
- potni 목록
- številke potnih listov

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="slovenia-tax-identification-number"></a>슬로베니아 납세자 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

8자리 숫자(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

- 1~9의 1자리 숫자
- 6자리 숫자
- 1자리 확인 번호

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_slovenia_eu_tax_file_number"></a>Keywords_slovenia_eu_tax_file_number

- davčna številka
- identifikacijska številka davka
- številka davčne datoteke
- 세금 파일 번호
- 세금 파일 번호
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #


## <a name="south-africa-identification-number"></a>남아프리카 공화국 식별 번호

### <a name="format"></a>형식

13자리 숫자, 공백 포함 가능

### <a name="pattern"></a>패턴

13자리 숫자:
- 6자리 숫자(YYMMDD 형식의 생년월일)
- 4자리 숫자
- 1자리 숫자 시민권 표시기
- 숫자 "8" 또는 "9"
- 1자리 숫자, 체크섬 숫자임

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_south_africa_identification_number"></a>Keyword_south_africa_identification_number

- Id 카드
- ID
- Identification(식별)

## <a name="south-korea-resident-registration-number"></a>대한민국 주민 등록 번호

### <a name="format"></a>형식

13자리 숫자, 하이픈 포함

### <a name="pattern"></a>패턴

13자리 숫자:
- 6자리 숫자(YYMMDD 형식의 생년월일)
- 하이픈
- 1자리 숫자, 세기와 성별에 따라 결정
- 4자리 숫자 출생 지역 코드
- 1자리 숫자, 앞의 숫자가 동일한 사람들을 구분하는 데 사용
- 1자리 확인 숫자.

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_south_korea_resident_number"></a>Keyword_south_korea_resident_number

- 국가 ID 카드
- 시민 등록 번호
- Jumin deungnok beonho
- RRN
- 주민등록번호

## <a name="spain-drivers-license-number"></a>스페인 운전 면허 번호

### <a name="format"></a>서식

8자리 숫자 뒤에 1개 문자

### <a name="pattern"></a>패턴

8자리 숫자 뒤에 1개 문자:

- 8자리 숫자
- 1개 숫자 또는 문자(대/소문자 구분 안 함)

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- 드라이버 라이선스
- driverslicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- drivers 라이선스
- drivers 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- 드라이버 (o) #
- 드라이버 ' s 라이선스 ' #
- 드라이버 ' s 라이선스 ' #
- driver'slicence #
- driver'slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 추진 라이선스 
- 추진 라이선스
- dlno #
- 드라이브 lic
- 드라이브 licen
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이버 licen
- 드라이버 licen
- 드라이버의 licen
- lic 구동
- 주행
- 운전 라이선스
- 추진 라이선스
- 주행 라이선스
- 촉진 허용
- dl 아니요
- dlno
- dl 번호


#### <a name="keywords_spain_eu_drivers_license_number"></a>Keywords_spain_eu_driver ' s_license_number

- permiso de conducción
- permiso conducción
- licencia de conducir
- licencia conducir
- permiso conducir
- permiso de conducir
- permisos de conducir
- permisos conducir
- carnet conducir
- carnet de conducir
- licencia de manejo
- licencia manejo

## <a name="spain-dni"></a>스페인 DNI
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

8자리 숫자 뒤에 1개 문자

### <a name="pattern"></a>패턴

7자리 숫자 뒤에 1개 문자

- 8자리 숫자
- 선택적 공백 또는 하이픈
- 1개 확인 문자(대/소문자 구분 안 함)

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_spain_eu_national_id_card"></a>Keywords_spain_eu_national_id_card

- carné de identidad
- dni #
- dni
- dninúmero #
- documento nacional de identidad
- identidad único
- identidadúnico #
- 보험 번호
- 국가 식별 번호
- 국가 id
- nationalid #
- nationalidno #
- nie #
- nie
- nienúmero #
- número de identificación
- número nacional identidad
- 개인 식별 번호
- 개인 id 아니요
- 고유 id 번호
- uniqueid #

## <a name="spain-passport-number"></a>스페인 여권 번호

### <a name="format"></a>서식

문자와 숫자의 8자 또는 9자 조합(공백 또는 구분 기호 비포함)

### <a name="pattern"></a>패턴

문자와 숫자의 8자 또는 9자 조합:

- 2개 숫자 또는 문자
- 1개 숫자 또는 문자(선택 사항)
- 6자리 숫자

### <a name="checksum"></a>체크섬

해당 없음

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- 여권 번호

#### <a name="keywords_spain_eu_passport_number"></a>Keywords_spain_eu_passport_number

- pasaporte
- número pasaporte
- españa pasaporte
- números de pasaporte
- número de pasaporte
- números pasaporte
- pasaporte
- 포트 n °
- n °의 포트
- pasaporte 아니요.
- pasaporte n °
- 스페인 passport

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="spain-social-security-number-ssn"></a>스페인 SSN(사회 보장 번호)


### <a name="format"></a>형식

11-12자리 숫자

### <a name="pattern"></a>패턴

11-12자리 숫자:
- 2자리 숫자
- 슬래시(선택 사항)
- 7~8자리 숫자
- 슬래시(선택 사항)
- 2자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_spain_eu_passport_number"></a>Keywords_spain_eu_passport_number

- ssn
- ssn #
- 사회 alsecurityno
- 소셜 보안 아니요
- 사회 보장 번호
- número de la seguridad 소셜

## <a name="spain-tax-identification-number"></a>스페인 납세자 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

지정된 패턴의 7 또는 8자리 숫자 및 1 또는 2개 문자

### <a name="pattern"></a>패턴

스페인 자연인(스페인 국민 신분증 있음)

- 8자리 숫자
- 1개 대문자(대/소문자 구분)

비상주 스페인인(스페인 국민 신분증 없음)

- "L" 대문자(대/소문자 구분)
- 7자리 숫자
- 1개 대문자(대/소문자 구분)

14년 미만 상주 스페인인(스페인 국민 신분증 없음):

- "K" 대문자(대/소문자 구분)
- 7자리 숫자
- 1개 대문자(대/소문자 구분)

외국인(외국인 식별 번호 있음)

- "X", "Y" 또는 "Z"인 1개 대문자(대/소문자 구분)
- 7자리 숫자
- 1개 대문자(대/소문자 구분)

외국인(외국인 식별 번호 없음)

- "M"인 1개 대문자(대/소문자 구분)
- 7자리 숫자
- 1개 대문자(대/소문자 구분)

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_spain_eu_tax_file_number"></a>Keywords_spain_eu_tax_file_number

- cif
- cifid #
- cifnúmero #
- número de contribuyente
- número de identificación 회계
- número de impuesto corporativo
- spanishcifid #
- spanishcifid
- spanishcifno #
- spanishcifno
- 세금 파일 번호
- 세금 파일 번호
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #

## <a name="sweden-drivers-license-number"></a>스웨덴 운전 면허 번호

### <a name="format"></a>형식

10자리 숫자, 하이픈 포함

### <a name="pattern"></a>패턴

10자리 숫자(하이픈 포함):

- 6자리 숫자
- 하이픈
- 4자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- 드라이버 라이선스
- driverslicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- drivers 라이선스
- drivers 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- 드라이버 (o) #
- 드라이버 ' s 라이선스 ' #
- 드라이버 ' s 라이선스 ' #
- driver'slicence #
- driver'slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 추진 라이선스 
- 추진 라이선스
- dlno #
- 드라이브 lic
- 드라이브 licen
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이버 licen
- 드라이버 licen
- 드라이버의 licen
- lic 구동
- 주행
- 운전 라이선스
- 추진 라이선스
- 주행 라이선스
- 촉진 허용
- dl 아니요
- dlno
- dl 번호


#### <a name="keywords_sweden_eu_drivers_license_number"></a>Keywords_sweden_eu_driver ' s_license_number

- ajokortti
- permis de conducere
- ajokortin numero
- kuljettajat lic.
- drivere lic.
- körkort
- numărul permisului de conducere
-  שאָפער דערלויבעניש נומער
- förare lic.
-  דריווערס דערלויבעניש
- körkortsnummer

## <a name="sweden-national-id"></a>스웨덴 신분증 ID

### <a name="format"></a>형식

10자리 또는 12자리 숫자와 선택적 구분 기호

### <a name="pattern"></a>패턴

10자리 또는 12자리 숫자와 선택적 구분 기호:
- 2자리 숫자(선택 사항)
- 6자리 숫자, 날짜 형식 YYMMDD
- "-" 또는 "+" 구분 기호 (선택 사항)
- 4자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_swedish_national_identifier"></a>Keywords_swedish_national_identifier

- id 번호
- id 번호
- a-id #
- 식별 안 함
- 식별 번호
- identifikationsnumret #
- identifikationsnumret
- identitetshandling
- id 문서
- id 아니요
- id 번호
- id-nummer
- 개인 id
- personnummer #
- personnummer
- skatteidentifikationsnummer

## <a name="sweden-passport-number"></a>스웨덴 여권 번호

### <a name="format"></a>형식

8자리 숫자

### <a name="pattern"></a>패턴

연속된 숫자 8개

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- passport #
- passport #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- 여권 번호

#### <a name="keyword_sweden_passport"></a>Keyword_sweden_passport

- 외계인 등록 카드
- g3 처리 요금
- 여러 항목
- Numéro de 포트
- 포트 n °
- 가 아닌 포트
- 포트 #
- 포트 #
- 고가 아닌 포트
- 고가
- passnummer
- nr 전달
- schengen
- s 구아젠 퇴사
- 단일 항목
- sverige pass
- 필수 조건
- 비주얼 처리
- 2018년 3

#### <a name="keywords_eu_passport_date"></a>Keywords_eu_passport_date

- 문제 날짜
- 만료 날짜


## <a name="sweden-tax-identification-number"></a>스웨덴 납세자 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

지정된 패턴의 10자리 숫자 및 1개 기호

### <a name="pattern"></a>패턴

10자리 숫자 및 1개 기호:

- 생년월일에 해당하는 6자리 숫자(YYMMDD)
- 더하기 기호 또는 빼기 기호
- 식별 번호를 고유하게 지정하는 세 자리 숫자:
  - 1990년 이전에 발급된 번호의 경우 7번째 및 8번째 숫자는 출생지 또는 외국 출생의 사람을 식별합니다.
  - 9번째 자리의 숫자는 성별을 남성의 경우 홀수 또는 여성의 경우 짝수로 나타냅니다.
- 1자리 확인 번호

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_sweden_eu_tax_file_number"></a>Keywords_sweden_eu_tax_file_number

- 개인 ID 번호
- personnummer
- 일반 ID nummer
- 수식어 식별
- beta identifikationsnummer
- sverige tin
- 세금 파일
- 세금 ID
- 세금 식별 번호
- 세금 ID 번호
- 세금 번호 #
- 세금 번호
- 세금 번호
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin no
- 주석 #


## <a name="swift-code"></a>SWIFT 코드

### <a name="format"></a>형식

4개 문자 뒤에 5-31개 문자 또는 숫자

### <a name="pattern"></a>패턴

4개 문자 뒤에 5-31개 문자 또는 숫자:
- 4자로 된 은행 코드(대/소문자 구분 안 함)
- 공백(선택 사항)
- 4-28개 문자 또는 숫자(BBAN(Basic Bank Account Number))
- 공백(선택 사항)
- 1~3개 문자 또는 숫자(BBAN의 나머지)

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_swift"></a>Keyword_swift

- 국제 표준화 조직 9362
- iso 9362
- iso9362
- 스위프트 #
- swiftcode
- swiftnumber
- swiftroutingnumber
- swift 코드
- swift 번호 #
- swift 라우팅 번호
- bic number
- bic 코드
- 빅 #
- 빅 #
- 은행 식별자 코드
- 조직 국제 정규화 해제 9362
- rapide #
- 코드 SWIFT
- le numéro de swift
- swift numéro d'acheminement
- le numéro BIC
- \# 빅
- code identificateur de banque
- SWIFT 작업
- SWIFT番号
- BIC番号
- BIC의
- SWIFT 키
- SWIFT 番号
- BIC 番号
- BIC의
- 金融機関識別コード
- 金融機関コード
- 銀行コード

## <a name="switzerland-ssn-ahv-number"></a>스위스 SSN AHV 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

13자리 숫자

### <a name="pattern"></a>패턴

13자리 숫자:

- 3자리 숫자 - 756
- 선택적 점
- 4자리 숫자
- 선택적 점
- 4자리 숫자
- 선택적 점
- 2자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_swiss_ssn_ahv_number"></a>Keyword_swiss_ssn_AHV_number

- ahv
- Ssn
- pid
- 보험 번호
- personalidno #
- 사회 보장 번호
- 개인 ID 번호
- 개인 식별 아니요.
- insuranceno #
- uniqueidno #
- 고유 ID 아니요.
- avs number
- 개인 ID versicherungsnummer 없음
- identifikationsnummer
- einzigartige identitzit nicht
- sozialversicherungsnummer
- ID 담당자 유휴 상태
- numéro de sécurité sociale


## <a name="taiwan-national-identification-number"></a>대만 국가 식별 번호

### <a name="format"></a>형식

1개 문자(영문) 뒤에 9자리 숫자

### <a name="pattern"></a>패턴

1개 문자(영문) 뒤에 9자리 숫자:
- 한 문자(영어, 대/소문자 구분 안 함)
- 숫자 "1" 또는 "2"
- 8자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_taiwan_national_id"></a>Keyword_taiwan_national_id

- 身份證字號
- 身份證
- 身份證號碼
- 身份證號
- 身分證字號
- 身分證
- 身分證號碼
- 身份證號
- 身分證統一編號
- 國民身分證統一編號
- 簽名
- 蓋章
- 簽名或蓋章
- 簽章

## <a name="taiwan-passport-number"></a>대만 여권 번호

### <a name="format"></a>형식

- 생체 인식 여권 번호: 9자리 숫자
- 비 생체 인식 여권 번호: 9자리 숫자

### <a name="pattern"></a>패턴
생체 인식 여권 번호:
- 문자 "3"
- 8자리 숫자

비 생체 인식 여권 번호:
- 9자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_taiwan_passport"></a>Keyword_taiwan_passport

- ROC Passport 번호
- Passport 번호
- Passport 아니요
- Passport Num
- 여권 #
- 护照
- 中華民國護照
- Zhónghuó Mónguó hózhóo

## <a name="taiwan-resident-certificate-arctarc-number"></a>대만 거류증(ARC/TARC) 번호

### <a name="format"></a>형식

10개의 문자와 숫자:

### <a name="pattern"></a>패턴

10개의 문자와 숫자:
- 두 문자(대/소문자 구분 안 함)
- 8자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_taiwan_resident_certificate"></a>Keyword_taiwan_resident_certificate

- 상주 인증서
- 상주 인증서
- 상주 인증서.
- 식별 카드
- 외계인 상주 인증서
- 원호의
- 대만 영역 상주 인증서
- TARC
- 居留證
- 外僑居留證
- 台灣地區居留證

## <a name="thai-citizen-id"></a>태국어 시민 ID

### <a name="format"></a>형식

13자리

### <a name="pattern"></a>패턴

13자리 숫자:
- 첫 번째 숫자는 0 또는 9가 아닙니다.
- 12자리

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_thai_citizen_id"></a>Keyword_thai_citizen_Id

- ID 번호
- 식별 번호
- บัตรประชาชน
- รหัสบัตรประชาชน
- บัตรประชาชน
- รหัสบัตรประชาชน

## <a name="turkish-national-identification-number"></a>터키어 국가 식별 번호

### <a name="format"></a>형식

11자리 숫자

### <a name="pattern"></a>패턴

11자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_turkish_national_id"></a>Keyword_turkish_national_id

- TC Kimlik 아니요
- TC Kimlik numarası
- Vatandaşlık numarası
- Vatandaşlık

## <a name="uk-drivers-license-number"></a>영국 운전 면허 번호

### <a name="format"></a>형식

지정된 형식의 18개 문자와 숫자 조합

### <a name="pattern"></a>패턴

18개의 문자와 숫자:
- 문자 대신 5 개 문자 (대/소문자 구분 안 함) 또는 숫자 "9"
- 한 자릿수.
- 날짜 형식의 5 자리 숫자는 생년월일을 MMDDY 합니다. 드라이버가 여성 인 경우 일곱 번째 문자는 50 씩 증가 합니다. 예를 들어 51은 01 ~ 12 대신 62입니다.
- 문자 대신 두 문자 (대/소문자 구분 안 함) 또는 숫자 "9"를 포함 합니다.
- 5 자리 숫자입니다.

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_drivers_license_number"></a>Keywords_eu_driver ' s_license_number

- driverlic
- driverlics
- driverlicense
- driverlicenses
- driverlicence
- driverlicences
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- driverslic
- driverslics
- 드라이버 라이선스
- driverslicences
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 lic
- 드라이버 lics
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- 드라이버 라이선스
- drivero
- driverlics
- drivers 라이선스
- drivers 라이선스
- drivers 라이선스
- driver'licences
- 드라이버 ' lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버 ' 라이선스
- driver'slic
- 드라이버 (o)
- 드라이버 ' s 라이선스 '
- 드라이버 ' s 라이선스 '
- driver'slicence
- driver'slicences
- 드라이버의 lic
- 드라이버의 lics
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- 드라이버의 라이선스
- dl #
- 된다 #
- driverlic #
- driverlics #
- driverlicense #
- driverlicenses #
- driverlicence #
- driverlicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslic #
- driverslics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- driverslicences #
- 드라이버 lic #
- 드라이버 lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- drivero #
- driverlics #
- drivers 라이선스 #
- drivers 라이선스 #
- drivers 라이선스 #
- driver'licences #
- 드라이버 ' lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버 ' 라이선스 #
- driver'slic #
- 드라이버 (o) #
- 드라이버 ' s 라이선스 ' #
- 드라이버 ' s 라이선스 ' #
- driver'slicence #
- driver'slicences #
- 드라이버의 lic #
- 드라이버의 lics #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 드라이버의 라이선스 #
- 추진 라이선스 
- 추진 라이선스
- dlno #
- 드라이브 lic
- 드라이브 licen
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이브 라이선스
- 드라이버 licen
- 드라이버 licen
- 드라이버의 licen
- lic 구동
- 주행
- 운전 라이선스
- 추진 라이선스
- 주행 라이선스
- 촉진 허용
- dl 아니요
- dlno
- dl 번호


## <a name="uk-electoral-roll-number"></a>영국 선거인 명부 번호

### <a name="format"></a>형식

2개 문자 뒤에 1-4자리 숫자

### <a name="pattern"></a>패턴

두 문자 (대/소문자 구분 안 함)와 1-4 번호

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_uk_electoral"></a>Keyword_uk_electoral

- 협의회 지명
- 지명 양식
- 선거인 등록
- 선거인 명부


## <a name="uk-national-health-service-number"></a>영국 국민 건강 보험 번호

### <a name="format"></a>형식

10-17자리 숫자, 공백으로 구분

### <a name="pattern"></a>패턴

10-17자리 숫자:
- 3 자리 또는 10 자리
- 공백
- 3자리 숫자
- 공백
- 4자리 숫자

### <a name="checksum"></a>체크섬

Yes

### <a name="keywords"></a>키워드

#### <a name="keyword_uk_nhs_number"></a>Keyword_uk_nhs_number

- 국가별 상태 관리 서비스
- nhs
- 상태 관리 서비스 기관
- 상태 권한

#### <a name="keyword_uk_nhs_number1"></a>Keyword_uk_nhs_number1

- 환자 id
- 환자 식별
- 환자 없음
- 환자 수

#### <a name="keyword_uk_nhs_number_dob"></a>Keyword_uk_nhs_number_dob

- GP
- DOB
- D. .B
- Date of Birth
- Birth Date

## <a name="uk-national-insurance-number-nino"></a>영국 NINO(국민 보험 번호)
이 중요 한 정보 형식 엔터티는 EU 국가별 식별 번호 중요 정보 형식에 포함 되어 있습니다. 독립 실행형 중요 한 정보 형식 엔터티로도 사용할 수 있습니다.

### <a name="format"></a>형식

7개 문자 또는 9개 문자, 공백 또는 대시로 구분

### <a name="pattern"></a>패턴

2가지 가능한 패턴:

- 두 문자 (올바른 NINOs는이 접두사의 특정 문자만 사용 하 여이 패턴의 유효성을 검사 하 고 대/소문자 구분 안 함)
- 6자리 숫자
- ' A ', ' B ', ' C ' 또는 ' d ' (접두사와 같이 접미사는 특정 문자만 사용할 수 있음)는 대/소문자를 구분 하지 않습니다.

또는

- 2개 문자
- 공백 또는 대시
- 2자리 숫자
- 공백 또는 대시
- 2자리 숫자
- 공백 또는 대시
- 2자리 숫자
- 공백 또는 대시
- 'A', 'B', 'C', 'D' 중 하나

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_uk_nino"></a>Keyword_uk_nino

- 국가 보험 번호
- 국가 보험 기여
- 보호 작동
- 보험
- 사회 보장 번호
- 보험 응용 프로그램
- 의료 응용 프로그램
- 소셜 보험
- 의료 주의
- 소셜 보안
- great britain
- NI 번호
- NI.
- NI #
- NI #
- 회사 #
- insurancenumber
- nationalinsurance #
- nationalinsurancenumber


## <a name="uk-unique-taxpayer-reference-number"></a>영국 고유 납세자 참조 번호
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>형식

10자리 숫자, 공백 및 구분 기호 없음


### <a name="pattern"></a>패턴

10개의 자릿수

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_uk_eu_tax_file_number"></a>Keywords_uk_eu_tax_file_number

- 세금 번호
- 세금 파일
- 세금 id
- 세금 식별 번호
- 세금 식별 번호
- 세금 없음 #
- 세금 없음
- 세금 등록 번호
- taxid #
- taxidno #
- taxidnumber #
- taxno #
- taxnumber #
- taxnumber
- tin id
- tin
- tin #

## <a name="us-bank-account-number"></a>미국 은행 계좌 번호

### <a name="format"></a>형식

6-17자리 숫자

### <a name="pattern"></a>패턴

연속된 숫자 6-17개

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_usa_bank_account"></a>Keyword_usa_Bank_Account

- 계정 번호 확인
- 계정 확인 중
- 계정 확인 #
- Acct 번호 확인
- Acct 확인 #
- Acct 아니요를 선택합니다.
- 계정 번호 확인
- 은행 계좌 번호
- 은행 계좌 #
- 은행 누적 번호
- Bank Acct #
- Bank Acct 아니요.
- 은행 계좌 번호
- 절감액 계좌 번호
- 절감액 계정.
- 절감액 계정 #
- 절감액 수
- 절감액 #
- Savings Acct No.
- Savings Account 아니요.
- 직불 계정 번호
- 직불 계정
- 직불 계정 #
- 직불 Acct 번호
- 직불 Acct #
- Debit Acct 아니요.
- 직불 계정 번호

## <a name="us-drivers-license-number"></a>미국 운전 면허 번호

### <a name="format"></a>형식

주별로 다름

### <a name="pattern"></a>패턴

은 주(예: 뉴욕)에 따라 달라집니다.
- ddd ddd ddd와 같은 형식의 9자리가 일치합니다.
- 9자리 숫자, ddddddddd와 같은 형식은 일치하지 않음

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_us_drivers_license_abbreviations"></a>Keyword_us_drivers_license_abbreviations

- DL
- DLS
- CDL
- CDLS
- ID
- ID
- DL #
- DL #
- CDL #
- CDLS #
- 자료 #
- Id #
- ID 번호
- ID 번호
- LIC
- LIC #

#### <a name="keyword_us_drivers_license"></a>Keyword_us_drivers_license

- DriverLic
- DriverLics
- DriverLicense
- DriverLicenses
- Driver Lic
- 드라이버 Lics
- 드라이버 라이선스
- 드라이버 라이선스
- DriversLic
- DriversLics
- DriversLicense
- DriversLicenses
- Drivers Lic
- 드라이버Lics
- 드라이버 라이선스
- 드라이버 라이선스
- Driver'Lic
- Driver'Lics
- 운전면허
- 운전면허
- Driver' Lic
- 드라이버의 Lics
- 운전면허
- 운전 기사 라이선스
- Driver'sLic
- Driver'sLics
- 드라이버의License
- 드라이버의License
- Driver's Lic
- 드라이버의 Lics
- 운전면허증
- 운전 기사의 라이선스
- ID 번호
- ID 번호
- 식별 #
- ID 카드
- ID 카드
- ID 카드
- ID 카드
- DriverLic #
- DriverLics #
- DriverLicense #
- DriverLicenses #
- Driver Lic #
- 드라이버 Lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- DriversLic #
- DriversLics #
- DriversLicense #
- DriversLicenses #
- Drivers Lic #
- 드라이버Lics #
- 드라이버 라이선스 #
- 드라이버 라이선스 #
- Driver'Lic #
- Driver'Lics #
- 운전면허 #
- 운전면허 #
- Driver' Lic #
- 드라이버의 Lics #
- 운전면허 #
- 운전 기사 라이선스 #
- Driver'sLic #
- Driver'sLics #
- 드라이버의License #
- 드라이버의License #
- Driver's Lic #
- 드라이버의 Lics #
- 운전면허증 #
- 운전 기사의 라이선스 #
- ID 카드 #
- ID 카드 #
- ID 카드 #
- ID 카드 #


#### <a name="keyword_state_name_drivers_license_name"></a>Keyword_[state_name]_drivers_license_name

- 주 약어(예: "NY")
- 주 이름(예: "New York")

## <a name="us-individual-taxpayer-identification-number-itin"></a>미국 ITIN(개인 납세자 식별 번호)

### <a name="format"></a>형식

"9"로 시작하고 네 번째 숫자로 "7" 또는 "8"을 포함하는 9자리이며 선택적으로 공백 또는 대시로 서식이 지정됩니다.

### <a name="pattern"></a>패턴

형식 있음:
- 숫자 "9"
- 2자리 숫자
- 공백 또는 대시
- "7" 또는 "8"
- 1자리 숫자
- 공백 또는 대시
- 4자리 숫자

형식 없음:
- 숫자 "9"
- 2자리 숫자
- "7" 또는 "8"
- 5자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_itin"></a>Keyword_itin

- 납세자
- 세금 ID
- 세금 식별
- itin
- i.t.i.n.
- Ssn
- 주석
- 사회 보장
- 세금 지급자
- itins
- taxid
- 개별 권해


## <a name="us-social-security-number-ssn"></a>미국 SSN(사회 보장 번호)

### <a name="format"></a>형식

9자리 숫자, 형식이 있거나 없는 패턴일 수 있음

> [!NOTE]
> 2011년 중반 이전에 발급된 경우 SSN에 강력히 형식이 적용됨, 숫자의 특정 부분이 특정 범위 내에 있어야 유효함(체크섬은 없음)

### <a name="pattern"></a>패턴

4가지 함수가 4가지 다른 패턴으로 SSN을 찾음:
- Func_ssn 대시 또는 공백으로 서식이 지정된 2011년 이전의 강력한 서식이 있는 SSN을 찾습니다(ddd-dd-dddd OR dddd dd).
- Func_unformatted_ssn 9자리 연속 숫자(dddddd)로 서식이 지정되지 않은 2011년 이전의 강력한 서식을 가진 SSN을 찾습니다.
- Func_randomized_formatted_ssn 대시 또는 공백으로 서식이 지정된 2011년 이후 SSN을 찾습니다(ddd-dd-dddd OR dddd dddd).
- Func_randomized_unformatted_ssn 9자리 연속 숫자로 서식이 변경되지 않은 2011년 이후 SSN을 찾습니다(dddddddddd).

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_ssn"></a>Keyword_ssn

- SSA 번호
- 사회 보장 번호
- 사회 보장 #
- 사회 보장 #
- 사회 보장 아니요
- 사회 보장 #
- Soc Sec
- SSN
- SSNS
- SSN #
- SS #
- SSID

## <a name="us--uk-passport-number"></a>미국/영국 여권 번호

### <a name="format"></a>형식

9자리 숫자

### <a name="pattern"></a>패턴

연속된 숫자 9개

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keywords_eu_passport_number"></a>Keywords_eu_passport_number

- 여권 #
- 여권 #
- passportid
- 여권
- passportno
- passport 아니요
- passportnumber
- 여권 번호
- passportnumbers
- passport numbers

#### <a name="keywords_uk_eu_passport_number"></a>Keywords_uk_eu_passport_number

- 영국 Passport
- 영국 passport


## <a name="ukraine-passport-domestic"></a>우크라이나 여권 번호(국내)
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>형식

9자리 숫자

### <a name="pattern"></a>패턴

9자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_ukraine_passport_domestic"></a>Keyword_ukraine_passport_domestic

- passport passport
- 여권 번호
- passport 아니요
- паспорт України
- номер паспорта
- персональний


## <a name="ukraine-passport-international"></a>우크라이나 여권 번호(국제)
이 중요 정보 형식은 다음에만 사용할 수 있습니다.
- 데이터 손실 방지 정책
- 커뮤니케이션 준수 정책
- 정보 거버넌스
- 레코드 관리
- Microsoft Cloud App Security

### <a name="format"></a>서식

8자 영숫자 패턴

### <a name="pattern"></a>패턴

8자 영숫자 패턴:
- 2개 문자 또는 숫자
- 6자리 숫자

### <a name="checksum"></a>체크섬

예

### <a name="keywords"></a>키워드

#### <a name="keyword_ukraine_passport_international"></a>Keyword_ukraine_passport_international

- passport passport
- 여권 번호
- passport 아니요
- паспорт України
- номер паспорта
