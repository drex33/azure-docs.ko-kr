---
title: Azure Active Directory 및 Workday 통합 참조
description: Azure Active Directory의 Workday-HR 기반 프로비저닝에 대한 기술 심층 분석
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 06/01/2021
ms.author: kenwith
ms.reviewer: arvinh, chmutali
ms.openlocfilehash: 1a1b0dfbdad6aaea91ca8e3fe7d11d74b1aa2967
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990352"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-workday"></a>Azure Active Directory 프로비저닝이 Workday와 통합되는 방법

[Azure Active Directory 사용자 프로비저닝 서비스](../app-provisioning/user-provisioning.md)는 사용자의 ID 수명 주기를 관리하기 위해 [Workday HCM](https://www.workday.com)과 통합됩니다. Azure Active Directory는 미리 빌드된 세 가지 통합을 제공합니다. 

* [온-프레미스 Active Directory 사용자 프로비저닝에 대한 Workday](../saas-apps/workday-inbound-tutorial.md)
* [Azure Active Directory 사용자 프로비저닝에 대한 Workday](../saas-apps/workday-inbound-cloud-only-tutorial.md)
* [Workday 쓰기 저장](../saas-apps/workday-writeback-tutorial.md)

이 문서에서는 통합이 작동하는 방식과 여러 HR 시나리오에 대한 프로비저닝 동작을 사용자 지정하는 방법을 설명합니다. 

## <a name="establishing-connectivity"></a>연결 설정 

### <a name="restricting-workday-api-access-to-azure-ad-endpoints"></a>Azure AD 엔드포인트에 대한 Workday API 액세스 제한
Azure AD 프로비저닝 서비스는 기본 인증을 사용하여 Workday 웹 서비스 API 엔드포인트에 연결합니다.  

Azure AD 프로비저닝 서비스와 Workday 간의 연결을 더욱 안전하게 보호하기 위해, 지정된 통합 시스템 사용자가 허용되는 Azure AD IP 범위에서 Workday API에만 액세스하도록 액세스를 제한할 수 있습니다. Workday 테넌트에서 다음 구성을 완료하려면 Workday 관리자에게 문의하세요. 

1. Azure 퍼블릭 클라우드의 [최신 IP 범위](https://www.microsoft.com/download/details.aspx?id=56519) 다운로드 
1. 파일을 열고 **AzureActiveDirectory** 태그 검색 

   >[!div class="mx-imgBorder"] 
   >![Azure AD IP 범위](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

1. *addressPrefixes* 요소 내에 나열된 모든 IP 주소 범위를 복사하고 범위를 사용하여 IP 주소 목록을 작성합니다.
1. Workday 관리자 포털에 로그인합니다. 
1. **IP 범위 유지 관리** 작업에 액세스하여 Azure 데이터 센터에 대한 새 IP 범위를 만듭니다. IP 범위(CIDR 표기법 사용)를 쉼표로 구분된 목록으로 지정합니다.  
1. **인증 정책 관리** 작업에 액세스하여 새 인증 정책을 만듭니다. 인증 정책에서 인증 허용 목록을 사용하여 Azure AD IP 범위 및 해당 IP 범위에서 액세스가 허용되는 보안 그룹을 지정합니다. 변경 내용을 저장합니다. 
1. 변경 내용을 확인하려면 **모든 보류 중인 인증 정책 변경 내용 활성화** 작업에 액세스합니다.

### <a name="limiting-access-to-worker-data-in-workday-using-constrained-security-groups"></a>제한된 보안 그룹을 사용하여 Workday의 작업자 데이터에 대한 액세스 제한

[Workday 통합 시스템 사용자를 구성](../saas-apps/workday-inbound-tutorial.md#configure-integration-system-user-in-workday)하는 기본 단계에서는 Workday 테넌트의 모든 사용자를 검색할 수 있는 액세스 권한을 부여합니다. 특정 통합 시나리오에서, 특정 감독 기관에만 속하는 사용자가 Get_Workers API 호출에서 반환되고 Workday Azure AD 커넥터에서 처리되도록 액세스를 제한할 수 있습니다. 

Workday 관리자와 협력하고 제한된 통합 시스템 보안 그룹을 구성하여 해당 요구 사항을 충족할 수 있습니다. 해당 작업을 수행하는 방법에 대한 자세한 내용은 [관련 Workday 커뮤니티 문서](https://community.workday.com/forums/customer-questions/620393)를 참조하세요(*이 문서에 액세스하려면 Workday 커뮤니티 로그인 자격 증명이 필요함*).

제한된 ISSG(통합 시스템 보안 그룹)를 사용하여 액세스를 제한하는 전략은 특히 다음과 같은 시나리오에서 유용합니다. 
* **단계적 출시 시나리오**: Workday 테넌트가 크고 Azure AD 자동화된 프로비저닝에서 Workday를 단계적으로 출시하는 계획입니다. 이 시나리오에서는 Azure AD 범위 지정 필터를 사용하여 현재 단계의 범위 내에 없는 사용자를 제외하는 것이 아니라, 범위 내 작업자만 Azure AD에 표시되도록 제한된 ISSG를 구성하는 것이 좋습니다.
* **여러 프로비저닝 작업 시나리오**: 각각 다른 비즈니스 단위/부서/회사를 지원하는 Workday 테넌트가 크고 여러 AD 도메인이 있습니다. 이 토폴로지를 지원하기 위해 특정 작업자 집합을 프로비저닝하는 각 작업을 사용하여 여러 Workday에서 Azure AD 프로비저닝 작업을 실행하려 합니다. 이 시나리오에서는 Azure AD 범위 지정 필터를 사용하여 작업자 데이터를 제외하는 대신, 관련 작업자 데이터만 Azure AD에 표시되도록 제한된 ISSG를 구성하는 것이 좋습니다. 

### <a name="workday-test-connection-query"></a>Workday 연결 테스트 쿼리

Workday 연결을 테스트하기 위해 Azure AD는 다음 *Get_Workers* Workday 웹 서비스 요청을 보냅니다. 

```XML
<!-- Test connection query tries to retrieve one record from the first page -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to the test connection event -->
<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:28:50.1491022Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:28:50.1491022Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
    <p1:Exclude_Employees>true</p1:Exclude_Employees>
    <p1:Exclude_Contingent_Workers>true</p1:Exclude_Contingent_Workers>
    <p1:Exclude_Inactive_Workers>true</p1:Exclude_Inactive_Workers>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:28:50.1491022Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:28:50.1491022Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>1</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="how-full-sync-works"></a>전체 동기화 방식

Workday 기반 프로비저닝의 컨텍스트에서 **전체 동기화** 는 Workday에서 모든 ID를 가져오고 각 작업자 개체에 적용할 프로비저닝 규칙을 결정하는 프로세스를 말합니다. 처음으로 프로비저닝을 켜고 Azure Portal 또는 Graph API를 사용하여 *프로비저닝을 다시 시작* 하는 경우 전체 동기화가 수행됩니다. 

Azure AD는 다음 *Get_Workers* Workday 웹 서비스 요청을 보내 작업자 데이터를 검색합니다. 이 쿼리는 전체 동기화 실행에 해당하는 시간 동안 개시 날짜가 지정된 모든 작업자 항목에 대해 Workday 트랜잭션 로그를 조회합니다. 

```XML
<!-- Workday full sync query -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to full sync run -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Type_References>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Hire Employee</p1:ID>
        </p1:Transaction_Type_Reference>
        <p1:Transaction_Type_Reference>
          <p1:ID p1:type="Business_Process_Type">Contract Contingent Worker</p1:ID>
        </p1:Transaction_Type_Reference>
      </p1:Transaction_Type_References>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Transaction_Log_Data>1</p1:Include_Transaction_Log_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```
*Response_Group* 노드는 Workday에서 가져올 작업자 특성을 지정하는 데 사용됩니다. *Response_Group* 노드의 각 플래그에 대한 설명은 Workday [Get_Workers API 설명서](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v35.2/Get_Workers.html#Worker_Response_GroupType)를 참조하세요. 

*Response_Group* 노드에 지정된 특정 플래그 값은 Workday Azure AD 프로비저닝 애플리케이션에 구성된 특성을 기반으로 계산됩니다. 플래그 값을 설정하는 데 사용되는 조건에 대해 *지원되는 엔터티* 섹션을 참조하세요. 

위의 쿼리에 대해 Workday의 *Get_Workers* 응답에는 작업자 레코드 수 및 페이지 수가 포함됩니다.

```XML
  <wd:Response_Results>
    <wd:Total_Results>509</wd:Total_Results>
    <wd:Total_Pages>17</wd:Total_Pages>
    <wd:Page_Results>30</wd:Page_Results>
    <wd:Page>1</wd:Page>
  </wd:Response_Results>
```
결과 집합의 다음 페이지를 검색하기 위해 다음 *Get_Workers* 쿼리는 페이지 번호를 *Response_Filter* 의 매개 변수로 지정합니다.

```XML
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:29:16.0094202Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:29:16.0094202Z</p1:As_Of_Entry_DateTime>
    <p1:Page>2</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
```
Azure AD 프로비저닝 서비스는 전체 동기화 중에 각 페이지를 처리하고 모든 유효 작업자를 반복합니다. Workday에서 가져온 각 작업자 항목에 대해 다음을 수행합니다.
* [XPATH 식](workday-attribute-reference.md)은 Workday에서 특성 값을 검색하는 데 적용됩니다.
* 특성 매핑 및 일치 규칙이 적용됩니다. 
* 서비스는 대상(Azure AD/AD)에서 수행할 작업을 결정합니다. 

처리가 완료되면 전체 동기화의 시작과 관련된 타임 스탬프를 워터마크로 저장합니다. 이 워터마크는 증분 동기화 주기의 시작점으로 사용됩니다. 

## <a name="how-incremental-sync-works"></a>증분 동기화 방식

전체 동기화 후에 Azure AD 프로비저닝 서비스는 이를 유지 관리하고`LastExecutionTimestamp` 사용하여 증분 변경 내용을 검색하는 델타 쿼리를 만듭니다. 증분 동기화 중에 Azure AD는 다음 유형의 쿼리를 Workday로 보냅니다. 

* [수동 업데이트용 쿼리](#query-for-manual-updates)
* [개시 날짜가 지정된 업데이트 및 종료 쿼리](#query-for-effective-dated-updates-and-terminations)
* [미래의 날짜가 지정된 고용 쿼리](#query-for-future-dated-hires)

### <a name="query-for-manual-updates"></a>수동 업데이트용 쿼리

다음 *Get_Workers* 는 마지막 실행과 현재 실행 시간 사이에 발생한 수동 업데이트에 대한 쿼리를 요청합니다. 

```xml
<!-- Workday incremental sync query for manual updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Updated_From>2021-01-19T02:29:16.0094202Z</p1:Updated_From>
        <p1:Updated_Through>2021-01-19T02:49:06.290136Z</p1:Updated_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-effective-dated-updates-and-terminations"></a>개시 날짜가 지정된 업데이트 및 종료 쿼리

다음 *Get_Workers* 는 마지막 실행과 현재 실행 시간 사이에 발생한 개시 날짜가 지정된 업데이트에 대한 쿼리를 요청합니다. 

```xml
<!-- Workday incremental sync query for effective-dated updates -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below with the UTC time corresponding to last execution and current execution time -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_Criteria>
    <p1:Transaction_Log_Criteria_Data>
      <p1:Transaction_Date_Range_Data>
        <p1:Effective_From>2021-01-19T02:29:16.0094202Z</p1:Effective_From>
        <p1:Effective_Through>2021-01-19T02:49:06.290136Z</p1:Effective_Through>
      </p1:Transaction_Date_Range_Data>
    </p1:Transaction_Log_Criteria_Data>
  </p1:Request_Criteria>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-01-19T02:49:06.290136Z</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-01-19T02:49:06.290136Z</p1:As_Of_Entry_DateTime>
    <p1:Page>1</p1:Page>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

### <a name="query-for-future-dated-hires"></a>미래의 날짜가 지정된 고용 쿼리

위의 쿼리에서 미래의 날짜가 지정된 고용을 반환하는 경우 다음 *Get_Workers* 요청을 사용하여 미래의 날짜가 지정된 새 고용에 대한 정보를 가져옵니다. 새 고용의 *WID* 특성은 조회를 수행하는 데 사용되며, 개시 날짜는 채용 날짜 및 시간으로 설정됩니다. 

```xml
<!-- Workday incremental sync query to get new hire data effective as on hire date/first day of work -->
<!-- Replace version with Workday Web Services version present in your connection URL -->
<!-- Replace timestamps below hire date/first day of work -->
<!-- Count specifies the number of records to return in each page -->
<!-- Response_Group flags derived from provisioning attribute mapping -->

<Get_Workers_Request p1:version="v21.1" xmlns:p1="urn:com.workday/bsvc" xmlns="urn:com.workday/bsvc">
  <p1:Request_References>
    <p1:Worker_Reference>
      <p1:ID p1:type="WID">7bf6322f1ea101fd0b4433077f09cb04</p1:ID>
    </p1:Worker_Reference>
  </p1:Request_References>
  <p1:Response_Filter>
    <p1:As_Of_Effective_Date>2021-02-01T08:00:00+00:00</p1:As_Of_Effective_Date>
    <p1:As_Of_Entry_DateTime>2021-02-01T08:00:00+00:00</p1:As_Of_Entry_DateTime>
    <p1:Count>30</p1:Count>
  </p1:Response_Filter>
  <p1:Response_Group>
    <p1:Include_Reference>1</p1:Include_Reference>
    <p1:Include_Personal_Information>1</p1:Include_Personal_Information>
    <p1:Include_Employment_Information>1</p1:Include_Employment_Information>
    <p1:Include_Organizations>1</p1:Include_Organizations>
    <p1:Exclude_Organization_Support_Role_Data>1</p1:Exclude_Organization_Support_Role_Data>
    <p1:Exclude_Location_Hierarchies>1</p1:Exclude_Location_Hierarchies>
    <p1:Exclude_Cost_Center_Hierarchies>1</p1:Exclude_Cost_Center_Hierarchies>
    <p1:Exclude_Company_Hierarchies>1</p1:Exclude_Company_Hierarchies>
    <p1:Exclude_Matrix_Organizations>1</p1:Exclude_Matrix_Organizations>
    <p1:Exclude_Pay_Groups>1</p1:Exclude_Pay_Groups>
    <p1:Exclude_Regions>1</p1:Exclude_Regions>
    <p1:Exclude_Region_Hierarchies>1</p1:Exclude_Region_Hierarchies>
    <p1:Exclude_Funds>1</p1:Exclude_Funds>
    <p1:Exclude_Fund_Hierarchies>1</p1:Exclude_Fund_Hierarchies>
    <p1:Exclude_Grants>1</p1:Exclude_Grants>
    <p1:Exclude_Grant_Hierarchies>1</p1:Exclude_Grant_Hierarchies>
    <p1:Exclude_Business_Units>1</p1:Exclude_Business_Units>
    <p1:Exclude_Business_Unit_Hierarchies>1</p1:Exclude_Business_Unit_Hierarchies>
    <p1:Exclude_Programs>1</p1:Exclude_Programs>
    <p1:Exclude_Program_Hierarchies>1</p1:Exclude_Program_Hierarchies>
    <p1:Exclude_Gifts>1</p1:Exclude_Gifts>
    <p1:Exclude_Gift_Hierarchies>1</p1:Exclude_Gift_Hierarchies>
    <p1:Include_Management_Chain_Data>1</p1:Include_Management_Chain_Data>
    <p1:Include_Additional_Jobs>1</p1:Include_Additional_Jobs>
  </p1:Response_Group>
</Get_Workers_Request>
```

## <a name="retrieving-worker-data-attributes"></a>작업자 데이터 특성 검색

*Get_Workers* API는 작업자와 연결된 다른 데이터 세트를 반환할 수 있습니다. 프로비저닝 스키마에 구성된 [XPATH API 식](workday-attribute-reference.md)에 따라 Azure AD 프로비저닝 서비스는 Workday에서 검색할 데이터 세트를 결정합니다. 따라서 *Get_Workers* 요청에 *Response_Group* 플래그가 설정됩니다. 

아래 표에서는 특정 데이터 세트를 검색하는 데 사용할 구성에 대한 지침을 제공합니다. 

| \# | Workday 엔터티                       | 기본적으로 포함됨 | 기본이 아닌 엔터티를 가져오기 위해 매핑할 때 지정할 XPATH 패턴입니다.             |
|----|--------------------------------------|---------------------|-------------------------------------------------------------------------------|
| 1  | 개인 데이터                        | 예                 | wd:Worker\_Data/wd:Personal\_Data                                             |
| 2  | 고용 데이터                      | 예                 | wd:Worker\_Data/wd:Employment\_Data                                           |
| 3  | 추가 작업 데이터                  | 예                 | wd:Worker\_Data/wd:Employment\_Data/wd:Worker\_Job\_Data\[@wd:Primary\_Job=0\]|
| 4  | 조직 데이터                    | 예                 | wd:Worker\_Data/wd:Organization\_Data                                         |
| 5  | 관리 체인 데이터                | 예                 | wd:Worker\_Data/wd:Management\_Chain\_Data                                    |
| 6  | 감독 기관             | 예                 | ‘감독’                                                                 |
| 7  | 회사                              | 예                 | ‘회사’                                                                     |
| 8  | 조합                        | 예                  | ‘비즈니스\_단위’                                                              |
| 9  | 비즈니스 단위 계층 구조              | 예                  | ‘비즈니스\_단위\_계층 구조’                                                   |
| 10 | 회사 계층 구조                    | 예                  | ‘회사\_계층 구조’                                                          |
| 11 | Cost Center                          | 아니요                  | ‘비용\_센터’                                                                |
| 12 | 비용 센터 계층 구조                | 예                  | ‘비용\_센터\_계층 구조’                                                     |
| 13 | 모금                                 | 예                  | ‘모금’                                                                        |
| 14 | 모금 계층 구조                       | 예                  | ‘모금\_계층 구조’                                                             |
| 15 | 선물                                 | 예                  | ‘선물’                                                                        |
| 16 | 선물 계층 구조                       | 예                  | ‘선물\_계층 구조’                                                             |
| 17 | 허용                                | 예                  | ‘권한 부여’                                                                       |
| 18 | 권한 부여 계층 구조                      | 예                  | ‘권한 부여\_계층 구조’                                                            |
| 19 | 비즈니스 사이트 계층 구조              | 예                  | ‘비즈니스\_사이트\_계층 구조’                                                   |
| 20 | 행렬 조직                  | 예                  | ‘행렬’                                                                      |
| 21 | 지불 그룹                            | 예                  | ‘지불\_그룹’                                                                  |
| 22 | 프로그램                             | 예                  | ‘프로그램’                                                                    |
| 23 | 프로그램 계층 구조                    | 예                  | ‘프로그램\_계층 구조’                                                          |
| 24 | 지역                               | 예                  | ‘지역\_계층 구조’                                                           |
| 25 | 위치 계층 구조                   | 예                  | ‘위치\_계층 구조’                                                         |
| 26 | 계정 프로비저닝 데이터            | 예                  | wd:Worker\_Data/wd:Account\_Provisioning\_Data                                |
| 27 | 백그라운드 확인 데이터                | 예                  | wd:Worker\_Data/wd:Background\_Check\_Data                                    |
| 28 | 혜택 자격 데이터             | 예                  | wd:Worker\_Data/wd:Benefit\_Eligibility\_Data                                 |
| 29 | 혜택 등록 데이터              | 예                  | wd:Worker\_Data/wd:Benefit\_Enrollment\_Data                                  |
| 30 | 커리어 데이터                          | 예                  | wd:작업자\_데이터/wd:커리어\_데이터                                               |
| 31 | 보정 데이터                    | 예                  | wd:Worker\_Data/wd:Compensation\_Data                                         |
| 32 | 불확정 작업자 세금 기관 데이터 | 예                  | wd:Worker\_Data/wd:Contingent\_Worker\_Tax\_Authority\_Form\_Type\_Data       |
| 33 | 개발 항목 데이터                | 예                  | wd:Worker\_Data/wd:Development\_Item\_Data                                    |
| 34 | 직원 계약 데이터              | 예                  | wd:Worker\_Data/wd:Employee\_Contracts\_Data                                  |
| 35 | 직원 검토 데이터                 | 예                  | wd:Worker\_Data/wd:Employee\_Review\_Data                                     |
| 36 | 수신된 피드백 데이터               | 예                  | wd:작업자\_데이터/wd:수신된\_피드백\_데이터                                   |
| 37 | 작업자 목표 데이터                     | 예                  | wd:Worker\_Data/wd:Worker\_Goal\_Data                                         |
| 38 | 사진 데이터                           | 예                  | wd:Worker\_Data/wd:Photo\_Data                                                |
| 39 | 정규화 데이터                   | 예                  | wd:Worker\_Data/wd:Qualification\_Data                                        |
| 40 | 관련된 사람 데이터                 | 예                  | wd:Worker\_Data/wd:Related\_Persons\_Data                                     |
| 41 | 역할 데이터                            | 예                  | wd:작업자\_데이터/wd:역할\_데이터                                                 |
| 42 | 기술 데이터                           | 예                  | wd:Worker\_Data/wd:Skill\_Data                                                |
| 43 | 연속 프로필 데이터              | 예                  | wd:Worker\_Data/wd:Succession\_Profile\_Data                                  |
| 44 | 인재 평가 데이터               | 예                  | wd:Worker\_Data/wd:Talent\_Assessment\_Data                                   |
| 45 | 사용자 계정 데이터                    | 예                  | wd:Worker\_Data/wd:User\_Account\_Data                                        |
| 46 | 작업자 문서 데이터                 | 예                  | wd:Worker\_Data/wd:Worker\_Document\_Data                                     |

>[!NOTE]
>테이블에 나열된 각 Workday 엔터티는 Workday의 **도메인 보안 정책** 으로 보호됩니다. 올바른 XPATH를 설정한 후 엔터티와 연결된 특성을 검색할 수 없는 경우 Workday 관리자에게 문의하여, 프로비저닝 앱과 연결된 통합 시스템 사용자에 대해 적절한 도메인 보안 정책이 구성되어 있는지 확인합니다. 예를 들어 *기술 데이터* 를 검색하려면 Workday 도메인 *작업자 데이터: 기술 및 경험* 에 대한 액세스 권한 *받기* 가 필요합니다. 

특정 요구 사항을 충족하기 위해 Workday 통합을 확장할 수 있는 몇 가지 방법은 다음과 같습니다. 

### <a name="example-1-retrieving-cost-center-and-pay-group-information"></a>예제 1: 비용 센터 검색 및 그룹 정보 지불

Workday에서 다음 데이터 세트를 검색하고 프로비저닝 규칙에서 사용하려는 경우를 가정합니다.

* 비용 센터
* 비용 센터 계층 구조
* 지불 그룹

위의 데이터 세트는 기본적으로 포함되어 있지 않습니다. 해당 데이터 세트를 검색하려면 다음과 같이 합니다.
1. Azure Portal에 로그인하고 Workday를 AD/Azure AD 사용자 프로비저닝 앱으로 엽니다. 
1. 프로비저닝 블레이드에서 매핑을 편집하고 고급 섹션에서 Workday 특성 목록을 엽니다. 
1. 다음 특성 정의를 추가하고 “필요”로 표시합니다. 해당 특성은 AD 또는 Azure AD의 특성에 매핑되지 않습니다. 해당 특성은 비용 센터, 비용 센터 계층 구조, 지불 그룹 정보를 검색하는 커넥터에 대한 신호로만 사용됩니다. 

     > [!div class="mx-tdCol2BreakAll"]
     >| 특성 이름 | Workday XPATH API 식 |
     >|---|---|
     >| CostCenterHierarchyFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='COST_CENTER_HIERARCHY']/wd:Organization_Reference/@wd:Descriptor |
     >| CostCenterFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='COST_CENTER']/wd:Organization_Data/wd:Organization_Code/text() |
     >| PayGroupFlag  |  wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='PAY_GROUP']/wd:Organization_Data/wd:Organization_Reference_ID/text() |

1. *Get_Workers* 응답에서 비용 센터 및 지불 그룹 데이터 세트를 사용할 수 있게 되면 아래 XPATH 값을 사용하여 비용 센터 이름, 비용 센터 코드, 지불 그룹을 검색할 수 있습니다. 

     > [!div class="mx-tdCol2BreakAll"]
     >| 특성 이름 | Workday XPATH API 식 |
     >|---|---|
     >| CostCenterName  | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Cost Center']/wd:Organization_Name/text() |
     >| CostCenterCode | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Cost Center']/wd:Organization_Code/text() |
     >| PayGroup | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/@wd:Descriptor='Pay Group']/wd:Organization_Name/text() |

### <a name="example-2-retrieving-qualification-and-skills-data"></a>예제 2: 자격 및 기술 데이터 검색

사용자와 연결된 인증을 검색하려는 경우를 가정합니다. 이 정보는 *자격 데이터* 집합의 일부로 사용할 수 있습니다. 해당 데이터 세트를 *Get_Workers* 응답의 일부로 가져오려면 다음 XPATH를 사용합니다. 

`wd:Worker/wd:Worker_Data/wd:Qualification_Data/wd:Certification/wd:Certification_Data/wd:Issuer/text()`

### <a name="example-3-retrieving-provisioning-group-assignments"></a>예제 3: 프로비저닝 그룹 할당 검색

작업자에게 할당된 *프로비저닝 그룹* 을 검색하려는 경우를 가정합니다. 이 정보는 *계정 프로비저닝 데이터* 집합의 일부로 사용할 수 있습니다. 해당 데이터를 *Get_Workers* 응답의 일부로 가져오려면 다음 XPATH를 사용합니다. 

`wd:Worker/wd:Worker_Data/wd:Account_Provisioning_Data/wd:Provisioning_Group_Assignment_Data[wd:Status='Assigned']/wd:Provisioning_Group/text()`

## <a name="handling-different-hr-scenarios"></a>여러 HR 시나리오 처리

### <a name="support-for-worker-conversions"></a>작업자 변환 지원

작업자가 직원에서 비정규 작업자로 또는 비정규 작업자에서 직원으로 전환하는 경우 Workday 커넥터는 이 변경 사항을 자동으로 감지하고 AD 계정을 활성 작업자 프로필에 연결하여 모든 AD 특성이 활성 작업자 프로필과 동기화되도록 합니다. 이 기능을 사용하려면 구성을 변경할 필요가 없습니다. 변환이 발생할 때의 프로비저닝 동작에 대한 설명은 다음과 같습니다. 

* John Smith가 1월에 비정규 작업자로 조인했다고 가정해 보겠습니다. John의 *WorkerID*(일치하는 특성)와 연결된 AD 계정이 없기 때문에 프로비저닝 서비스는 사용자에 대한 새 AD 계정을 만들고 John의 비정규 작업자 *WID(WorkdayID)* 를 자신의 AD 계정에 연결합니다.
* 3개월 후 John은 정규직 직원으로 전환합니다. Workday에서 John에 대한 새 작업자 프로필이 만들어집니다. Workday에서 John의 *WorkerID* 는 동일하게 유지되지만, John은 이제 Workday에 두 개의 *WID* 가 있습니다. 하나는 비정규 작업자 프로필과 연결되고 다른 하나는 직원 작업자 프로필과 연결됩니다. 
* 증분 동기화 중에 프로비저닝 서비스는 동일한 WorkerID에 대해 두 개의 작업자 프로필을 검색하면 AD 계정의 소유권을 활성 작업자 프로필로 자동으로 이전합니다. 이 경우 AD 계정에서 비정규직 작업자 프로필을 연결 해제하고 John의 활성 직원 작업자 프로필과 AD 계정 간에 새 연결을 설정합니다. 

>[!NOTE]
>초기 전체 동기화 중에 이전 비활성 작업자 프로필과 연결된 특성 값이 변환된 작업자의 AD 계정으로 흐르는 동작을 확인할 수 있습니다. 이는 일시적이며 전체 동기화가 진행됨에 따라 활성 작업자 프로필의 특성 값으로 덮어쓰게 됩니다. 전체 동기화가 완료되고 프로비저닝 작업이 안정적인 상태에 도달하면 증분 동기화 중에 항상 활성 작업자 프로필을 선택합니다. 


### <a name="retrieving-international-job-assignments-and-secondary-job-details"></a>국제 작업 할당 및 보조 작업 세부 정보 검색

기본적으로 Workday 커넥터는 작업자의 주 작업에 연결된 특성을 검색합니다. 하지만 커넥터를 통해 국제 작업 할당 또는 보조 작업과 관련된 *추가 작업 데이터* 도 검색할 수 있습니다. 

다음 단계를 사용하여 국가별 작업 할당과 관련된 특성을 검색합니다. 

1. Workday 연결 URL이 Workday 웹 서비스 API 30.0 이상 버전을 사용하도록 설정합니다. 이에 따라 Workday 프로비저닝 앱에서 [올바른 XPATH 값](workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)을 설정합니다. 
1. 노드`@wd:Primary_Job=0`에 있는`Worker_Job_Data` 선택기를 사용하여 올바른 특성을 검색합니다. 
   * **예 1:** XPATH를 `SecondaryBusinessTitle` 사용하려면 `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Worker_Job_Data[@wd:Primary_Job=0]/wd:Position_Data/wd:Business_Title/text()`
   * **예 2:** XPATH를 `SecondaryBusinessLocation` 사용하려면 `wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Worker_Job_Data[@wd:Primary_Job=0]/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Location_Reference/@wd:Descriptor`

 

## <a name="next-steps"></a>다음 단계

* [Workday Active Directory 프로비저닝을 구성하는 방법을 알아봅니다.](../saas-apps/workday-inbound-tutorial.md)
* [Workday에 쓰기 저장하는 방법을 알아봅니다.](../saas-apps/workday-writeback-tutorial.md)
* [인바운드 프로비저닝에 지원되는 Workday 특성에 대해 자세히 알아봅니다.](workday-attribute-reference.md)

