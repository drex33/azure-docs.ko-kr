---
title: Azure Security Center용 에이전트 자동 배포 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 사용하는 Log Analytics 에이전트, 기타 에이전트 및 확장을 자동으로 프로비저닝하도록 설정하는 방법을 설명합니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 10/08/2021
ms.author: memildin
ms.openlocfilehash: 58f4ce3a7b7faeec729f0b56cad6adb23a0cd2f2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713729"
---
# <a name="configure-auto-provisioning-for-agents-and-extensions-from-azure-security-center"></a>Azure Security Center에서 에이전트 및 확장에 대한 자동 프로비저닝 구성

Azure Security Center는 해당 리소스의 관련 에이전트 또는 확장 및 활성화된 데이터 수집 유형을 사용하여 리소스에서 데이터를 수집합니다. 아래 절차를 사용하여 리소스에 Security Center에서 사용하는 필수 에이전트 및 확장이 있는지 확인합니다.

## <a name="prerequisites"></a>사전 요구 사항
Security Center를 시작하려면 Microsoft Azure에 대한 구독이 있어야 합니다. 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)에 가입할 수 있습니다.

## <a name="availability"></a>가용성

| 양상                  | 세부 정보                                                                                                                                                                                                                      |
|-------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 릴리스 상태:          | **기능**: 자동 프로비저닝은 GA(일반 공급)입니다.<br>**에이전트 및 확장**: Azure VM용 Log Analytics 에이전트는 GA이고 Microsoft 종속성 에이전트는 미리 보기 상태이며, Kubernetes용 정책 추가 기능은 GA이고, 게스트 구성 에이전트는 미리 보기 상태입니다.  |
| 가격 책정:                | Free                                                                                                                                                                                                                         |
| 지원되는 대상: | :::image type="icon" source="./media/icons/yes-icon.png"::: Azure 머신<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure Arc 머신<br>:::image type="icon" source="./media/icons/no-icon.png"::: Kubernetes 노드<br>:::image type="icon" source="./media/icons/no-icon.png":::Virtual Machine Scale Sets |
| 클라우드:                 | **기능**:<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government, Azure 중국 21Vianet<br>**에이전트 및 확장**:<br>Azure VM용 Log Analytics 에이전트는 모든 클라우드에서 사용할 수 있으며, Kubernetes용 정책 추가 기능은 모든 클라우드에서 사용할 수 있지만 게스트 구성 에이전트는 상업용 클라우드에서만 사용할 수 있습니다.  |
|                         |                                                                                                                                                                                                                              |

## <a name="how-does-security-center-collect-data"></a>Security Center에서 데이터를 수집하는 방법은 무엇인가요?

Security Center는 Azure VM(가상 머신), 가상 머신 확장 집합, IaaS 컨테이너 및 비 Azure(온-프레미스 포함) 컴퓨터에서 데이터를 수집하여 보안 취약성 및 위협을 모니터링합니다. 

누락된 업데이트, 잘못 구성된 OS 보안 설정, 엔드포인트 보호 상태, 상태 및 위협 방지에 대한 가시성을 제공하려면 데이터 컬렉션이 필요합니다. 데이터 수집은 VM, 가상 머신 확장 집합, IaaS 컨테이너 및 비 Azure 컴퓨터와 같은 컴퓨팅 리소스에서만 필요합니다. 

에이전트를 프로비저닝하지 않더라도 Azure Security Center의 이점을 제공합니다. 그러나 보안이 제한적이며 위에 나열된 기능은 지원되지 않습니다.  

데이터는 다음을 사용하여 수집됩니다.

