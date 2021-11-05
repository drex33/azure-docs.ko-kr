---
title: Azure Active Directory 애플리케이션 프로비저닝의 격리 상태
description: 자동 사용자 프로비저닝을 위해 애플리케이션을 구성한 경우 격리의 프로비저닝 상태의 의미와 이러한 상태를 지우는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 93b00212bda4f02b6a31c151856639b1f461cac1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050834"
---
# <a name="application-provisioning-in-quarantine-status"></a>격리 상태의 애플리케이션 프로비저닝

Azure AD 프로비저닝 서비스는 구성의 상태를 모니터링합니다. 또한 비정상 앱은 "격리" 상태가 됩니다. 대상 시스템에 대해 수행된 호출의 대부분 또는 전체가 일관되게 실패하는 경우 프로비저닝 작업은 격리된 상태로 표시됩니다. 잘못된 관리자 자격 증명 때문에 오류가 발생한 경우를 예로 들 수 있습니다.

격리 중에 다음이 진행됩니다.
- 증분 주기 빈도는 하루에 한 번까지 점차 감소됩니다.
- 오류를 모두 수정하고 다음 동기화 주기가 시작되면 프로비저닝 작업이 격리에서 제거됩니다. 
- 프로비저닝 작업이 4주 넘게 격리 상태로 유지되면 프로비저닝 작업을 사용할 수 없게 됩니다(실행 중지).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>애플리케이션이 격리 상태인지 확인하려면 어떻게 하나요?

애플리케이션이 격리되어 있는지 여부를 확인하는 방법에는 다음 세 가지가 있습니다.

