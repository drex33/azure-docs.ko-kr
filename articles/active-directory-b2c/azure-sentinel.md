---
title: Azure Sentinel을 사용하여 Azure AD B2C 보호
titleSuffix: Azure AD B2C
description: Azure Sentinel을 사용하여 Azure Active Directory B2C 데이터에 대한 보안 분석을 수행하는 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: be45eaa692b0b8235541c798cf82ca26b14b9f3f
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691572"
---
# <a name="tutorial-configure-security-analytics-for-azure-active-directory-b2c-data-with-azure-sentinel"></a>자습서: Azure Sentinel을 사용하여 Azure Active Directory B2C 데이터에 대한 보안 분석 구성

로그 및 감사 정보를 Azure Sentinel에 라우팅하여 AD(Azure Active Directory) B2C 환경을 더욱 안전하게 보호할 수 있습니다. Azure Sentinel은 클라우드 네이티브 SIEM(보안 정보 이벤트 관리) 및 SOAR(보안 오케스트레이션 자동화 응답) 솔루션입니다. Azure Sentinel은 Azure AD B2C에 대한 경고 검색, 위협 가시성, 주도적 헌팅, 위협 대응 기능을 제공합니다.

Azure AD B2C에서 Azure Sentinel을 사용하여 다음을 수행할 수 있습니다.

- Microsoft 분석 및 업계 최고의 위협 인텔리전스를 사용하여 이전에 미검사된 위협을 탐지하고 가양성을 최소화합니다.
- 인공 지능을 사용하여 위협을 조사합니다. Microsoft의 수년간의 사이버 보안 관련 작업을 활용하여 대규모의 의심스러운 활동을 헌팅합니다.
- 일반 작업의 기본 제공 오케스트레이션 및 자동화로 빠르게 인시던트에 대응합니다.
- 조직의 보안 및 규정 준수 요구 사항을 충족합니다.

이 자습서에서는 다음 작업을 수행하는 방법을 알아봅니다.

