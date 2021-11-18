---
title: Microsoft Sentinel을 사용한 Azure AD B2C 보안
titleSuffix: Azure AD B2C
description: 이 자습서에서는 Microsoft Sentinel을 사용하여 Azure Active Directory B2C 데이터에 대한 보안 분석을 수행합니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5afd5535dc9bd9a6f0dce507e6279b664c90fe8e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281422"
---
# <a name="tutorial-configure-security-analytics-for-azure-active-directory-b2c-data-with-microsoft-sentinel"></a>자습서: Microsoft Sentinel을 사용하여 Azure Active Directory B2C 데이터에 대한 보안 분석 구성

로그 및 감사 정보를 Microsoft Sentinel로 라우팅하여 Azure AD B2C(Azure Active Directory B2C) 환경을 더 안전하게 보호할 수 있습니다. Microsoft Sentinel은 클라우드 네이티브 SIEM(보안 정보 및 이벤트 관리) 및 SOAR(보안 오케스트레이션, 자동화 및 응답) 솔루션입니다. Microsoft Sentinel은 Azure AD B2C에 대한 경고 검색, 위협 가시성, 주도적 헌팅, 위협 대응 기능을 제공합니다.

Azure AD B2C에서 Microsoft Sentinel을 사용하여 다음을 수행할 수 있습니다.

- Microsoft의 분석 및 위협 인텔리전스를 사용하여 이전에 탐지되지 않은 위협을 탐지하고 가양성을 최소화합니다.
- AI를 사용하여 위협을 조사합니다. 의심스러운 활동을 규모에 맞게 헌팅하고, 수년간에 걸친 Microsoft의 사이버 보안 관련 작업을 활용합니다.
- 일반 작업의 기본 제공 오케스트레이션 및 자동화로 빠르게 인시던트에 대응합니다.
- 조직의 보안 및 규정 준수 요구 사항을 충족합니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Azure AD B2C 로그를 Log Analytics 작업 영역에 보냅니다.
> * Log Analytics 작업 영역에서 Microsoft Sentinel을 사용하도록 설정합니다.
> * Microsoft Sentinel에서 인시던트를 트리거할 샘플 규칙을 만듭니다.
> * 자동화된 응답을 구성합니다.

## <a name="configure-azure-ad-b2c-with-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics를 사용하여 Azure AD B2C 구성

리소스에 대한 로그 및 메트릭을 보내야 하는 위치를 정의하려면 Azure AD B2C 테넌트 내의 Azure AD에서 **진단 설정** 을 사용하도록 설정합니다. 그런 다음, [로그를 Azure Monitor에 보내도록 Azure AD B2C를 구성](./azure-monitor.md)합니다.

## <a name="deploy-a-microsoft-sentinel-instance"></a>Microsoft Sentinel 인스턴스 배포

로그를 Azure Monitor에 보내도록 Azure AD B2C 인스턴스가 구성되면 Microsoft Sentinel 인스턴스를 사용하도록 설정해야 합니다.

>[!IMPORTANT]
>Microsoft Sentinel을 사용하도록 설정하려면 Microsoft Sentinel 작업 영역이 있는 구독에 대한 기여자 권한이 필요합니다. Microsoft Sentinel을 사용하려면 작업 영역이 속한 리소스 그룹에 대해 기여자 또는 읽기 권한자 권한이 있어야 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다. Log analytics 작업 영역이 만들어지는 구독을 선택합니다.

2. **Microsoft Sentinel** 을 검색하여 선택합니다.

   ![Azure Portal에서 Microsoft Sentinel 검색을 보여 주는 스크린샷.](./media/azure-sentinel/azure-sentinel-add.png)

3. **추가** 를 선택합니다.

4. 새 작업 영역을 선택합니다.

   ![Microsoft Sentinel 작업 영역을 선택하는 위치를 보여주는 스크린샷.](./media/azure-sentinel/create-new-workspace.png)

5. **Microsoft Sentinel 추가** 를 선택합니다.

>[!NOTE]
>둘 이상의 작업 영역에서 [Microsoft Sentinel을 실행](../sentinel/quickstart-onboard.md)할 수 있지만 데이터는 단일 작업 영역으로 격리됩니다.

## <a name="create-a-microsoft-sentinel-rule"></a>Microsoft Sentinel 규칙 만들기