- Azure Portal에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션** > &lt;*애플리케이션 이름*&gt; > **프로비저닝** 으로 이동하고, 격리 메시지의 진행률 표시줄을 검토합니다.   

  ![격리 상태를 보여 주는 프로비저닝 상태 표시줄](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Azure Portal에서 **Azure Active Directory** > **감사 로그** 로 이동한 후 **작업: 격리** 를 필터링하고 격리 기록을 검토합니다. 위에서 설명한 대로 진행률 표시줄의 보기는 프로비저닝이 현재 격리 상태인지 여부를 보여 줍니다. 감사 로그에는 애플리케이션에 대한 격리 기록이 표시됩니다. 

- Microsoft Graph 요청 [Get synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta&preserve-view=true)을 사용하여 프로비저닝 작업의 상태를 프로그래밍 방식으로 가져옵니다.

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- 메일을 확인합니다. 애플리케이션이 격리 상태가 되면 일회성 알림 메일이 전송됩니다. 격리 이유가 변경되면 업데이트된 메일을 전송하여 새로운 격리 이유를 제공합니다. 메일이 표시되지 않는 경우 다음을 수행합니다.

  - 애플리케이션의 프로비저닝 구성에서 유효한 **알림 메일** 을 지정했는지 확인합니다.
  - 알림 메일 받은 편지함에 스팸 필터링이 없는지 확인합니다.
  - 메일을 구독 취소하지 않았는지 확인합니다.
  - `azure-noreply@microsoft.com`에서 메일 확인

## <a name="why-is-my-application-in-quarantine"></a>애플리케이션이 격리된 이유는 무엇인가요?

|Description|권장 작업|
|---|---|
|**SCIM 규정 준수 문제:** HTTP/404 찾을 수 없음 응답이 예상된 HTTP/200 정상 응답 대신 반환되었습니다. 이 경우 Azure AD 프로비저닝 서비스에서 대상 애플리케이션에 대한 요청을 수행하고 예기치 않은 응답을 받았습니다.|관리자 자격 증명 섹션을 확인합니다. 애플리케이션에서 테넌트 URL을 지정해야 하는지와 URL이 올바른지 확인합니다. 문제가 표시되지 않으면 애플리케이션 개발자에게 문의하여 서비스가 SCIM 규격인지 확인합니다. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**잘못된 자격 증명:** 대상 애플리케이션에 대한 액세스에 권한을 부여하려고 할 때 제공된 자격 증명이 유효하지 않음을 나타내는 대상 애플리케이션의 응답을 받았습니다.|프로비저닝 구성 UI의 관리자 자격 증명 섹션으로 이동하고 유효한 자격 증명을 사용하여 액세스 권한을 다시 부여합니다. 애플리케이션이 갤러리에 있는 경우 필요한 추가 단계에 대한 애플리케이션 구성 자습서를 검토합니다.|
|**중복 역할:** Salesforce 및 Zendesk와 같은 특정 애플리케이션에서 가져온 역할은 고유해야 합니다. |Azure Portal에서 애플리케이션 [매니페스트](../develop/reference-app-manifest.md)로 이동한 후 중복 역할을 제거합니다.|

 프로비저닝 작업 상태를 가져오는 Microsoft Graph 요청은 다음과 같은 격리 이유를 보여 줍니다.
- `EncounteredQuarantineException`은 잘못된 자격 증명이 제공되었음을 나타냅니다. 프로비저닝 서비스가 원본 시스템과 대상 시스템 간에 연결을 설정할 수 없습니다.
- `EncounteredEscrowProportionThreshold`는 프로비저닝이 에스크로 임계값을 초과했음을 나타냅니다. 이 상태는 프로비저닝 이벤트의 40% 이상이 실패한 경우에 발생합니다. 자세한 내용은 아래의 에스크로 임계값 정보를 참조하세요.
- `QuarantineOnDemand`는 애플리케이션에서 문제를 검색했으며 수동으로 격리로 설정했음을 의미합니다.

**에스크로 임계값**

비례 에스크로 임계값을 충족하는 경우 프로비저닝 작업은 격리로 전환됩니다. 이 논리는 변경될 수 있지만 대략적으로 아래에 설명된 대로 작동합니다. 

작업은 관리자 자격 증명 또는 SCIM 규정 준수와 같은 문제에 대한 오류 수에 관계 없이 격리로 전환될 수 있습니다. 그러나 일반적으로 5천 번의 오류는 오류 횟수가 너무 많기 때문에 격리할지 여부를 평가하기 시작하기 위한 최소 횟수입니다. 예를 들어, 4천 번의 오류가 발생한 작업은 격리로 전환되지 않습니다. 그러나 5천 번의 오류가 있는 작업은 평가를 트리거합니다. 평가는 다음 조건을 사용합니다.  
- 프로비저닝 이벤트의 40% 이상이 실패하거나 4만 번 이상 오류가 발생한 경우 프로비저닝 작업은 격리로 전환됩니다. 참조 오류는 40% 또는 4만 번의 임계값 일부로 계산되지 않습니다. 예를 들어, 관리자 또는 그룹 멤버를 업데이트하지 못하는 것은 참조 오류입니다.
- 4만5천 명의 사용자가 성공적으로 프로비저닝되지 않은 작업은 4만 번의 임계값을 초과하는 격리로 이어질 수 있습니다.
- 3만 명의 사용자는 프로비저닝하지 못하고 5천 명은 성공한 작업은 40% 임계값 및 5천 번의 최소값을 초과하므로 격리될 수 있습니다.
- 2만 번 오류가 발생하고 10만 번 성공한 작업은 40% 오류 임계값 또는 4만 번의 실패 최대값을 초과하지 않으므로 격리로 이동되지 않습니다.  
- 참조 오류 및 비참조 오류를 모두 포함하는 6만 번 오류에 해당하는 절대 임계값이 있습니다. 예를 들어, 4만 명의 사용자는 프로비저닝을 실패했고 2만천 명의 관리자는 업데이트를 실패했습니다. 모두 더하면 6만천 번의 오류이며 6만 번의 제한을 초과합니다.


## <a name="how-do-i-get-my-application-out-of-quarantine"></a>애플리케이션을 격리에서 해제하려면 어떻게 해야 하나요?

먼저 애플리케이션을 격리되도록 한 문제를 해결합니다.

- 애플리케이션의 프로비저닝 설정을 확인하여 [유효한 관리자 자격 증명을 입력](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)했는지 확인합니다. Azure AD는 대상 애플리케이션과의 트러스트를 설정해야 합니다. 유효한 자격 증명을 입력하고 계정에 필요한 사용 권한이 있는지 확인합니다.

- [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md)를 검토하여 격리를 발생시키는 오류를 자세히 조사하고 오류를 해결합니다. **작업** 섹션에서 **Azure Active Directory** &gt; **엔터프라이즈 앱** &gt; **프로비저닝 로그(미리 보기)** 로 이동합니다.

문제를 해결한 후 프로비저닝 작업을 다시 시작합니다. 특성 매핑 또는 범위 지정 필터와 같은 애플리케이션 프로비저닝 설정이 변경되면 자동으로 프로비저닝이 다시 시작됩니다. 애플리케이션의 **프로비저닝** 페이지에 있는 진행률 표시줄에는 프로비저닝이 마지막으로 시작된 시간이 표시됩니다. 프로비저닝 작업을 수동으로 다시 시작해야 하는 경우 다음 방법 중 하나를 사용합니다.  

- Azure Portal를 사용하여 프로비저닝 작업을 다시 시작합니다. 애플리케이션의 **프로비저닝** 페이지에서 **프로비저닝 다시 시작** 을 선택합니다. 이 작업은 프로비저닝 서비스를 완전히 다시 시작하며, 이 작업은 다소 시간이 걸릴 수 있습니다. 에스크로를 지우고, 격리에서 앱을 제거하고, 모든 워터마크를 지우는 전체 초기 주기가 다시 실행됩니다. 그러면 서비스는 원본 시스템의 모든 사용자를 다시 평가하고 프로비저닝 범위에 있는지 확인합니다. 이 문서에 나와 있는 것처럼 이 서비스는 애플리케이션이 현재 격리 중이거나 특성 매핑을 변경해야 하는 경우에 유용할 수 있습니다. 계산해야 하는 개체의 수 때문에 초기 주기는 일반적인 증분 주기보다 완료하는 데 더 오래 걸립니다. [여기](application-provisioning-when-will-provisioning-finish-specific-user.md)에서 초기 및 증분 주기의 성능에 대해 자세히 알아볼 수 있습니다.

- Microsoft Graph를 사용하여 [프로비저닝 작업을 다시 시작](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true)합니다. 다시 시작하는 작업을 완전히 제어할 수 있습니다. 에스크로를 지우거나(격리 상태를 적용하는 에스크로 카운터 다시 시작), 격리를 지우거나(격리에서 애플리케이션 제거), 워터마크를 지우도록 선택할 수 있습니다. 다음 요청을 사용합니다.

```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

"{ID}"를 애플리케이션 ID의 값으로 바꾸고 "{jobId}"를 [동기화 작업의 ID](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta&preserve-view=true#list-synchronization-jobs-in-the-context-of-the-service-principal)로 바꿉니다.
