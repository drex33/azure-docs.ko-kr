---
title: Azure Active Directory의 감사 로그 | Microsoft Docs
description: Azure Active Directory의 감사 로그 개요입니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/30/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ccb2ddb9e229faea69ae3f67ed8285b0256808
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128581269"
---
# <a name="audit-logs-in-azure-active-directory"></a>Azure Active Directory의 감사 로그 

IT 관리자가 IT 환경의 운영 상태를 파악하려고 합니다. 시스템 상태 정보를 사용하여 잠재적 이슈에 대응해야 하는지 여부와 방법을 평가할 수 있습니다. 

이 목표를 지원하기 위해 Azure Active Directory 포털은 다음 세 가지 활동 로그에 대한 액세스를 제공합니다.

- **[로그인](concept-sign-ins.md)** – 로그인 및 사용자가 리소스를 사용하는 방법에 대한 정보입니다.
- **[감사](concept-audit-logs.md)** – 사용자 및 그룹 관리 또는 테넌트 리소스에 적용된 업데이트와 같이 테넌트에 적용된 변경 사항에 대한 정보입니다.
- **[프로비저닝](concept-provisioning-logs.md)** – ServiceNow의 그룹 생성 또는 Workday에서 가져온 사용자와 같이 프로비저닝 서비스에서 수행하는 활동입니다.

이 문서에서는 감사 보고서에 대한 개요를 제공합니다.


## <a name="what-is-it"></a>무엇인가요?

Azure AD의 감사 로그를 사용하여 규정 준수를 위한 시스템 활동 레코드에 액세스할 수 있습니다.
이 로그의 가장 일반적인 보기는 다음 범주를 기반으로 합니다.

- 사용자 관리

- 그룹 관리
 
- 애플리케이션 관리  


사용자 중심 보기를 사용하면 다음과 같은 질문에 대한 답을 얻을 수 있습니다.

- 어떤 종류의 업데이트가 사용자에게 적용되나요?

- 얼마나 많은 사용자가 변경되었나요?

- 얼마나 많은 암호가 변경되었나요?

- 관리자가 디렉터리에서 무엇을 수행했나요?


그룹 중심 보기를 사용하면 다음과 같은 질문에 대한 답을 얻을 수 있습니다.

- 추가된 그룹은 무엇인가요?

- 멤버 자격이 변경된 그룹이 있나요?

- 그룹의 소유자가 변경되었나요?

- 어떤 라이선스가 그룹 또는 사용자에 할당되었나요?

애플리케이션 중심 보기를 사용하면 다음과 같은 질문에 대한 답을 얻을 수 있습니다.

- 추가되거나 업데이트된 애플리케이션은 무엇인가요?

- 제거된 애플리케이션은 무엇인가요?

- 애플리케이션에 대한 서비스 주체가 변경되었나요?

- 애플리케이션의 이름이 변경되었나요?

- 누가 애플리케이션에 동의했나요?

 
## <a name="what-license-do-i-need"></a>어떤 라이선스가 필요한가요?

감사 활동 보고서는 Azure AD의 모든 버전에서 사용할 수 있습니다.

## <a name="who-can-access-it"></a>액세스할 수 있는 사용자는 누구인가요?

감사 로그에 액세스하려면 다음 역할 중 하나가 있어야 합니다. 

- 보안 관리자
- 보안 판독기
- 보고서 읽기 권한자
- 전역 Reader
- 전역 관리자

## <a name="where-can-i-find-it"></a>어디서 찾을 수 있나요?

Azure Portal은 로그에 액세스하는 몇 가지 옵션을 제공합니다. 예를 들어 Azure Active Directory 메뉴의 **모니터링** 섹션에서 로그를 열 수 있습니다.  

![감사 로그 열기](./media/concept-audit-logs/audit-logs-menu.png)

또한 [이 링크](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/ProvisioningEvents)를 사용하여 감사 로그로 직접 이동할 수 있습니다.


Microsoft Graph API를 통해서 감사 로그에 액세스할 수도 있습니다.


## <a name="what-is-the-default-view"></a>기본 보기는 무엇인가요?

감사 로그에는 다음 항목을 보여 주는 기본 목록 보기가 있습니다.

- 발생 날짜와 시간
- 발생을 기록한 서비스
- 활동의 범주 및 이름(*항목*) 
- 활동의 상태(성공 또는 실패)
- 대상
- 작업의 초기자/행위자(누가)

![감사 로그](./media/concept-audit-logs/listview.png "감사 로그")

도구 모음에서 **열** 을 클릭하여 목록 보기를 사용자 지정할 수 있습니다.

![감사 열](./media/concept-audit-logs/columns.png "감사 열")

그러면 추가 필드를 표시하거나 이미 표시된 필드를 제거할 수 있습니다.

![필드 제거](./media/concept-audit-logs/columnselect.png "필드 제거")

자세한 정보를 가져오려면 목록 보기에서 항목을 선택합니다.