이제 Microsoft Sentinel을 사용하도록 설정했으므로 Azure AD B2C 테넌트에서 의심스러운 상황이 발생하면 알림을 받습니다.

환경에서 위협과 비정상적인 동작을 검색하는 [사용자 지정 분석 규칙](../sentinel/detect-threats-custom.md)을 만들 수 있습니다. 이러한 규칙은 특정 이벤트 또는 이벤트 세트를 검색하고, 특정 이벤트 임계값 또는 조건에 도달하면 경고합니다. 그런 다음, 추가 조사를 위한 인시던트를 생성합니다.

>[!NOTE]
>Microsoft Sentinel은 Microsoft의 보안 전문가 및 분석가 팀에서 설계한 위협 탐지 규칙을 만드는 데 도움이 되는 기본 제공 템플릿을 제공합니다. 템플릿에서 생성된 규칙은 데이터에서 의심스러운 활동을 자동으로 검색합니다. 지금은 사용할 수 있는 기본 Azure AD B2C 커넥터가 없습니다. 이 자습서의 예제에서는 자체 규칙을 만듭니다.

다음 예에서는 누군가가 사용자 환경에 강제로 액세스하려고 했지만 성공하지 못하는 경우 알림을 받습니다. 무차별 암호 대입 공격을 의미할 수 있습니다. 60초 이내에 두 번 이상 실패한 로그인에 대한 알림을 받으려고 합니다.

1. Microsoft Sentinel의 왼쪽 메뉴에서 **분석** 을 선택합니다.

2. 위쪽의 작업 모음에서 **+ 만들기** > **예약된 쿼리 규칙** 을 차례로 선택합니다. 

    ![예약된 쿼리 규칙을 만들기 위한 선택 항목을 보여 주는 스크린샷](./media/azure-sentinel/create-scheduled-rule.png)

