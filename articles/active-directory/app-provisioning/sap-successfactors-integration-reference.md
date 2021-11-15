---
title: Azure Active Directory 및 SAP SuccessFactors 통합 참조
description: Azure Active Directory용 SAP SuccessFactors-HR 기반 프로비저닝에 대한 기술 심층 분석입니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 10/11/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: 8c215c8b032fb3981771d2091b449b1934e78533
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990833"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>Azure Active Directory 프로비저닝이 SAP SuccessFactors와 통합되는 방법 

[Azure Active Directory 사용자 프로비저닝 서비스](../app-provisioning/user-provisioning.md)는 사용자의 ID 수명 주기를 관리하기 위해 [SAP SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)과 통합됩니다. Azure Active Directory는 미리 빌드된 세 가지 통합을 제공합니다. 

* [SuccessFactors에서 온-프레미스 Active Directory로 사용자 프로비저닝](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors에서 Azure Active Directory로 사용자 프로비저닝](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)
* [SuccessFactors 쓰기 저장](../saas-apps/sap-successfactors-writeback-tutorial.md)

이 문서에서는 통합이 작동하는 방식과 여러 HR 시나리오에 대한 프로비저닝 동작을 사용자 지정하는 방법을 설명합니다. 

## <a name="establishing-connectivity"></a>연결 설정 
Azure AD 프로비저닝 서비스는 기본 인증을 사용하여 Employee Central OData API 엔드포인트에 연결합니다. SuccessFactors 프로비저닝 앱을 설정할 때 '관리자 자격 증명' 섹션의 '테넌트 URL' 매개 변수를 사용하여 [API 데이터 센터 URL](https://apps.support.sap.com/sap/support/knowledge/en/2215682)을 구성합니다. 

Azure AD 프로비저닝 서비스와 SuccessFactors 간의 연결을 더욱 안전하게 보호하기 위해 아래에 설명된 단계를 사용하여 SuccessFactors IP 허용 목록에 Azure AD IP 범위를 추가할 수 있습니다.

1. Azure 퍼블릭 클라우드의 [최신 IP 범위](https://www.microsoft.com/download/details.aspx?id=56519)를 다운로드합니다. 
1. 파일을 열고 **AzureActiveDirectory** 태그 검색 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP 범위](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. *addressPrefixes* 요소 내에 나열된 모든 IP 주소 범위를 복사하고 범위를 사용하여 IP 주소 제한 목록을 작성합니다.
1. CIDR 값을 IP 범위로 변환합니다.  
1. SuccessFactors 관리 포털에 로그인하여 허용 목록에 IP 범위를 추가합니다. SAP [지원 노트 2253200](https://apps.support.sap.com/sap/support/knowledge/en/2253200)을 참조하세요. 이제 이 도구에 [IP 범위를 입력할](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) 수 있습니다. 

## <a name="supported-entities"></a>지원되는 엔터티
SuccessFactors의 모든 사용자에 대해 Azure AD 프로비저닝 서비스는 다음 엔터티를 검색합니다. 각 엔터티는 OData API *$expand* 쿼리 매개 변수를 사용하여 확장됩니다. 아래의 '검색 규칙' 열을 참조하세요. 일부 엔터티는 기본적으로 확장되지만 일부 엔터티는 매핑에 특정 특성이 있는 경우에만 확장됩니다. 

| \# | SuccessFactors 엔터티                  | OData 노드     | 검색 규칙 |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | 루트 노드                  | 항상           |
| 2  | PerPersonal                            | personalInfoNav              | 항상           |
| 3  | PerPhone                               | phoneNav                     | 항상           |
| 4  | PerEmail                               | emailNav                     | 항상           |
| 5  | EmpEmployment                          | employmentNav                | 항상           |
| 6  | 사용자                                   | employmentNav/userNav        | 항상           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | 항상           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | 항상           |
| 9  | 사용자의 관리자                         | employmentNav/userNav/manager/empInfo | 항상  |
| 10 | FOCompany                              | employmentNav/jobInfoNav/companyNav | `company` 또는 `companyId` 특성이 매핑된 경우에만 |
| 11 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | `department` 또는 `departmentId` 특성이 매핑된 경우에만 |
| 12 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | `businessUnit` 또는 `businessUnitId` 특성이 매핑된 경우에만 |
| 13 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | `costCenter` 또는 `costCenterId` 특성이 매핑된 경우에만 |
| 14 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | `division` 또는 `divisionId` 특성이 매핑된 경우에만 |
| 15 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | `jobCode` 또는 `jobCodeId` 특성이 매핑된 경우에만 |
| 16 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | `payGrade` 특성이 매핑된 경우에만 |
| 17 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | `location` 특성이 매핑된 경우에만 |
| 18 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | 매핑에 다음 특성 중 하나가 포함되는 경우: `officeLocationAddress,  officeLocationCity, officeLocationZipCode` |
| 19 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | `eventReason` 특성이 매핑된 경우에만 |
| 20 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | `assignmentType`이 매핑된 경우에만 |
| 21 | EmploymentType 선택 목록                | employmentNav/jobInfoNav/employmentTypeNav | `employmentType`이 매핑된 경우에만 |
| 22 | EmployeeClass 선택 목록                 | employmentNav/jobInfoNav/employeeClassNav | `employeeClass`가 매핑된 경우에만 |
| 23 | EmplStatus 선택 목록                    | employmentNav/jobInfoNav/emplStatusNav | `emplStatus`가 매핑된 경우에만 |
| 24 | AssignmentType 선택 목록                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | `assignmentType`이 매핑된 경우에만 |
| 25 | 위치                               | employmentNav/jobInfoNav/positionNav | `positioNav`이 매핑된 경우에만 |
| 26 | 관리자 사용자                           | employmentNav/jobInfoNav/managerUserNav | `managerUserNav`이 매핑된 경우에만 |

## <a name="how-full-sync-works"></a>전체 동기화 방식
특성 매핑을 기반으로 Azure AD 프로비저닝 서비스는 전체 동기화를 수행하는 동안 다음 'GET' OData API 쿼리를 전송하여 모든 활성 사용자의 유효 데이터를 가져옵니다. 

> [!div class="mx-tdCol2BreakAll"]
>| 매개 변수 | 설명 |
>| ----------|-------------|
>| OData API 호스트 | 테넌트 URL에 https를 추가합니다. 예: `https://api4.successfactors.com` |
>| OData API 엔드포인트 | `/odata/v2/PerPerson` |
>| OData $format 쿼리 매개 변수 | `json` |
>| OData $filter 쿼리 매개 변수 | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| OData $expand 쿼리 매개 변수 | 이 매개 변수 값은 매핑된 특성에 따라 다릅니다. 예: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| OData customPageSize 쿼리 매개 변수 | `100` |

> [!NOTE]
> 첫 번째 초기 전체 동기화 중에 Azure AD 프로비저닝 서비스는 비활성/해지된 작업자 데이터를 가져오지 않습니다.

각 SuccessFactors 사용자에 대해 프로비저닝 서비스는 매핑에 정의된 일치 특성을 사용하여 대상(Azure AD/온-프레미스 Active Directory)의 계정을 찾습니다. 예: *personIdExternal* 이 *employeeId* 에 매핑되고 일치 특성으로 설정된 경우 프로비저닝 서비스는 *personIdExternal* 값을 사용하여 *employeeId* 필터가 포함된 사용자를 검색합니다. 사용자 일치 항목이 발견되면 대상 특성을 업데이트합니다. 일치 항목을 찾을 수 없는 경우 대상에 새 항목을 만듭니다. 

특정 `personIdExternal`에 대해 OData API 엔드포인트에서 반환된 데이터의 유효성을 검사하려면 데이터 센터 서버 URL을 사용하여 아래의 API 쿼리의 `SuccessFactorsAPIEndpoint`를 업데이트하고 [Postman](https://www.postman.com/downloads/) 같은 도구를 사용하여 쿼리를 호출합니다. 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>증분 동기화 방식

전체 동기화 후에 Azure AD 프로비저닝 서비스는 `LastExecutionTimestamp`를 유지 관리하고 사용하여 증분 변경 내용을 검색하는 델타 쿼리를 만듭니다. `lastModifiedDateTime`, `startDate`, `endDate`, `latestTerminationDate` 등 각 SuccessFactors 엔터티에 있는 타임스탬프 특성은 변경 내용이 `LastExecutionTimestamp`와 `CurrentExecutionTime` 사이에 속하는지 여부를 확인하기 위해 평가됩니다. 예일 경우 항목 변경 내용이 유효한 것으로 간주되고 동기화를 위해 처리됩니다. 

## <a name="reading-attribute-data"></a>특성 데이터 읽기

Azure AD 프로비저닝 서비스는 SuccessFactors를 쿼리할 때 JSON 결과 집합을 검색합니다. JSON 결과 집합은 Employee Central에 저장된 여러 특성을 포함합니다. 기본적으로 프로비저닝 스키마는 해당 특성의 하위 집합만 검색하도록 구성됩니다. 

추가 특성을 검색하려면 아래 나열된 단계를 따르세요.
    
1. **엔터프라이즈 애플리케이션** -> **SuccessFactors 앱** -> **프로비저닝** -> **프로비저닝 편집** -> **특성 매핑 페이지** 로 이동합니다.
1. 아래로 스크롤하여 **고급 옵션 표시** 를 클릭합니다.
1. **SuccessFactors 특성 목록 편집** 을 클릭합니다. 

   > [!NOTE] 
   > Azure Portal에 **SuccessFactors 특성 목록 편집** 옵션이 표시되지 않으면 *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* URL을 사용하여 해당 페이지에 액세스합니다. 

1. 이 보기의 **API 식** 열에 커넥터가 사용하는 JSONPath 식이 표시됩니다.

   >[!div class="mx-imgBorder"] 
   >![API 식](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  

1. 기존 JSONPath 값을 편집하거나 유효한 JSONPath 식으로 새 특성을 스키마에 추가할 수 있습니다. 

다음 섹션에서는 JSONPath 값을 편집하는 일반적인 시나리오의 목록을 제공합니다. 

## <a name="handling-different-hr-scenarios"></a>여러 HR 시나리오 처리

JSONPath는 XML에 대한 XPath처럼 JSON에 대한 쿼리 언어입니다. XPath와 마찬가지로 JSONPath를 사용하여 JSON 페이로드에서 데이터를 추출하고 필터링할 수 있습니다.

JSONPath 변환을 사용하여 Azure AD 프로비저닝 앱의 동작을 사용자 지정하여 사용자 지정 특성을 검색하고 재고용, 작업자 전환 및 글로벌 배치와 같은 시나리오를 처리할 수 있습니다. 

이 섹션에서는 다음과 같은 HR 시나리오에 대해 프로비저닝 앱을 사용자 지정할 수 있는 방법을 설명합니다. 
* [추가 특성 검색](#retrieving-additional-attributes)
* [사용자 지정 특성 검색](#retrieving-custom-attributes)
* [작업자 전환 시나리오 처리](#handling-worker-conversion-scenario)
* [재고용 시나리오 처리](#handling-rehire-scenario)
* [글로벌 배치 시나리오 처리](#handling-global-assignment-scenario)
* [동시 직무 처리 시나리오](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>추가 특성 검색

기본 Azure AD SuccessFactors 프로비저닝 앱 스키마는 [90개 이상의 미리 정의된 특성](sap-successfactors-attribute-reference.md)과 함께 제공됩니다. 프로비저닝 스키마에 더 많은 SuccessFactors 특성을 추가하려면 아래 나열된 단계를 사용합니다. 

1. 아래 OData 쿼리를 사용하여 Employee Central에서 유효한 테스트 사용자에 대한 데이터를 검색합니다. 

   ```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
   ```

1. 특성과 연결된 Employee Central 엔터티를 확인합니다.
   * 특성이 *EmpEmployment* 엔터티의 일부인 경우 *employmentNav* node 아래에서 특성을 찾습니다. 
   * 특성이 *User* 엔터티의 일부인 경우 *employmentNav/userNav* 노드 아래에서 특성을 찾습니다.
   * 특성이 *EmpJob* 엔터티의 일부인 경우 *employmentNav/jobInfoNav* 노드 아래에서 특성을 찾습니다. 
1. 특성과 연결된 JSON 경로를 생성하고 이 새 특성을 SuccessFactors 특성 목록에 추가합니다. 
   * 예제 1: *employmentNav* 엔터티의 일부인 *okToRehire* 특성을 추가한 다음 JSONPath `$.employmentNav.results[0].okToRehire`를 사용하려는 경우
   * 예제 2: *userNav* 엔터티의 일부인 *timeZone* 특성을 추가한 다음 JSONPath `$.employmentNav.results[0].userNav.timeZone`을 사용하려는 경우
   * 예제 3: *jobInfoNav* 엔터티의 일부인 *flsaStatus* 특성을 추가한 다음 JSONPath `$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`을 사용하려는 경우
1. 스키마를 저장합니다. 
1. 프로비저닝을 다시 시작합니다.

### <a name="retrieving-custom-attributes"></a>사용자 지정 특성 검색

기본적으로 다음과 같은 사용자 지정 특성은 Azure AD SuccessFactors 프로비저닝 앱에 미리 정의되어 있습니다. 
* User(userNav) 엔터티의 *custom01-custom15*
* *empNavCustomString1-empNavCustomString15* 라는 EmpEmployment(employmentNav) 엔터티의 *customString1-customString15*
* *empJobNavCustomString1-empNavJobCustomString15* 라는 EmpJobInfo(jobInfoNav) 엔터티의 *customString1-customString15*

예를 들어 Employee Central에서 *EmpJobInfo* 의 *customString35* 특성이 위치 설명을 저장합니다. 이 값을 Active Directory *physicalDeliveryOfficeName* 특성으로 이동하려고 합니다. 이 시나리오에 대해 특성 매핑을 구성하려면 아래 단계를 사용합니다. 

1. SuccessFactors 특성 목록을 편집하여 *empJobNavCustomString35* 라는 새 특성을 추가합니다.
1. 이 특성에 대한 JSONPath API 식을 다음과 같이 설정합니다. `$.employmentNav.results[0].jobInfoNav.results[0].customString35`
1. Azure Portal에서 매핑 변경 내용을 저장하고 다시 로드합니다.  
1. 특성 매핑 블레이드에서 *empJobNavCustomString35* 를 *physicalDeliveryOfficeName* 에 매핑합니다.
1. 변경 내용을 저장합니다.

이 시나리오를 확장합니다. 
* *User* 엔터티에서 *custom35* 특성을 매핑하려면 JSONPath `$.employmentNav.results[0].userNav.custom35`를 사용합니다.
* *EmpEmployment* 엔터티에서 *customString35* 특성을 매핑하려면 JSONPath `$.employmentNav.results[0].customString35`를 사용합니다.

### <a name="handling-worker-conversion-scenario"></a>작업자 전환 시나리오 처리

작업자 전환은 기존 직원을 계약직 작업자로 또는 계약직 작업자를 직원으로 전환하는 프로세스입니다. 이 시나리오에서 Employee Central은 동일한 *Person* 엔터티에 대해 새 *User* 엔터티와 함께 새 *EmpEmployment* 엔터티를 추가합니다. 이전 *EmpEmployment* 엔터티 아래에 중첩된 *User* 엔터티는 Null로 설정됩니다. 전환이 발생할 때 새 고용 데이터가 표시되도록 이 시나리오를 처리하려면 아래 나열된 단계를 사용하여 프로비저닝 앱 스키마를 대량 업데이트할 수 있습니다.  

1. SuccessFactors 프로비저닝 앱의 특성 매핑 블레이드를 엽니다. 
1. 아래로 스크롤하여 **고급 옵션 표시** 를 클릭합니다.
1. **여기에서 스키마 검토** 링크를 클릭하여 스키마 편집기를 엽니다. 

   >![스키마 편집기를 여는 여기에서 스키마 검토 링크를 보여 주는 스크린샷](media/sap-successfactors-integration-reference/review-schema.png#lightbox)

1. **다운로드** 링크를 클릭하여 편집하기 전에 스키마 복사본을 저장합니다. 

   >![스키마 복사본을 저장하기 위해 다운로드가 선택된 스키마 편집기를 보여 주는 스크린샷](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
1. 스키마 편집기에서 Ctrl + H 키를 눌러 찾기-바꾸기 컨트롤을 엽니다.
1. 찾기 텍스트 상자에서 값 `$.employmentNav.results[0]`를 복사하여 붙여넣습니다.
1. 바꾸기 텍스트 상자에서 값 `$.employmentNav.results[?(@.userNav != null)]`을 복사하여 붙여넣습니다. `!=` 연산자 주위의 공백은 JSONPath 식을 성공적으로 처리하는 데 중요합니다. 
   >![find-replace-conversion](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
1. '모두 바꾸기' 옵션을 클릭하여 스키마를 업데이트합니다. 
1. 스키마를 저장합니다. 
1. 위의 프로세스는 다음과 같이 모든 JSONPath 식을 업데이트합니다. 
   * 이전 JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 새 JSONPath: `$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
1. 프로비저닝을 다시 시작합니다. 

### <a name="handling-rehire-scenario"></a>재고용 시나리오 처리

일반적으로 재고용을 처리하는 옵션은 두 가지가 있습니다. 
* 옵션 1: Employee Central에서 새 사용자 프로필 만들기
* 옵션 2: Employee Central에서 기존 사용자 프로필 다시 사용

HR 프로세스에서 옵션 1을 사용하는 경우 프로비저닝 스키마를 변경할 필요가 없습니다. HR 프로세스에서 옵션 2를 사용하는 경우 Employee Central은 동일한 *Person* 엔터티에 대한 새 *User* 엔터티와 함께 새 *EmpEmployment* 엔터티를 추가합니다. 전환 시나리오와 달리 이전 *EmpEmployment* 엔터티의 *User* 엔터티는 Null로 설정되지 않습니다. 

재고용 프로필에 최신 고용 데이터가 표시되도록 이 재고용 시나리오(옵션 2)를 처리하려면 아래 나열된 단계를 사용하여 프로비저닝 앱 스키마를 대량 업데이트할 수 있습니다.  

1. SuccessFactors 프로비저닝 앱의 특성 매핑 블레이드를 엽니다. 
1. 아래로 스크롤하여 **고급 옵션 표시** 를 클릭합니다.
1. **여기에서 스키마 검토** 링크를 클릭하여 스키마 편집기를 엽니다.   
1. **다운로드** 링크를 클릭하여 편집하기 전에 스키마 복사본을 저장합니다.   
1. 스키마 편집기에서 Ctrl + H 키를 눌러 찾기-바꾸기 컨트롤을 엽니다.
1. 찾기 텍스트 상자에서 값 `$.employmentNav.results[0]`를 복사하여 붙여넣습니다.
1. 바꾸기 텍스트 상자에서 값 `$.employmentNav.results[-1:]`을 복사하여 붙여넣습니다. 이 JSONPath 식은 최신 *EmpEmployment* 레코드를 반환합니다.   
1. '모두 바꾸기' 옵션을 클릭하여 스키마를 업데이트합니다. 
1. 스키마를 저장합니다. 
1. 위의 프로세스는 다음과 같이 모든 JSONPath 식을 업데이트합니다. 
   * 이전 JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 새 JSONPath: `$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
1. 프로비저닝을 다시 시작합니다. 

이 스키마 변경은 작업자 전환 시나리오도 지원합니다. 

### <a name="handling-global-assignment-scenario"></a>글로벌 배치 시나리오 처리

Employee Central의 사용자가 글로벌 배치를 위해 처리되면 SuccessFactors는 새 *EmpEmployment* 엔터티를 추가하고 *assignmentClass* 를 'GA'로 설정합니다. 또한 새 *User* 엔터티를 만듭니다. 따라서 이제 사용자에게 다음이 포함됩니다.
* *assignmentClass* 가 'ST'로 설정된 국내 배치에 해당하는 1개의 *EmpEmployment* + *User* 엔터티 및 
* *assignmentClass* 가 'GA'로 설정된 글로벌 배치에 해당하는 또 다른 1개의 *EmpEmployment* + *User* 엔터티

표준 배치 및 글로벌 배치 사용자 프로필에 속하는 특성을 가져오려면 아래 나열된 단계를 사용합니다. 

1. SuccessFactors 프로비저닝 앱의 특성 매핑 블레이드를 엽니다. 
1. 아래로 스크롤하여 **고급 옵션 표시** 를 클릭합니다.
1. **여기에서 스키마 검토** 링크를 클릭하여 스키마 편집기를 엽니다.   
1. **다운로드** 링크를 클릭하여 편집하기 전에 스키마 복사본을 저장합니다.   
1. 스키마 편집기에서 Ctrl + H 키를 눌러 찾기-바꾸기 컨트롤을 엽니다.
1. 찾기 텍스트 상자에서 값 `$.employmentNav.results[0]`를 복사하여 붙여넣습니다.
1. 바꾸기 텍스트 상자에서 값 `$.employmentNav.results[?(@.assignmentClass == 'ST')]`를 복사하여 붙여넣습니다. 
1. '모두 바꾸기' 옵션을 클릭하여 스키마를 업데이트합니다. 
1. 스키마를 저장합니다. 
1. 위의 프로세스는 다음과 같이 모든 JSONPath 식을 업데이트합니다. 
   * 이전 JSONPath: `$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
   * 새 JSONPath: `$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
1. 앱의 특성 매핑 블레이드를 다시 로드합니다. 
1. 아래로 스크롤하여 **고급 옵션 표시** 를 클릭합니다.
1. **SuccessFactors 특성 목록 편집** 을 클릭합니다.
1. 새 특성을 추가하여 글로벌 배치 데이터를 가져옵니다. 예를 들어 글로벌 배치 프로필에 연결된 부서 이름을 가져오려는 경우 JSONPath 식이 `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized`로 설정된 *globalAssignmentDepartment* 특성을 추가할 수 있습니다. 
1. 이제 두 부서 값을 Active Directory 특성으로 전달하거나 식 매핑을 사용하여 선택적으로 값을 전달할 수 있습니다. 예제: 아래 식은 AD *department* 특성의 값을 *globalAssignmentDepartment*(있는 경우)로 설정하고, 다른 값은 표준 배치와 연결된 *department* 로 설정합니다. 
   * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`

1. 변경 내용을 저장합니다. 
1. 프로비저닝을 다시 시작합니다. 

### <a name="handling-concurrent-jobs-scenario"></a>동시 직무 처리 시나리오

Employee Central의 사용자에게 동시/다중 직무가 있는 경우 *assignmentClass* 가 'ST'로 설정된 두 개의 *EmpEmployment* 및 *User* 엔터티가 있습니다. 두 직무가 모두 속하는 특성을 가져오려면 아래 나열된 단계를 사용합니다. 

1. SuccessFactors 프로비저닝 앱의 특성 매핑 블레이드를 엽니다. 
1. 아래로 스크롤하여 **고급 옵션 표시** 를 클릭합니다.
1. **SuccessFactors 특성 목록 편집** 을 클릭합니다.
1. 직무 1 및 직무 2와 연결된 부서를 풀하려 한다고 가정해 보겠습니다. 미리 정의된 *department* 특성은 이미 첫 번째 직무의 부서 값을 가져옵니다. *secondJobDepartment* 라는 새 특성을 정의하고 JSONPath 식을 `$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`로 설정할 수 있습니다.
1. 이제 두 부서 값을 Active Directory 특성으로 전달하거나 식 매핑을 사용하여 선택적으로 값을 전달할 수 있습니다. 
1. 변경 내용을 저장합니다. 
1. 프로비저닝을 다시 시작합니다. 

### <a name="retrieving-position-details"></a>위치 세부 정보 검색

SuccessFactors 커넥터는 위치 개체의 확장을 지원합니다. 특정 언어에서 작업 수준 또는 위치 이름과 같은 위치 개체 특성을 확장하고 검색하려면 아래와 같이 JSONPath 식을 사용할 수 있습니다. 

| 특성 이름 | JSONPath 식 |
| -------------- | ------------------- |
| positionJobLevel | $.employmentNav.results[0].jobInfoNav.results[0].positionNav.jobLevel |
| positionNameFR | $.employmentNav.results[0].jobInfoNav.results[0].positionNav.externalName_fr_FR |
| positionNameDE | $.employmentNav.results[0].jobInfoNav.results[0].positionNav.externalName_de_DE |

## <a name="writeback-scenarios"></a>쓰기 저장 시나리오

이 섹션에서는 다양한 쓰기 저장 시나리오에 대해 설명합니다. SuccessFactors에서 이메일 및 전화 번호를 설정하는 방법에 따라 권장하는 구성 방법이 달라집니다.

### <a name="supported-scenarios-for-phone-and-email-write-back"></a>전화 및 이메일 쓰기 저장에 지원되는 시나리오 

| \# | 시나리오 요구 사항 | 이메일 기본 <br> 플래그 값 | 회사 전화 <br> 기본 플래그 값 | 휴대폰 <br> 기본 플래그 값 | 회사 전화 <br> 매핑 | 휴대폰 <br> 매핑 |
|--|--|--|--|--|--|--|
| 1 | * 회사 이메일만 기본으로 설정합니다. <br> * 전화 번호를 설정하지 않습니다. | true | true | false | \[설정 안 됨\] | \[설정 안 됨\] | 
| 2 | * SuccessFactors에서 회사 이메일 및 회사 전화는 기본입니다. <br> * 항상 Azure AD 전화 번호를 회사 전화에 전달하고 모바일을 휴대폰에 전달합니다. | true | true | false | telephoneNumber | mobile | 
| 3 | * SuccessFactors에서 회사 이메일 및 휴대폰은 기본입니다. <br> * 항상 Azure AD 전화 번호를 회사 전화에 전달하고 휴대폰을 휴대폰에 전달합니다. | true | false | true |  telephoneNumber | mobile | 
| 4 | * SuccessFactors에서 회사 이메일은 기본입니다. <br> * Azure AD에서 회사 전화 번호가 있는지 확인하고, 있는 경우 휴대폰 번호가 있는지 확인하고, 휴대폰 번호가 있는 경우에만 회사 전화 번호를 기본으로 표시합니다. | true | 식 매핑 사용: `IIF(IsPresent([telephoneNumber]), IIF(IsPresent([mobile]),"false", "true"), "false")` | 식 매핑 사용: `IIF(IsPresent([mobile]),"false", "true")` | telephoneNumber | mobile | 
| 5 | * SuccessFactors에서 회사 이메일 및 회사 전화는 기본입니다. <br> * Azure AD에서 휴대폰을 사용할 수 있는 경우 이를 회사 전화로 설정하고, 그렇지 않으면 telephoneNumber를 사용합니다. | true | true | false | `IIF(IsPresent([mobile]), [mobile], [telephoneNumber])` | \[설정 안 됨\] | 

* 쓰기 저장 특성 매핑에 전화 번호 매핑이 없는 경우에는 이메일만 쓰기 저장에 포함됩니다.
* Employee Central에서 신입 직원을 등록할 때는 회사 이메일 및 전화 번호를 사용할 수 없습니다. 등록 중에 회사 이메일 및 회사 전화를 기본으로 설정하는 것이 필수인 경우 신입 직원을 생성할 때 회사 전화 및 이메일에 대한 더미 값을 설정할 수 있습니다. 이 값은 나중에 쓰기 저장 앱에 의해 업데이트됩니다.
 
### <a name="enabling-writeback-with-userid"></a>UserID를 사용하여 쓰기 저장을 사용하도록 설정

SuccessFactors 쓰기 저장 앱은 다음 논리를 사용하여 사용자 개체 특성을 업데이트합니다. 
* 첫 번째 단계로 변경 집합에서 *userId* 특성을 찾습니다. 이 특성이 있는 경우 SuccessFactors API 호출을 위해 "UserId"를 사용합니다. 
* *userId* 가 없는 경우 기본적으로 *personIdExternal* 특성 값을 사용합니다. 

일반적으로 SuccessFactors의 *personIdExternal* 특성 값은 *userId* 특성 값과 일치합니다. 그러나 재고용 및 작업자 전환과 같은 시나리오에서 SuccessFactors의 직원은 두 개의 고용 레코드(하나는 활성 및 하나는 비활성)를 가질 수 있습니다. 이러한 시나리오에서 쓰기 저장이 활성 사용자 프로필을 업데이트하도록 하려면 아래 설명된 대로 SuccessFactors 프로비저닝 앱의 구성을 업데이트하세요. 이 구성은 *userId* 가 커넥터에 표시되는 변경 집합에 항상 존재하고 SuccessFactors API 호출에 사용되도록 합니다.

1. Azure AD 사용자 프로비저닝 앱에서 SuccessFactors를 열거나 온-프레미스 AD 사용자 프로비저닝 앱에서 SuccessFactors를 엽니다. 
1. Azure AD의 extensionAttribute *(extensionAttribute1-15)* 는 모든 작업자의 활성 고용 레코드의 *userId* 를 항상 저장해야 합니다. 이는 Azure AD의 extensionAttribute에 SuccessFactors *userId* 특성을 매핑하여 달성할 수 있습니다. 
    > [!div class="mx-imgBorder"]
    > ![인바운드 UserID 특성 매핑](./media/sap-successfactors-integration-reference/inbound-userid-attribute-mapping.png)
1. JSONPath 설정에 대한 지침은 [재고용 시나리오 처리](#handling-rehire-scenario) 섹션을 참조하여 활성 고용 레코드의 *userId* 값이 Azure AD로 흐르는지 확인하세요. 
1. 변경 내용을 저장합니다. 
1. 프로비저닝 작업을 실행하여 *userId* 값이 Azure AD로 흐르도록 합니다. 
    > [!NOTE]
    > 온-프레미스 Active Directory 사용자 프로비저닝에 SuccessFactors를 사용하는 경우 온-프레미스 Active Directory의 *userId* 특성 값을 Azure AD로 동기화하도록 AAD Connect를 구성합니다.   
1. Azure Portal에서 SuccessFactors 쓰기 저장 앱을 엽니다. 
1. userId 값을 포함하는 원하는 *extensionAttribute* 를 SuccessFactors *userId* 특성에 매핑합니다.
    > [!div class="mx-imgBorder"]
    > ![쓰기 저장 UserID 특성 매핑](./media/sap-successfactors-integration-reference/userid-attribute-mapping.png)
1. 변경 내용을 저장합니다. 
1. *특성 매핑 -> 고급 -> 스키마 검토* 로 이동하여 JSON 스키마 편집기를 엽니다.
1. 스키마 복사본을 백업으로 다운로드합니다. 
1. 스키마 편집기에서 Ctrl-F를 누르고 userId 매핑이 포함된 JSON 노드를 검색합니다. 여기서 원본 Azure AD 특성에 매핑됩니다. 
1. 아래와 같이 flowBehavior 특성을 "FlowWhenChanged"에서 "FlowAlways"로 업데이트합니다. 
    > [!div class="mx-imgBorder"]
    > ![매핑 흐름 동작 업데이트](./media/sap-successfactors-integration-reference/mapping-flow-behavior-update.png)
1. 주문형 프로비저닝을 사용하여 변경 내용을 저장하고 쓰기 저장 시나리오를 테스트합니다. 

### <a name="unsupported-scenarios-for-phone-and-email-write-back"></a>전화 및 이메일 쓰기 저장에 대해 지원하지 않는 시나리오

* Employee Central에서 등록하는 동안 개인 이메일 및 개인 전화는 기본으로 설정됩니다. 쓰기 저장 앱은 이 설정을 변경하고 회사 이메일 및 회사 전화를 기본으로 설정할 수 없습니다.
* Employee Central에서 회사 전화는 기본으로 설정됩니다. 쓰기 저장 앱은 이 설정을 변경하고 휴대폰을 기본으로 설정할 수 없습니다.
* 쓰기 저장 앱은 현재 기본 플래그 설정을 읽고 쓰기 작업에 동일한 값을 사용할 수 없습니다. 특성 매핑에 구성된 플래그 값이 항상 사용됩니다. 

## <a name="next-steps"></a>다음 단계

* [SuccessFactors에서 Active Directory로 프로비저닝을 구성하는 방법을 알아봅니다.](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors에 쓰기 저장을 구성하는 방법을 알아봅니다.](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [인바운드 프로비저닝에 지원되는 SuccessFactors 특성에 대해 자세히 알아봅니다.](sap-successfactors-attribute-reference.md)