- **Log Analytics 에이전트** - 컴퓨터에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석을 위해 데이터를 작업 영역에 복사합니다. 이러한 데이터의 예로는 운영 체제 유형 및 버전, 운영 체제 로그(Windows 이벤트 로그), 실행 중인 프로세스, 머신 이름, IP 주소, 로그인된 사용자를 들 수 있습니다.
- **보안 확장**(예: [Kubernetes에 대한 Azure Policy 추가 기능](../governance/policy/concepts/policy-for-kubernetes.md)) - 특수 리소스 종류와 관련된 데이터를 Security Center에 제공할 수도 있습니다.

> [!TIP]
> Security Center가 성장함에 따라 모니터링할 수 있는 리소스 종류도 증가했습니다. 확장의 수도 증가했습니다. 자동 프로비저닝은 Azure Policy 기능을 활용하여 추가 리소스 종류를 지원하도록 확장되었습니다.

## <a name="why-use-auto-provisioning"></a>자동 프로비저닝을 사용하는 이유
이 페이지에서 설명하는 모든 에이전트 및 확장은 수동으로 *설치할 수 있습니다*([Log Analytics 에이전트 수동 설치](#manual-agent) 참조). 그러나 **자동 프로비저닝** 은 지원되는 모든 리소스에 대한 더 빠른 보안 적용 범위를 보장하기 위해 필요한 모든 에이전트 및 확장을 기존 및 새 컴퓨터에 설치하여 관리 오버헤드를 줄입니다. 

자동 프로비저닝을 사용하도록 설정하는 것이 좋지만, 기본적으로 사용하지 않도록 설정되어 있습니다.

## <a name="how-does-auto-provisioning-work"></a>자동 프로비저닝의 작동 방식
Security Center의 자동 프로비저닝 설정에는 지원되는 각 확장 유형에 대한 설정/해제가 있습니다. 확장의 자동 프로비저닝을 사용하도록 설정할 때 적절한 **존재하지 않는 경우 배포** 정책을 할당합니다. 이 정책 유형을 사용하면 해당 유형의 기존 및 미래의 모든 리소스에서 확장이 프로비저닝됩니다.

> [!TIP]
> [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)에서 '없는 경우 배포'를 포함하여 Azure Policy 효과에 대해 자세히 알아보세요.


## <a name="enable-auto-provisioning-of-the-log-analytics-agent-and-extensions"></a>Log Analytics 에이전트 및 <a name="auto-provision-mma"></a> 확장의 자동 프로비저닝 사용

Log Analytics 에이전트에 대한 자동 프로비저닝이 설정되어 있으면 Security Center에서 에이전트를 지원되는 모든 Azure VM 및 새로 만든 Azure VM에 배포합니다. 지원되는 플랫폼 목록은 [Azure Security Center에서 지원되는 플랫폼](security-center-os-coverage.md)을 참조하세요.

Log Analytics 에이전트를 자동으로 프로비저닝하도록 설정하려면 다음을 수행합니다.

1. Security Center 메뉴에서 **가격 책정 및 설정** 을 선택합니다.
1. 관련 구독을 선택합니다.
1. **자동 프로비저닝** 페이지에서 Log Analytics 에이전트에 대한 자동 프로비저닝 상태를 **켜기** 로 설정합니다.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Log Analytics 에이전트의 자동 프로비저닝을 사용하도록 설정합니다." lightbox="./media/security-center-enable-data-collection/enable-automatic-provisioning.png":::

1. 구성 옵션 창에서 사용할 작업 영역을 정의합니다.

    :::image type="content" source="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png" alt-text="Log Analytics 에이전트를 VM에 자동으로 프로비저닝하기 위한 구성 옵션." lightbox="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png":::

    - **Security Center에서 만든 기본 작업 영역에 Azure VM 연결** - Security Center에서 새 리소스 그룹 및 기본 작업 영역을 동일한 지리적 위치에 만들고 에이전트를 해당 작업 영역에 연결합니다. 구독에 여러 지리적 위치의 VM이 포함된 경우 Security Center에서 데이터 개인 정보 보호 요구 사항을 준수하기 위해 여러 작업 영역을 만듭니다.

        작업 영역 및 리소스 그룹에 대한 명명 규칙은 다음과 같습니다. 
        - 작업 영역: DefaultWorkspace-[subscription-ID]-[geo] 
        - 리소스 그룹: DefaultResourceGroup-[geo] 

        Security Center는 구독에 설정된 가격 책정 계층에 따라 작업 영역에서 자동으로 Security Center 솔루션을 사용하도록 설정합니다. 

        > [!TIP]
        > 기본 작업 영역에 대한 질문은 다음을 참조하세요.
        >
        > - [Security Center에서 만든 작업 영역에서 Azure Monitor 로그에 대한 요금을 청구하나요?](./faq-data-collection-agents.yml#am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center-)
        > - [기본 Log Analytics 작업 영역은 어디에서 생성되나요?](./faq-data-collection-agents.yml#where-is-the-default-log-analytics-workspace-created-)
        > - [Security Center에서 만든 기본 작업 영역을 삭제할 수 있나요?](./faq-data-collection-agents.yml#can-i-delete-the-default-workspaces-created-by-security-center-)

    - **다른 작업 영역에 Azure VM 연결** - 드롭다운 목록에서 수집된 데이터를 저장할 작업 영역을 선택합니다. 드롭다운 목록에는 모든 구독에 대한 모든 작업 영역이 포함됩니다. 이 옵션을 사용하여 서로 다른 구독에서 실행되는 가상 머신에서 데이터를 수집하고 선택한 작업 영역에 모두 저장할 수 있습니다.  

        이미 기존 Log Analytics 작업 영역이 있는 경우 동일한 작업 영역을 사용하는 것이 좋습니다(작업 영역에 대한 읽기 및 쓰기 권한 필요). 이 옵션은 조직에서 중앙 집중식 작업 영역을 사용하고 있고 보안 데이터 수집에 사용하려는 경우에 유용합니다. [Azure Monitor에서 로그 데이터 및 작업 영역에 대한 액세스 관리](../azure-monitor/logs/manage-access.md)에서 자세히 알아보세요.

        선택한 작업 영역에서 이미 "Security" 또는 "SecurityCenterFree" 솔루션을 사용하도록 설정된 경우 가격 책정이 자동으로 설정됩니다. 그렇지 않은 경우 Security Center 솔루션을 작업 영역에 설치합니다.

        1. Security Center 메뉴에서 **가격 책정 및 설정** 을 엽니다.
        1. 에이전트를 연결할 작업 영역을 선택합니다.
        1. **Azure Defender 켜기** 또는 **Azure Defender 끄기** 를 선택합니다.

1. **Windows 보안 이벤트** 구성에서 저장할 원시 이벤트 데이터의 양을 선택합니다.
    - **없음** – 보안 이벤트 스토리지를 사용하지 않도록 설정합니다. 이 값은 기본 설정입니다.
    - **최소** – 이벤트 볼륨을 최소화하려는 경우에 대한 작은 이벤트 집합입니다.
    - **일반** – 대부분의 고객을 만족시키고 전체 감사 내역을 제공하는 이벤트 세트입니다.
    - **모든 이벤트** – 모든 이벤트를 저장하려는 고객용입니다.

    > [!TIP]
    > 이러한 옵션을 작업 영역 수준에서 설정하려면 [작업 영역 수준에서 보안 이벤트 옵션 설정](#setting-the-security-event-option-at-the-workspace-level)을 참조하세요.
    > 
    > 이러한 옵션에 대한 자세한 내용은 [Log Analytics 에이전트에 대한 Windows 보안 이벤트 옵션](#data-collection-tier)을 참조하세요.

1. 구성 창에서 **적용** 을 선택합니다.

1. Log Analytics 에이전트 이외의 확장을 자동으로 프로비저닝하도록 설정하려면 다음을 수행합니다. 

    1. Microsoft 종속성 에이전트에 대해 자동 프로비저닝을 사용하도록 설정하는 경우 Log Analytics 에이전트가 자동 배포로 설정되어 있는지 확인합니다.
    1. 관련 확장에 대한 상태를 **켜기** 로 전환합니다.

        :::image type="content" source="./media/security-center-enable-data-collection/toggle-kubernetes-add-on.png" alt-text="K8s 정책 추가 기능에 대해 자동 프로비저닝을 사용하도록 설정합니다.":::

    1. **저장** 을 선택합니다. Azure Policy 정의가 할당되고 수정 작업이 만들어집니다.

        |확장명  |정책  |
        |---------|---------|
        |Kubernetes에 대한 Policy 추가 기능                      |[Azure Kubernetes Service 클러스터에 Azure Policy 추가 기능 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa8eff44f-8c92-45c3-a3fb-9880802d67a7)|
        |Microsoft 종속성 에이전트(미리 보기)(Windows VM)|[Windows 가상 머신용 종속성 에이전트 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1c210e94-a481-4beb-95fa-1571b434fb04)         |
        |Microsoft 종속성 에이전트(미리 보기)(Linux VM)  |[Linux 가상 머신용 종속성 에이전트 배포](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da21710-ce6f-4e06-8cdb-5cc4c93ffbee)|
        |게스트 구성 에이전트(미리 보기)               |[필수 구성 요소를 배포하여 가상 머신에서 게스트 구성 정책을 사용하도록 설정](https://github.com/Azure/azure-policy/blob/64dcfa3033a3ff231ec4e73d2c1dad4db4e3b5dd/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json)|
        |||

1. **저장** 을 선택합니다. 작업 영역을 프로비저닝해야 하는 경우 에이전트 설치에는 최대 25분이 걸릴 수 있습니다.

1. 이전에 기본 작업 영역에 연결되어 모니터링되는 VM을 다시 구성할지 묻는 메시지가 표시됩니다.

    :::image type="content" source="./media/security-center-enable-data-collection/reconfigure-monitored-vm.png" alt-text="모니터링되는 VM을 다시 구성하는 옵션을 검토합니다.":::

    - **아니요** - 새 작업 영역 설정은 Log Analytics 에이전트가 설치되지 않은 새로 검색된 VM에만 적용됩니다.
    - **예** - 새 작업 영역 설정이 모든 VM에 적용되고 현재 Security Center에서 만든 작업 영역에 연결된 모든 VM이 새 대상 작업 영역에 다시 연결됩니다.

   > [!NOTE]
   > **예** 를 선택하는 경우 모든 VM이 새 대상 작업 영역에 다시 연결될 때까지 Security Center에서 만든 작업 영역을 삭제하지 마세요. 작업 영역을 너무 빨리 삭제하면 이 작업이 실패합니다.


## <a name="windows-security-event-options-for-the-log-analytics-agent"></a>Log Analytics 에이전트에 대한 Windows 보안 이벤트 옵션 <a name="data-collection-tier"></a> 

Azure Security Center에서 데이터 수집 계층을 선택하면 Log Analytics 작업 영역에 있는 보안 이벤트의 *스토리지* 에만 영향을 줍니다. Log Analytics 에이전트는 작업 영역에 저장하도록 선택한 보안 이벤트 수준에 관계없이 Security Center의 위협 방지에 필요한 보안 이벤트를 계속 수집하고 분석합니다. 보안 이벤트를 저장하도록 선택하면 작업 영역에서 해당 이벤트를 조사, 검색 및 감사할 수 있습니다.

### <a name="requirements"></a>요구 사항 
Azure Defender는 Windows 보안 이벤트 데이터를 저장하는 데 필요합니다. [Azure Defender에 대해 자세히 알아보세요](azure-defender.md).

Log Analytics의 데이터를 저장하면 데이터 스토리지에 대한 추가 요금이 발생할 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

### <a name="information-for-azure-sentinel-users"></a>Azure Sentinel 사용자에 대한 정보 
Azure Sentinel 사용자: 단일 작업 영역의 컨텍스트 내에서 보안 이벤트 수집은 Azure Security Center 또는 Azure Sentinel에서 구성할 수 있지만 둘 다에서 구성할 수는 없습니다. Azure Security Center에서 이미 경고를 받고 있고 보안 이벤트를 수집하도록 설정된 작업 영역에 Azure Sentinel을 추가할 계획인 경우 두 가지 옵션이 있습니다.
- Azure Security Center의 보안 이벤트 컬렉션은 그대로 둡니다. Azure Sentinel 및 Azure Defender에서 이러한 이벤트를 쿼리하고 분석할 수 있습니다. 그러나 커넥터의 연결 상태를 모니터링하거나 Azure Sentinel에서 해당 구성을 변경할 수 없습니다. 이것이 중요하다면 두 번째 옵션을 고려합니다.
- Azure Security Center에서 보안 이벤트 수집을 사용하지 않도록 설정합니다(Log Analytics 에이전트 구성에서 **Windows 보안 이벤트** 를 **없음** 으로 설정). 그런 다음, Azure Sentinel에서 보안 이벤트 커넥터를 추가합니다. 첫 번째 옵션과 마찬가지로 Azure Sentinel 및 Azure Defender/ASC에서 이벤트를 쿼리하고 분석할 수 있지만, 이제 Azure Sentinel에서만 커넥터의 연결 상태를 모니터링하거나 구성을 변경할 수 있습니다.

### <a name="what-event-types-are-stored-for-common-and-minimal"></a>"일반" 및 "최소"에 대해 저장되는 이벤트 유형은 무엇인가요?
이러한 집합은 일반적인 시나리오를 해결하도록 설계되었습니다. 특정 집합을 구현하기 전에 요구에 적합한 집합을 평가하세요.

**일반** 및 **최소** 옵션에 대한 이벤트를 결정하기 위해 고객과 협력하고 업계 표준을 사용하여 각 이벤트에 대한 필터링되지 않은 빈도와 이를 사용하는 방법을 알아보았습니다. 그에 따라 이 프로세스에서는 다음 지침이 사용되었습니다.

- **최소** - 이 집합에는 보안 침해 성공을 나타낼 수 있는 이벤트와 중요 이벤트(양이 매우 적음)만 포함하세요. 예를 들어 로그인 성공/실패(이벤트 ID 4624, 4625)는 이 집합에 포함되지만 로그아웃은 포함되지 않습니다. 로그아웃의 경우 감사에서는 중요하지만 검색에서는 중요하지 않으며 양이 비교적 많기 때문입니다. 이 집합에 포함되는 데이터 볼륨 중 대부분은 로그인 이벤트 및 프로세스 만들기 이벤트(이벤트 ID 4688)입니다.
- **일반** - 이 집합에서는 전체 사용자 감사 내역을 제공하세요. 예를 들어 사용자 로그인 및 사용자 로그아웃(이벤트 ID 4634)을 모두 이 집합에 포함합니다. Microsoft는 보안 그룹 변경, 주요 도메인 컨트롤러 Kerberos 작업, 그리고 업계의 조직에서 권장하는 기타 이벤트와 같은 감사 작업을 이 집합에 포함했습니다.

양이 매우 적은 이벤트는 일반 집합에 포함되었습니다. 모든 이벤트를 선택하는 대신 이러한 이벤트를 선택하는 주된 이유는 특정 이벤트를 필터링하여 제외하기 위해서가 아니라 이벤트의 양을 줄이기 위해서이기 때문입니다.

아래에는 각 집합별 전체 보안 및 App Locker 이벤트 ID가 나와 있습니다.

| 데이터 계층 | 수집된 이벤트 표시기 |
| --- | --- |
| 최소 | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| 일반 | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - GPO(그룹 정책 개체)를 사용하는 경우 감사 정책인 프로세스 만들기 이벤트 4688과 *CommandLine* 필드 내부 이벤트 4688을 사용하는 것이 좋습니다. 프로세스 만들기 이벤트 4688에 대한 자세한 내용은 Security Center의 [FAQ](./faq-data-collection-agents.yml#what-happens-when-data-collection-is-enabled-)를 참조하세요. 감사 정책에 대한 자세한 내용은 [감사 정책 권장 사항](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)을 참조하세요.
> -  [적응형 애플리케이션 제어](security-center-adaptive-application.md)에 데이터 수집을 사용하기 위해 Security Center는 모든 애플리케이션을 허용하도록 감사 모드에서 로컬 AppLocker 정책을 구성합니다. 이렇게 하면 AppLocker가 이벤트를 생성하게 되고, 이 이벤트를 Security Center에서 수집하여 활용합니다. 이 정책은 이미 AppLocker 정책이 구성된 컴퓨터에서는 구성할 수 없습니다. 
> - Windows 필터링 플랫폼 [이벤트 ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)을 수집하려면 [감사 필터링 플랫폼 연결](/windows/security/threat-protection/auditing/audit-filtering-platform-connection)을 사용하도록 설정해야 합니다(Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable).
>

### <a name="setting-the-security-event-option-at-the-workspace-level"></a>작업 영역 수준에서 보안 이벤트 옵션 설정

작업 영역 수준에서 저장할 보안 이벤트 데이터의 수준을 정의할 수 있습니다.

1. Azure Portal의 Security Center 메뉴에서 **가격 책정 및 설정** 을 선택합니다.
1. 관련 작업 영역을 선택합니다. 작업 영역에 대한 유일한 데이터 수집 이벤트는 이 페이지에서 설명하는 Windows 보안 이벤트입니다.

    :::image type="content" source="media/security-center-enable-data-collection/event-collection-workspace.png" alt-text="작업 영역에 저장할 보안 이벤트 데이터를 설정합니다.":::

1. 저장할 원시 이벤트 데이터의 양을 선택하고, **저장** 을 선택합니다.

## <a name="manual-agent-provisioning"></a>수동 에이전트 프로비전 <a name="manual-agent"></a>
 
Log Analytics 에이전트를 수동으로 설치하려면 다음을 수행합니다.

1. 자동 프로비저닝을 사용하지 않도록 설정합니다.

1. 필요에 따라 작업 영역을 생성합니다.

1. Log Analytics 에이전트를 설치할 작업 영역에서 Azure Defender를 사용하도록 설정합니다.

    1. Security Center 메뉴에서 **가격 책정 및 설정** 을 선택합니다.

    1. 에이전트를 설치할 작업 영역을 설정합니다. 작업 영역이 Security Center에서 사용하는 것과 동일한 구독에 있고 작업 영역에 대한 읽기/쓰기 권한이 있는지 확인합니다.

    1. **Azure Defender 켜기** 및 **저장** 을 선택합니다.

       >[!NOTE]
       >작업 영역에서 **보안** 또는 **SecurityCenterFree** 솔루션이 이미 설정된 경우 가격 책정이 자동으로 설정됩니다. 

1. Resource Manager 템플릿을 사용하여 에이전트를 새 VM에 배포하려면 Log Analytics 에이전트를 설치합니다.

   - [Windows용 Log Analytics 에이전트 설치](../virtual-machines/extensions/oms-windows.md)
   - [Linux용 Log Analytics 에이전트 설치](../virtual-machines/extensions/oms-linux.md)

1. 에이전트를 기존 VM에 배포하려면 [Azure Virtual Machines에 대한 데이터 수집](../azure-monitor/vm/monitor-virtual-machine.md)의 지침을 따릅니다(**이벤트 및 성능 데이터 수집** 섹션은 선택 사항임).

1. PowerShell을 사용하여 에이전트를 배포하려면 가상 머신 설명서의 지침을 사용하세요.

    - [Windows 컴퓨터의 경우](../virtual-machines/extensions/oms-windows.md?toc=%2fazure%2fazure-monitor%2ftoc.json#powershell-deployment)
    - [Linux 머신의 경우](../virtual-machines/extensions/oms-linux.md?toc=%2fazure%2fazure-monitor%2ftoc.json#azure-cli-deployment)

> [!TIP]
> PowerShell을 사용하여 Security Center를 온보드하는 방법에 대한 지침은 [PowerShell을 사용하여 Azure Security Center의 온보딩 자동화](security-center-powershell-onboarding.md)를 참조하세요.


## <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>기존 에이전트 설치 시 자동 프로비저닝 <a name="preexisting"></a> 

다음 사용 사례는 이미 에이전트 또는 확장이 설치된 경우에 자동 프로비전의 작동 방식을 지정합니다. 

- **Log Analytics 에이전트가 컴퓨터에 설치되어 있지만 확장으로 설치되지 않음(직접 에이전트)** - Log Analytics 에이전트가 VM에 직접 설치되어 있는 경우(Azure 확장이 아님) Security Center에서 Log Analytics 에이전트 확장을 설치하고 Log Analytics 에이전트를 최신 버전으로 업그레이드할 수 있습니다.
설치된 에이전트는 이미 구성된 작업 영역에 계속 보고하고, Security Center에서 구성된 작업 영역에도 보고합니다(Windows 컴퓨터에서는 멀티 호밍이 지원됨).
구성된 작업 영역이 사용자 작업 영역(Security Center의 기본 작업 영역이 아님)인 경우 해당 작업 영역에 보고하는 VM 및 컴퓨터에서 이벤트 처리를 시작하려면 해당 작업 영역에 “Security” 또는 “SecurityCenterFree” 솔루션을 설치해야 합니다.

    Linux 컴퓨터의 경우 에이전트 멀티 호밍은 아직 지원되지 않습니다. 따라서 기존 에이전트 설치가 탐지되면 자동 프로비저닝이 발생하지 않고 컴퓨터의 구성이 변경되지 않습니다.

    2019년 3월 17일 이전에 Security Center에 온보딩된 구독에 있는 기존 컴퓨터의 경우 기존 에이전트가 검색되면 Log Analytics 에이전트 확장이 설치되지 않고 컴퓨터에 영향을 주지 않습니다. 이러한 컴퓨터의 경우 해당 컴퓨터에서 에이전트 설치 문제를 해결하려면 "컴퓨터의 모니터링 에이전트 상태 문제 해결" 권장 사항을 참조하세요
  
- **System Center Operations Manager 에이전트가 컴퓨터에 설치되어 있음** - Security Center에서 Log Analytics 에이전트 확장을 기존 Operations Manager에 나란히 설치합니다. 기존 Operations Manager 에이전트는 Operations Manager 서버에 정상적으로 계속 보고합니다. Operations Manager 에이전트 및 Log Analytics 에이전트는 일반적인 런타임 라이브러리를 공유하며 이는 이 프로세스 중에 최신 버전으로 업데이트됩니다. Operations Manager 에이전트 버전 2012가 설치된 경우 자동 프로비저닝을 사용하도록 설정하지 **않습니다**.

- **기존 VM 확장이 있음**
    - 모니터링 에이전트가 확장으로 설치되면 확장 구성을 통해 단일 작업 영역에만 보고할 수 있습니다. Security Center는 사용자 작업 영역에 대한 기존 연결을 재정의하지 않습니다. "Security" 또는 "SecurityCenterFree" 솔루션이 설치되어 있으면 Security Center는 이미 연결된 작업 영역에 있는 VM의 보안 데이터를 저장합니다. 이 프로세스에서 Security Center가 확장 버전을 최신 버전으로 업그레이드할 수 있습니다.
    - 기존 작업 영역이 어떤 작업 영역으로 데이터를 전송하는지 확인하려면 [Azure Security Center를 사용하여 연결 유효성을 검사](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center)하는 테스트를 실행하세요. 또는 Log Analytics 작업 영역을 열고, 작업 영역을 선택하고, VM을 선택하고, Log Analytics 에이전트 연결을 확인하는 방법도 있습니다.
    - Log Analytics 에이전트가 클라이언트 워크스테이션에 설치되어 있고 기존 Log Analytics 작업 영역에 보고하는 환경을 갖고 있는 경우 [Azure Security Center에서 지원하는 운영 체제](security-center-os-coverage.md) 목록을 검토하여 현재 운영 체제가 지원되는지 확인하세요. 자세한 내용은 [기존 Log Analytics 고객](./faq-azure-monitor-logs.yml)을 참조하세요.
 

## <a name="disable-auto-provisioning"></a>자동 프로비저닝 사용 안 함 <a name="offprovisioning"></a>

자동 프로비저닝을 사용하지 않도록 설정하면 에이전트가 새 VM에 프로비저닝되지 않습니다.

에이전트를 자동으로 프로비저닝하지 않도록 설정하려면 다음을 수행합니다.

1. 포털의 Security Center 메뉴에서 **가격 책정 & 설정** 을 선택합니다.
1. 관련 구독을 선택합니다.
1. **자동 프로비저닝** 을 선택합니다.
1. 관련 에이전트에 대한 상태를 **끄기** 로 전환합니다.

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="에이전트 유형별 자동 프로비저닝을 비활성화하도록 전환합니다.":::

1. **저장** 을 선택합니다. 자동 프로비저닝을 사용하지 않도록 설정하면 기본 작업 영역 구성 섹션이 표시되지 않습니다.

    :::image type="content" source="./media/security-center-enable-data-collection/empty-configuration-column.png" alt-text="자동 프로비저닝을 사용하지 않도록 설정하면 구성 셀이 비어 있음":::


> [!NOTE]
>  자동 프로비저닝을 해제해도 에이전트가 프로비저닝된 Azure VM에서 Log Analytics 에이전트가 제거되지는 않습니다. OMS 확장을 제거하는 방법은 [Security Center가 설치한 OMS 확장을 제거하는 방법](./faq-data-collection-agents.yml#how-do-i-remove-oms-extensions-installed-by-security-center-)을 참조하세요.
>


## <a name="troubleshooting"></a>문제 해결

-   자동 프로비저닝 설치 문제를 파악하려면 [에이전트 상태 문제 모니터링](security-center-troubleshooting-guide.md#mon-agent)을 참조하세요.
-  모니터링 에이전트 네트워크 요구 사항을 파악하려면 [모니터링 에이전트 네트워크 요구 사항 문제 해결](security-center-troubleshooting-guide.md#mon-network-req)을 참조하세요.
-   수동 온보딩 문제를 파악하려면 [Operations Management Suite 온보딩 문제 해결 방법](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)을 참조하세요.



## <a name="next-steps"></a>다음 단계
이 페이지에서는 Log Analytics 에이전트 및 기타 Security Center 확장에 대해 자동 프로비저닝을 사용하도록 설정하는 방법을 설명했습니다. 수집된 데이터를 저장할 Log Analytics 작업 영역을 정의하는 방법에 대해서도 설명합니다. 데이터 컬렉션을 사용하도록 설정하려면 두 작업을 모두 수행해야 합니다. 새 작업 영역을 사용하든 기존 작업 영역을 사용하든 관계없이 Log Analytics에 데이터를 저장하면 데이터 스토리지에 대한 추가 요금이 발생할 수 있습니다. 선택한 통화와 지역에 따른 가격 정보는 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)을 참조하세요.