3. 분석 규칙 마법사에서 **일반** 탭으로 이동하여 다음 정보를 입력합니다.

    | 필드 | 값 |
    |:--|:--|
    |**이름** | 실패한 Azure AD B2C 로그인에 적합한 이름을 입력합니다. |
    |**설명** | 규칙이 60초 이내에 두 번 이상 실패한 로그인에 대해 알린다는 설명을 입력합니다. |
    | **전술** | 규칙을 분류할 공격 범주에서 선택합니다. 이러한 범주는 [MITRE ATT&CK](https://attack.mitre.org/) 프레임워크의 전술을 기반으로 합니다.<BR>이 예에서는 **PreAttack** 을 선택합니다. <BR> MITRE ATT&CK는 실제 관찰을 기반으로 하는 악의적 사용자 전술과 기법에 대해 전 세계에서 액세스할 수 있는 기술 자료입니다. 이 기술 자료는 특정 위협 모델 및 방법론을 개발하기 위한 기반으로 사용됩니다.
    | **심각도** | 적합한 심각도 수준을 선택합니다. |
    | **상태** | 규칙을 만들 때 상태는 기본적으로 **사용** 입니다. 이 상태는 규칙 만들기를 완료한 직후에 해당 규칙이 실행됨을 의미합니다. 즉시 실행하지 않으려면 **사용 안 함** 을 선택합니다. 그러면 규칙이 **활성 규칙** 탭에 추가되고, 필요할 때 여기서 사용하도록 설정할 수 있습니다.|

    ![기본 규칙 속성을 보여 주는 스크린샷](./media/azure-sentinel/create-new-rule.png)

4. 규칙 쿼리 논리를 정의하고 설정을 구성하려면 **규칙 논리 설정** 탭의 **규칙 쿼리** 상자에서 쿼리를 직접 작성합니다. 

    ![규칙 논리를 설정하기 위한 탭에서 규칙 쿼리를 입력하는 것을 보여 주는 스크린샷](./media/azure-sentinel/rule-query.png)

    이 쿼리는 Azure AD B2C 테넌트에 대해 60초 이내에 실패한 로그인이 두 번 이상 있을 때 경고합니다. 이는 `UserPrincipalName`으로 구성됩니다.

5. **쿼리 일정** 섹션에서 다음 매개 변수를 설정합니다.

    ![쿼리 예약 매개 변수 설정을 보여 주는 스크린샷](./media/azure-sentinel/query-scheduling.png)

6. **다음: 인시던트 설정(미리 보기)** 을 선택합니다. 나중에 자동화된 응답을 구성하고 추가합니다.

7. **검토 및 만들기** 탭으로 이동하여 새 경고 규칙에 대한 모든 설정을 검토합니다. **유효성 검사 통과** 메시지가 나타나면 **만들기** 를 선택하여 경고 규칙을 초기화합니다.

    ![규칙을 검토하고 만들기 위한 탭을 보여 주는 스크린샷.](./media/azure-sentinel/review-create.png)

8. 규칙 및 해당 규칙이 생성하는 인시던트를 확인합니다. 기본 **분석** 화면의 **활성 규칙** 탭 아래에 있는 테이블에서 새로 만든 사용자 지정 규칙(**예약됨** 유형)을 찾습니다. 이 목록에서 해당 단추를 사용하여 규칙에 대한 편집, 사용 설정, 사용 안 함 설정 또는 삭제를 수행할 수 있습니다.

    ![편집, 사용, 사용 안 함 또는 삭제 옵션이 있는 활성 규칙을 보여 주는 스크린샷](./media/azure-sentinel/rule-crud.png)

9. 실패한 Azure AD B2C 로그인에 대한 새 규칙의 결과를 확인합니다. **인시던트** 페이지로 이동합니다. 여기서 위협을 심사, 조사, 교정할 수 있습니다. 

    인시던트는 여러 경고를 포함할 수 있습니다. 이는 특정 조사에 대한 모든 관련 증거의 집계입니다. 인시던트 수준에서 심각도, 상태 등의 속성을 설정할 수 있습니다.

    > [!NOTE]
    > Microsoft Sentinel의 핵심 기능은 [인시던트 조사](../sentinel/investigate-cases.md)입니다.
    
10. 조사를 시작하려면 특정 인시던트를 선택합니다. 

    오른쪽에서 인시던트에 대한 자세한 정보를 볼 수 있습니다. 이 정보에는 심각도, 관련 엔터티, 인시던트를 트리거한 원시 이벤트 및 인시던트의 고유 ID가 포함되어 있습니다.

    ![인시던트 정보를 보여 주는 스크린샷](./media/azure-sentinel/select-incident.png)

11. 인시던트 창에서 **전체 세부 정보 보기** 를 선택합니다. 인시던트 정보를 요약하고 자세한 정보를 제공하는 탭을 검토합니다.

    ![인시던트 정보에 대한 탭을 보여 주는 스크린샷](./media/azure-sentinel/full-details.png)

12. 그런 다음, **증거** > **이벤트** > **Log Analytics에 연결** 을 선택합니다. 결과에 시도 횟수와 함께 로그인을 시도하는 ID의 `UserPrincipalName` 값이 표시됩니다.

    ![선택한 인시던트의 전체 세부 정보를 보여 주는 스크린샷](./media/azure-sentinel/logs.png)

## <a name="automated-response"></a>자동화된 대응

Microsoft Sentinel은 [강력한 SOAR 기능](../sentinel/automation-in-azure-sentinel.md)을 제공합니다. Microsoft Sentinel에서 *플레이북* 이라는 자동화된 작업은 요구 사항에 맞게 분석 규칙에 연결할 수 있습니다.

이 예에서는 규칙에서 만드는 인시던트에 대한 이메일 알림을 추가합니다. 이 작업을 수행하려면 [Microsoft Sentinel GitHub 리포지토리에서 기존 플레이북](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification)을 사용합니다. 플레이북이 구성되면 기존 규칙을 편집하고, **자동화된 응답** 탭에서 플레이북을 선택합니다.

![규칙과 연결된 자동화된 응답에 대한 이미지 구성 화면을 보여 주는 스크린샷](./media/azure-sentinel/automation-tab.png)

## <a name="related-information"></a>관련 정보

Microsoft Sentinel 및 Azure AD B2C에 대한 자세한 내용은 다음을 참조하세요.

- [샘플 통합 문서](https://github.com/azure-ad-b2c/siem#workbooks)

- [Microsoft Sentinel 설명서](../sentinel/index.yml)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Microsoft Sentinel에서 가양성 처리](../sentinel/false-positives.md)