![항목 선택](./media/concept-audit-logs/details.png "항목 선택")

## <a name="filtering-audit-logs"></a>감사 로그 필터링

다음 필드에서 감사 데이터를 필터링할 수 있습니다.

- 서비스
- 범주
- 활동
- 상태
- 대상
- 초기자(작업자)
- 날짜 범위

![개체 필터링](./media/concept-audit-logs/filter.png "Filter 개체")

**서비스** 필터를 사용하면 다음 서비스의 드롭다운 목록에서 선택할 수 있습니다.

- 모두
- AAD 관리 UX
- 액세스 검토
- 계정 프로비전
- 애플리케이션 프록시
- 인증 방법
- B2C
- 조건부 액세스
- 핵심 디렉터리
- 권한 관리
- 하이브리드 인증
- ID 보호
- 사용자 초대
- MIM 서비스
- MyApps
- PIM
- 셀프 서비스 그룹 관리
- 셀프 서비스 암호 관리
- 사용 조건

**범주** 필터를 사용하면 다음 필터 중 하나를 선택할 수 있습니다.

- 모두
- AdministrativeUnit
- ApplicationManagement
- 인증
- 권한 부여
- 연락처
- 디바이스
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- KeyManagement
- 레이블
- 기타
- PermissionGrantPolicy
- 정책
- ResourceManagement
- RoleManagement
- UserManagement

**활동** 필터는 사용자가 선택한 범주와 활동 리소스 종류를 기반으로 합니다. 보려는 특정 활동을 선택하거나 모두 선택할 수 있습니다. 

Graph API를 사용하여 모든 감사 활동 목록을 가져올 수 있습니다. `https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

**상태** 필터를 사용하여 감사 작업 상태를 기준으로 필터링할 수 있습니다. 상태는 다음 중 하나로 표시될 수 있습니다.

- 모두
- Success
- 실패

**대상** 필터를 사용하면 이름 또는 UPN(사용자 계정 이름)을 기준으로 특정 대상을 검색할 수 있습니다. 대상 이름 및 UPN은 대/소문자를 구분합니다. 

**시작한 사람** 필터를 사용하면 작업자 이름이나 UPN(사용자 계정 이름)을 정의할 수 있습니다. 이름 및 UPN은 대/소문자를 구분합니다.

**날짜** 범위 필터를 사용하면 반환되는 데이터의 시간 범위를 정의할 수 있습니다.  
가능한 값은 다음과 같습니다.

- 7 일
- 24시간
- 사용자 지정

사용자 지정 시간 범위를 선택하면 시작 시간과 종료 시간을 구성할 수 있습니다.

**다운로드** 단추를 선택하여 필터링된 데이터를 최대 25만 개의 레코드로 다운로드하도록 선택할 수도 있습니다. 로그는 CSV 또는 JSON 형식으로 다운로드할 수 있습니다. 다운로드할 수 있는 레코드의 수는 [Azure Active Directory 보고서 보존 정책](reference-reports-data-retention.md)에 의해 제한됩니다.

![데이터 다운로드](./media/concept-audit-logs/download.png "데이터 다운로드")



## <a name="microsoft-365-activity-logs"></a>Microsoft 365 활동 로그

[Microsoft 365 관리 센터](/office365/admin/admin-overview/about-the-admin-center)에서 Microsoft 365 활동 로그를 볼 수 있습니다. Microsoft 365 활동 로그와 Azure AD 활동 로그는 여러 디렉터리 리소스를 공유하지만 Microsoft 365 관리 센터에서만 Microsoft 365 활동 로그를 전체적으로 확인할 수 있습니다. 

[Office 365 관리 API](/office/office-365-management-api/office-365-management-apis-overview)를 사용하여 프로그래밍 방식으로 Microsoft 365 활동 로그에 액세스할 수도 있습니다.

> [!NOTE]
> 대부분의 독립 실행형 또는 번들 Microsoft 365 구독에는 Microsoft 365 데이터 센터 경계 내의 일부 하위 시스템에 대한 백 엔드 종속성이 있습니다. 종속성에는 디렉터리를 동기화된 상태로 유지하고 기본적으로 Exchange Online에 대한 구독 옵트인에서 무료로 온보딩을 가능하게 하기 위해 일부 정보 쓰기 저장이 필요합니다. 이러한 쓰기 저장의 경우 감사 로그 항목은 "Microsoft Substrate Management"에서 수행하는 작업을 표시합니다. 이러한 감사 로그 항목은 Exchange Online에서 Azure AD로 실행한 만들기/업데이트/삭제 작업을 나타냅니다. 항목은 정보 제공용이므로 어떤 조치도 필요하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 감사 활동 참조](reference-audit-activities.md)
- [Azure AD 로그 보존 참조](reference-reports-data-retention.md)
- [Azure AD 로그 대기 시간 참조](reference-reports-latencies.md)
- [감사 보고서에서 알 수 없는 작업자](/troubleshoot/azure/active-directory/unknown-actors-in-audit-reports)