1. [Azure Monitor 로그 작업 영역에 Azure AD B2C 로그 전송](#configure-azure-ad-b2c-with-azure-monitor-logs-analytics)
2. [Log Analytics 작업 영역에서 Azure Sentinel을 사용하도록 설정](#deploy-an-azure-sentinel-instance)
3. [Azure Sentinel에서 인시던트를 트리거할 샘플 규칙 만들기](#create-an-azure-sentinel-rule)
4. [자동화된 응답 구성](#automated-response)

## <a name="configure-azure-ad-b2c-with-azure-monitor-logs-analytics"></a>Azure Monitor 로그 분석을 사용하여 Azure AD B2C 구성

Azure AD B2C 테넌트 내에서 Azure AD의 **진단 설정** 을 사용하도록 설정하여 리소스에 대한 로그 및 메트릭이 전송되어야 하는 위치를 정의합니다.

그런 다음 [Azure Monitor에 로그를 보내도록 Azure AD B2C를 구성](https://docs.microsoft.com/azure/active-directory-b2c/azure-monitor)합니다.

## <a name="deploy-an-azure-sentinel-instance"></a>Azure Sentinel 인스턴스 배포

>[!IMPORTANT]
>Azure Sentinel을 사용하도록 설정하려면 Azure Sentinel 작업 영역이 있는 구독에 대한 **기여자 권한** 이 필요합니다. Azure Sentinel을 사용하려면 작업 영역이 속한 리소스 그룹에 대해 참가자 또는 읽기 권한자 권한이 있어야 합니다.

Azure Monitor에 로그를 보내도록 Azure AD B2C 인스턴스를 구성한 후에는 Azure Sentinel 인스턴스를 사용하도록 설정해야 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다. Log analytics 작업 영역이 생성되는 구독을 선택합니다.

2. **Azure Sentinel** 을 검색하여 선택합니다.

3. **추가** 를 선택합니다.

![Azure Portal에서 Azure Sentinel 검색을 보여주는 이미지](./media/azure-sentinel/azure-sentinel-add.png)

4. 새 작업 영역을 선택합니다.

![Sentinel 작업 영역을 선택하는 이미지](./media/azure-sentinel/create-new-workspace.png)

5. **Azure Sentinel 추가** 를 선택합니다.

>[!NOTE]
>둘 이상의 작업 영역에서 [Azure Sentinel을 실행](../sentinel/quickstart-onboard.md)할 수 있지만 데이터는 단일 작업 영역으로 격리됩니다.

## <a name="create-an-azure-sentinel-rule"></a>Azure Sentinel 규칙 만들기

>[!NOTE]
>Azure Sentinel에서는 Microsoft 보안 전문가 및 분석가 팀이 설계한, 위협 탐지 규칙을 만드는 데 도움이 되는 기본 제공 템플릿을 제공합니다. 템플릿에서 생성된 규칙은 데이터에서 의심스러운 활동을 자동으로 검색합니다. 지금은 사용할 수 있는 기본 Azure AD B2C 커넥터가 없습니다. 이 자습서의 예제에서는 자체 규칙을 만듭니다.

이제 Azure Sentinel을 사용하도록 설정했으므로 Azure AD B2C 테넌트에서 의심스러운 상황이 발생하면 알림을 받습니다.

사용자 환경에 존재하는 위협과 비정상적인 동작을 발견하는 데 도움이 되는 [사용자 지정 분석 규칙](../sentinel/tutorial-detect-threats-custom.md)을 만들 수 있습니다. 이 규칙은 특정 이벤트나 이벤트 집합을 검색하고, 특정 이벤트 임계값이나 조건에 도달할 경우 경고합니다. 이후에는 추가 조사를 위해 인시던트를 생성합니다.

다음 예제에서는 다른 사용자가 환경에 대한 액세스를 강제 시도하지만 성공하지 못하는 경우 알림을 받는 시나리오를 설명합니다. 무차별 암호 대입 공격(brute force attack)을 의미할 수 있습니다. 60초 이내에 두 개 이상의 성공적이지 않은 로그인에 대한 알림을 받고 싶습니다.

1. Azure Sentinel 탐색 메뉴에서 **Analytics** 를 선택합니다.

2. 상단 작업 모음에서 **+ 만들기** 를 선택한 다음 **예약된 쿼리 규칙** 을 선택합니다. **분석 규칙 마법사** 가 열립니다.

![예약된 쿼리 규칙 만들기 선택을 보여주는 이미지](./media/azure-sentinel/create-scheduled-rule.png)

3. 분석 규칙 마법사에서 **일반** 탭으로 이동합니다.

   | 필드 | 값 |
   |:--|:--|
   |Name | B2C 성공적이지 않은 로그인 |
   |Description | 60초 이내에 성공적이지 않은 두 개 이상의 로그인에 대해 알림 |
   | 전술 | 규칙을 분류할 공격 범주에서 선택합니다. 이러한 범주는 [MITRE ATT&CK](https://attack.mitre.org/) 프레임워크의 전술을 기반으로 합니다.<BR>이 예제에서는 `PreAttack`을 선택합니다. <BR> MITRE ATT&CK®는 실제 관찰을 기반으로 한 악의적 사용자 전술과 기법이 포함된 기술 자료로, 전 세계에서 액세스할 수 있습니다. ATT&CK 기술 자료는 특정 위협 모델과 방법론 개발의 기초로 사용됩니다.
   | 심각도 | 필요에 따라 |
   | 상태 | 규칙을 만들 때 상태는 기본적으로 `Enabled`로 설정되어 있습니다. 즉, 규칙 만들기를 완료한 후 즉시 실행됩니다. 즉시 실행하지 않으려면 `Disabled`를 선택합니다. 규칙은 활성 규칙 탭에 추가되고 여기에서 필요할 때 사용하도록 설정할 수 있습니다.|

![기본 규칙 속성을 제공하는 이미지](./media/azure-sentinel/create-new-rule.png)

4.  규칙 쿼리 논리를 정의하고 설정을 구성하려면 **규칙 논리 설정** 탭의 **규칙 쿼리** 필드에서 직접 쿼리를 작성합니다. 이 쿼리는 B2C 테넌트에서 60초 이내에 실패한 로그인이 두 번 이상 있을 때 경고하고 `UserPrincipalName`으로 구성됩니다.

![논리 탭에서 규칙 쿼리 입력을 보여주는 이미지](./media/azure-sentinel/rule-query.png)

쿼리 일정 섹션에서 다음 매개 변수를 설정합니다.

![쿼리 일정 매개 변수를 설정하는 이미지](./media/azure-sentinel/query-scheduling.png)

5. **다음: 인시던트 설정(미리 보기)** 을 선택합니다. 자동화 응답을 구성하고 나중에 추가합니다.

6. **검토 및 만들기** 탭으로 이동하여 새 경고 규칙에 대한 모든 설정을 검토합니다. **유효성 검사 통과** 메시지가 나타나면 **만들기** 를 선택하여 경고 규칙을 초기화합니다.

![규칙 검토 및 만들기 이미지](./media/azure-sentinel/review-create.png)

7. 규칙과 규칙이 생성하는 인시던트를 확인합니다. 기본 **분석** 화면의 **활성 규칙** 탭 아래에 있는 테이블에서 새로 만든 사용자 지정 규칙(**예약됨** 유형)을 찾습니다. 이 목록에서 규칙을 **편집**, **사용**, **사용하지 않음** 또는 **삭제** 할 수 있습니다.

![규칙을 편집, 사용, 사용하지 않음 또는 삭제하는 옵션을 보여주는 분석 화면 이미지](./media/azure-sentinel/rule-crud.png)

8. 새로운 Azure AD B2C 성공적이지 않은 로그인 규칙의 결과를 확인합니다. **인시던트** 페이지로 이동합니다. 여기서 위협을 심사, 조사, 교정할 수 있습니다. 인시던트는 여러 경고를 포함할 수 있습니다. 이는 특정 조사에 대한 모든 관련 증거의 집계입니다. 인시던트 수준에서 심각도, 상태 등의 속성을 설정할 수 있습니다.

>[!Note]
>Azure Sentinel의 핵심 기능은 [인시던트 조사](../sentinel/tutorial-investigate-cases.md)입니다.

9. 조사를 시작하려면 특정 인시던트를 선택합니다. 오른쪽에서 심각도, 관련 엔터티, 인시던트를 트리거한 원시 이벤트, 인시던트의 고유 ID를 포함하여 인시던트에 대한 자세한 정보를 볼 수 있습니다.

![alt-text="incident screen 이미지](./media/azure-sentinel/select-incident.png)

10. 인시던트 페이지에서 **전체 세부 정보 보기** 를 선택하고, 인시던트 정보가 요약되고 자세한 내용을 제공하는 관련 탭을 검토합니다.

![규칙 73 이미지](./media/azure-sentinel/full-details.png)

11. 그런 다음, **증거** > **이벤트** > **Log Analytics에 연결** 을 선택합니다. 결과에는 시도 횟수와 함께 로그인을 시도한 ID의 `UserPrincipalName`이 표시됩니다.

![선택한 인시던트의 세부 정보 이미지](./media/azure-sentinel/logs.png)

## <a name="automated-response"></a>자동화된 대응

Azure Sentinel은 [강력한 SOAR 기능](../sentinel/automation-in-azure-sentinel.md)을 제공합니다. Azure Sentinel에서 플레이북이라는 자동화된 작업은 요구 사항에 맞게 분석 규칙에 연결할 수 있습니다.

이 예제에서는 규칙으로 생성된 인시던트에 대한 전자 메일 알림을 추가합니다. 이 작업을 수행하려면 [Sentinel GitHub 리포지토리에서 기존 플레이북](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification)을 사용합니다. 플레이북이 구성되면 기존 규칙을 편집하고 자동화 탭에서 플레이북를 선택합니다.

![규칙에 연결된 자동화된 응답에 대한 구성 화면 이미지](./media/azure-sentinel/automation-tab.png)

## <a name="next-steps"></a>다음 단계

- [Azure Sentinel의 가양성 처리](../sentinel/false-positives.md)

- [샘플 통합 문서](https://github.com/azure-ad-b2c/siem#workbooks)

- [Azure Sentinel 설명서](../sentinel/index.yml)
