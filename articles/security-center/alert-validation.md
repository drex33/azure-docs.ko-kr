---
title: Microsoft Defender for Cloud에서 경고 유효성 검사 | Microsoft Docs
description: Microsoft Defender for Cloud에서 보안 경고가 올바르게 구성 되었는지 확인 하는 방법에 대해 알아봅니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: b5acd38ec3885fab3d1de9d70a80fa76cd9c60b0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453291"
---
# <a name="alert-validation-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud에서 경고 유효성 검사

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서는 시스템이 클라우드 경고에 대해 Microsoft Defender에 대해 올바르게 구성 되었는지 확인 하는 방법을 알아봅니다.

## <a name="what-are-security-alerts"></a>보안 경고란?
경고는 리소스에 대 한 위협을 검색 하는 경우 Defender for Cloud가 생성 하는 알림입니다. Security Center는 신속하게 문제를 조사하기 위해 필요한 정보와 함께 경고에 우선 순위를 정하여 나열합니다. 또한 Defender for Cloud는 공격을 해결 하는 방법에 대 한 권장 사항을 제공 합니다.
자세한 내용은 [클라우드에 대 한 Defender의 보안 경고](alerts-overview.md) 및 [보안 경고 관리 및 대응](managing-and-responding-alerts.md) 을 참조 하세요.


## <a name="generate-sample-security-alerts"></a>샘플 보안 경고 생성

[Microsoft Defender For Cloud의 보안 경고 관리 및 대응](managing-and-responding-alerts.md)에 설명 된 대로 새로운 경고 미리 보기 환경을 사용 하는 경우 Azure Portal의 보안 경고 페이지에서 몇 번의 클릭으로 샘플 경고를 만들 수 있습니다.

샘플 경고를 사용하여 다음을 수행합니다.

- Microsoft Defender 계획의 가치 및 기능 평가
- 보안 경고에 대해 수행한 모든 구성(예: SIEM 통합, 워크플로 자동화, 메일 알림)의 유효성 검사

샘플 경고를 만들려면 다음을 수행합니다.

1. **구독 기여자** 역할이 있는 사용자는 경고 페이지의 도구 모음에서 **샘플 경고 만들기** 를 선택합니다.
1. 구독을 선택합니다.
1. 경고를 보려는 관련 Microsoft Defender 계획/s를 선택 합니다. 
1. **샘플 경고 만들기** 를 선택합니다.

    :::image type="content" source="media/alert-validation/create-sample-alerts-procedures.png" alt-text="Microsoft Defender for Cloud에서 샘플 경고를 만드는 단계입니다.":::
    
    샘플 경고가 생성되고 있음을 알리는 알림이 표시됩니다.

    :::image type="content" source="media/alert-validation/notification-sample-alerts-creation.png" alt-text="샘플 경고가 생성되고 있다는 알림입니다.":::

    몇 분 후에 경고가 보안 경고 페이지에 표시됩니다. 또한 클라우드 보안 경고 (연결 된 SIEMs, 이메일 알림 등)를 위해 Microsoft Defender를 수신 하도록 구성 된 다른 모든 위치에 표시 됩니다.

    :::image type="content" source="media/alert-validation/sample-alerts.png" alt-text="보안 경고 목록의 샘플 경고":::

    > [!TIP]
    > 시뮬레이션된 리소스에 대한 경고입니다.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Azure VM(Windows)에서 경고 시뮬레이션 <a name="validate-windows"></a>

컴퓨터에 Log Analytics 에이전트가 설치 된 후에는 경고의 공격을 받을 컴퓨터에서 다음 단계를 수행 합니다.

1. 실행 파일(예: **calc.exe**)을 컴퓨터의 바탕 화면 또는 사용자가 편리한 다른 디렉터리에 복사하고 파일 이름을 **ASC_AlertTest_662jfi039N.exe** 로 바꿉니다.
1. 명령 프롬프트를 열고 이 파일을 인수(가짜 인수 이름)와 함께 실행합니다(예: ```ASC_AlertTest_662jfi039N.exe -foo```).
1. 5 ~ 10 분 정도 기다렸다가 클라우드 경고에 대해 Defender를 엽니다. 경고가 표시됩니다.

> [!NOTE]
> Windows에 대한 이 테스트 경고를 검토할 때는 **Arguments Auditing Enabled(인수 감사 사용됨)** 필드가 **true** 인지 확인합니다. **false** 인 경우 명령줄 인수 감사를 사용하도록 설정해야 합니다. 사용하도록 설정하려면 다음 명령을 사용합니다. 
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Azure VM(Linux)에서 경고 시뮬레이션 <a name="validate-linux"></a>

컴퓨터에 Log Analytics 에이전트가 설치 된 후에는 경고의 공격을 받을 컴퓨터에서 다음 단계를 수행 합니다.

1. 실행 파일을 편리한 위치에 복사 하 고 이름을로 바꿉니다 `./asc_alerttest_662jfi039n` . 예를 들어:

    `cp /bin/echo ./asc_alerttest_662jfi039n`

1. 명령 프롬프트를 열고 다음 파일을 실행합니다.

    `./asc_alerttest_662jfi039n testing eicar pipe`

1. 5 ~ 10 분 정도 기다린 후 클라우드 경고에 대해 Defender를 엽니다. 경고가 표시됩니다.

## <a name="simulate-alerts-on-kubernetes"></a>Kubernetes에서 경고 시뮬레이션 <a name="validate-kubernetes"></a>

Azure Kubernetes Service를 클라우드 용 Defender와 통합 한 경우 다음 kubectl 명령을 사용 하 여 경고가 작동 하는지 테스트할 수 있습니다.

`kubectl get pods --namespace=asc-alerttest-662jfi039n`

Kubernetes 노드 및 클러스터를 방어 하는 방법에 대 한 자세한 내용은 [Kubernetes 용 Microsoft Defender 소개](defender-for-kubernetes-introduction.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 경고 유효성 검사 프로세스에 대해 소개했습니다. 이제 유효성 검사에 익숙해졌으므로 다음 문서를 시도해 보세요.

* [Microsoft Defender for Cloud에서 Azure Key Vault 위협 검색의 유효성을 검사 하는 중](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Microsoft defender For cloud에서 보안 경고 관리 및 대응](managing-and-responding-alerts.md) -경고를 관리 하는 방법 및 클라우드 용 defender의 보안 인시던트에 대응 하는 방법을 알아봅니다.
* [Microsoft Defender For Cloud의 보안 경고 이해](./alerts-overview.md) -다양 한 유형의 보안 경고에 대해 알아봅니다.
