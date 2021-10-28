---
title: Azure Active Directory에 대한 SAP SuccessFactors 특성 참조
description: Azure Active Directory의 SuccessFactors-HR 기반 프로비저닝에서 지원하는 SuccessFactors의 특성을 알아봅니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: 83e3a0462543131298ee9460225db015d960bc52
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990789"
---
# <a name="sap-successfactors-attribute-reference-for-azure-active-directory"></a>Azure Active Directory에 대한 SAP SuccessFactors 특성 참조

이 문서에서는 다음에 대한 정보를 찾을 수 있습니다.

- [SuccessFactors 엔터티 및 특성](#supported-successfactors-entities-and-attributes)
- [기본 특성 매핑](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>지원되는 SuccessFactors 엔터티 및 특성

아래 표에서는 다음과 같은 두 프로비저닝 앱에 기본적으로 포함된 SuccessFactors 특성 목록을 캡처합니다.

- [SuccessFactors에서 Active Directory로 사용자 프로비저닝](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SuccessFactors에서 Active AD로 사용자 프로비저닝](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

추가 특성에 대한 스키마를 확장하려면 [SAP SuccessFactors 통합 참조](./sap-successfactors-integration-reference.md#retrieving-additional-attributes)를 참조하세요. 

| \# | SuccessFactors 엔터티                  | SuccessFactors 특성     | 작업 유형 |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | 읽기           |
| 2  | PerPerson                              | personId                     | 읽기           |
| 3  | PerPerson                              | perPersonUuid                | 읽기           |
| 4  | PerPersonal                            | displayName                  | 읽기           |
| 5  | PerPersonal                            | firstName                    | 읽기           |
| 6  | PerPersonal                            | gender                       | 읽기           |
| 7  | PerPersonal                            | lastName                     | 읽기           |
| 8  | PerPersonal                            | middleName                   | 읽기           |
| 9  | PerPersonal                            | preferredName                | 읽기           |
| 10 | 사용자                                   | addressLine1                 | 읽기           |
| 11 | 사용자                                   | addressLine2                 | 읽기           |
| 12 | 사용자                                   | addressLIne3                 | 읽기           |
| 13 | 사용자                                   | businessPhone                | 읽기           |
| 14 | 사용자                                   | cellPhone                    | 읽기           |
| 15 | 사용자                                   | city                         | 읽기           |
| 16 | 사용자                                   | country                      | 읽기           |
| 17 | 사용자                                   | custom01                     | 읽기           |
| 18 | 사용자                                   | custom02                     | 읽기           |
| 19 | 사용자                                   | custom03                     | 읽기           |
| 20 | 사용자                                   | custom04                     | 읽기           |
| 21 | 사용자                                   | custom05                     | 읽기           |
| 22 | 사용자                                   | custom06                     | 읽기           |
| 23 | 사용자                                   | custom07                     | 읽기           |
| 24 | 사용자                                   | custom08                     | 읽기           |
| 25 | 사용자                                   | custom09                     | 읽기           |
| 26 | 사용자                                   | custom10                     | 읽기           |
| 27 | 사용자                                   | custom11                     | 읽기           |
| 28 | 사용자                                   | custom12                     | 읽기           |
| 29 | 사용자                                   | custom13                     | 읽기           |
| 30 | 사용자                                   | custom14                     | 읽기           |
| 31 | 사용자                                   | empId                        | 읽기           |
| 32 | 사용자                                   | homePhone                    | 읽기           |
| 33 | 사용자                                   | jobFamily                    | 읽기           |
| 34 | 사용자                                   | nickname                     | 읽기           |
| 35 | 사용자                                   | state                        | 읽기           |
| 36 | 사용자                                   | timeZone                     | 읽기           |
| 37 | 사용자                                   | 사용자 이름                     | 읽기           |
| 38 | 사용자                                   | zipCode                      | 읽기           |
| 39 | PerPhone                               | areaCode                     | 읽기           |
| 40 | PerPhone                               | countryCode                  | 읽기           |
| 41 | PerPhone                               | 확장                    | 읽기           |
| 42 | PerPhone                               | phoneNumber                  | 읽기           |
| 43 | PerPhone                               | phoneType                    | 읽기           |
| 44 | PerEmail                               | emailAddress                 | 읽기, 쓰기    |
| 45 | PerEmail                               | emailType                    | 읽기           |
| 46 | EmpEmployment                          | firstDateWorked              | 읽기           |
| 47 | EmpEmployment                          | lastDateWorked               | 읽기           |
| 48 | EmpEmployment                          | userId                       | 읽기           |
| 49 | EmpEmployment                          | isContingentWorker           | 읽기           |
| 50 | EmpJob                                 | countryOfCompany             | 읽기           |
| 51 | EmpJob                                 | emplStatus                   | 읽기           |
| 52 | EmpJob                                 | endDate                      | 읽기           |
| 53 | EmpJob                                 | startDate                    | 읽기           |
| 54 | EmpJob                                 | jobTitle                     | 읽기           |
| 55 | EmpJob                                 | position                     | 읽기           |
| 65 | EmpJob                                 | customString13               | 읽기           |
| 56 | EmpJob                                 | managerId                    | 읽기           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | 읽기           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | 읽기           |
| 59 | EmpJob\.Company                        | company                      | 읽기           |
| 60 | EmpJob\.Company                        | companyId                    | 읽기           |
| 61 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode           | 읽기           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | 읽기           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | 읽기           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | 읽기           |
| 65 | EmpJob\.Department                     | department                   | 읽기           |
| 66 | EmpJob\.Department                     | departmentId                 | 읽기           |
| 67 | EmpJob\.Division                       | division                     | 읽기           |
| 68 | EmpJob\.Division                       | divisionId                   | 읽기           |
| 69 | EmpJob\.JobCode                        | jobCode                      | 읽기           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | 읽기           |
| 71 | EmpJob\.Location                       | LocationName                 | 읽기           |
| 72 | EmpJob\.Location                       | officeLocationAddress        | 읽기           |
| 73 | EmpJob\.Location                       | officeLocationCity           | 읽기           |
| 74 | EmpJob\.Location                       | officeLocationCustomString4  | 읽기           |
| 75 | EmpJob\.Location                       | officeLocationZipCode        | 읽기           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | 읽기           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | 읽기           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | 읽기           |

## <a name="default-attribute-mapping"></a>기본 특성 매핑

아래 표에서는 위에 나열된 SuccessFactors 특성과 AD/Azure AD 특성 간의 기본 특성 매핑을 제공합니다. Azure AD 프로비저닝 앱 "매핑" 블레이드에서는 위의 목록에서 특성을 포함하도록 이 기본 매핑을 수정할 수 있습니다. 

| \# | SuccessFactors 엔터티                  | SuccessFactors 특성 | 기본 AD/Azure AD 특성 매핑   | 주석 처리                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | 일치 특성으로 사용                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[매핑되지 않은 \-가 원본 앵커로 사용됨\] | 초기 동기화 중에 Provisioning Service는 personUuid를 기존 objectGuid\에 연결합니다.  |
| 3  | PerPersonal                            | displayName              | displayName                             | 해당 없음                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | 해당 없음                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | 해당 없음                                                                                           |
| 6  | 사용자                                   | addressLine1             | streetAddress                           | 해당 없음                                                                                           |
| 7  | 사용자                                   | city                     | l                                       | 해당 없음                                                                                           |
| 8  | 사용자                                   | country                  | co                                      | 해당 없음                                                                                           |
| 9  | 사용자                                   | state                    | st                                      | 해당 없음                                                                                           |
| 10 | 사용자                                   | 사용자 이름                 | samAccountName                          | 해당 없음                                                                                           |
| 11 | 사용자                                   | zipCode                  | postalCode                              | 해당 없음                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | 해당 없음                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | 해당 없음                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | 해당 없음                                                                                           |
| 15 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode       | c                                       | 해당 없음                                                                                           |
| 16 | EmpJob\.Department                     | department               | department                              | 해당 없음                                                                                           |
| 17 | EmpJob\.Division                       | division                 | company                                 | 해당 없음                                                                                           |
| 18 | EmpJob\.Location                       | officeLocationAddress    | streetAddress                           | 해당 없음                                                                                           |
| 19 | EmpJob\.Location                       | officeLocationZipCode    | postalCode                              | 해당 없음                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | activeEmploymentsCount = 0인 경우 account\를 사용하지 않도록 설정합니다.                                           |
