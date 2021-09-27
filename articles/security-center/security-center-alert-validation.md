---
title: Azure Security Center에서 경고 유효성 검사 | Microsoft Docs
description: Azure Security Center에서 보안 경고가 올바르게 구성되었는지 확인하는 방법에 대해 알아봅니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: 48a695f4217c7300c4d7db6edc23452ad4eaa55f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631707"
---
# <a name="alert-validation-in-azure-security-center"></a>Azure Security Center에서 경고 유효성 검사
이 문서에서는 시스템이 Azure Security Center 경고에 대해 제대로 구성되었는지 확인하는 방법을 알아봅니다.

## <a name="what-are-security-alerts"></a>보안 경고란?
경고는 리소스에 대한 위협을 탐지할 때 Security Center에서 생성하는 알림입니다. Security Center는 신속하게 문제를 조사하기 위해 필요한 정보와 함께 경고에 우선 순위를 정하여 나열합니다. Security Center는 공격을 해결하는 방법에 대한 권장 사항도 제공합니다.
자세한 내용은 [Security Center의 보안 경고](security-center-alerts-overview.md) 및 [보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)을 참조하세요.


## <a name="generate-sample-azure-defender-alerts"></a>샘플 Azure Defender 경고 생성

[Azure Security Center의 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)에 설명된 대로 새로운 미리 보기 알림 환경을 사용하는 경우 Azure Portal의 보안 경고 페이지에서 몇 번의 클릭으로 샘플 경고를 만들 수 있습니다.

샘플 경고를 사용하여 다음을 수행합니다.

- Azure Defender의 가치 및 기능 평가
- 보안 경고에 대해 수행한 모든 구성(예: SIEM 통합, 워크플로 자동화, 메일 알림)의 유효성 검사

샘플 경고를 만들려면 다음을 수행합니다.

1. **구독 기여자** 역할이 있는 사용자는 경고 페이지의 도구 모음에서 **샘플 경고 만들기** 를 선택합니다.
1. 구독을 선택합니다.
1. 경고를 보려는 관련 Azure Defender 플랜을 선택합니다. 
1. **샘플 경고 만들기** 를 선택합니다.

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="Azure Security Center에서 샘플 경고를 만드는 단계":::
    
    샘플 경고가 생성되고 있음을 알리는 알림이 표시됩니다.

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="샘플 경고가 생성되고 있다는 알림입니다.":::

    몇 분 후에 경고가 보안 경고 페이지에 표시됩니다. 또한 Azure Security Center 보안 경고(연결된 SIEM, 메일 알림 등)를 수신하도록 구성한 다른 모든 위치에 표시됩니다.

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="보안 경고 목록의 샘플 경고":::

    > [!TIP]
    > 시뮬레이션된 리소스에 대한 경고입니다.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Azure VM(Windows)에서 경고 시뮬레이션 <a name="validate-windows"></a>

Security Center 에이전트가 컴퓨터에 설치된 경우 경고의 공격받은 리소스에 해당하는 컴퓨터에서 다음 단계를 따릅니다.

1. 실행 파일(예: **calc.exe**)을 컴퓨터의 바탕 화면 또는 사용자가 편리한 다른 디렉터리에 복사하고 파일 이름을 **ASC_AlertTest_662jfi039N.exe** 로 바꿉니다.
1. 명령 프롬프트를 열고 이 파일을 인수(가짜 인수 이름)와 함께 실행합니다(예: ```ASC_AlertTest_662jfi039N.exe -foo```).
1. 5~10분 정도 기다렸다가 Security Center 경고를 엽니다. 경고가 표시됩니다.

> [!NOTE]
> Windows에 대한 이 테스트 경고를 검토할 때는 **Arguments Auditing Enabled(인수 감사 사용됨)** 필드가 **true** 인지 확인합니다. **false** 인 경우 명령줄 인수 감사를 사용하도록 설정해야 합니다. 사용하도록 설정하려면 다음 명령을 사용합니다. 
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Azure VM(Linux)에서 경고 시뮬레이션 <a name="validate-linux"></a>

Security Center 에이전트가 컴퓨터에 설치된 경우 경고의 공격받은 리소스에 해당하는 컴퓨터에서 다음 단계를 따릅니다.

1. 실행 파일을 편리한 위치에 복사하고 이름을 로 `./asc_alerttest_662jfi039n` 바꿉니다. 예:

    `cp /bin/echo ./asc_alerttest_662jfi039n`

1. 명령 프롬프트를 열고 다음 파일을 실행합니다.

    `./asc_alerttest_662jfi039n testing eicar pipe`

1. 5~10분 정도 기다린 다음 Security Center 경고를 엽니다. 경고가 표시됩니다.

## <a name="simulate-alerts-on-kubernetes"></a>Kubernetes에서 경고 시뮬레이션 <a name="validate-kubernetes"></a>

Azure Kubernetes Service를 Security Center와 통합한 경우 다음 kubectl 명령을 사용하여 경고가 작동하는지 테스트할 수 있습니다.

`kubectl get pods --namespace=asc-alerttest-662jfi039n`

Kubernetes 노드 및 클러스터를 방어하는 방법에 대한 자세한 내용은 [Azure Defender for Kubernetes 소개](defender-for-kubernetes-introduction.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
이 문서에서는 경고 유효성 검사 프로세스에 대해 소개했습니다. 이제 유효성 검사에 익숙해졌으므로 다음 문서를 시도해 보세요.

* [Azure Security Center에서 Azure Key Vault 위협 검색의 유효성 검사](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center의 보안 경고 이해](./security-center-alerts-overview.md) - 다양한 유형의 보안 경고에 대해 알아봅니다.
