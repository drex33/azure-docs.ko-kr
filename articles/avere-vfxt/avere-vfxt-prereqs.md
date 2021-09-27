---
title: Avere vFXT 필수 조건 - Azure
description: 구독, 할당량 및 스토리지 서비스 엔드포인트 처리를 비롯하여 Avere vFXT for Azure에서 클러스터를 만들기 전에 수행할 작업에 대해 알아봅니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 36ff2aadaf579403603dcf920050e52c0a8bf600
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124807697"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Avere vFXT 만들기 준비

이 문서에서는 Avere vFXT 클러스터를 만들기 위한 필수 작업에 대해 설명합니다.

## <a name="create-a-new-subscription"></a>새 구독 만들기

먼저 새 Azure 구독을 만듭니다. 각 Avere vFXT 프로젝트에 대해 별도의 구독을 사용하여 비용 추적과 정리를 간소화하고 클러스터 워크플로를 프로비저닝할 때 다른 프로젝트가 할당량 또는 리소스 제한의 영향을 받지 않도록 합니다.

Azure Portal에 새 Azure 구독을 만들려면 다음을 수행합니다.

1. [구독 블레이드](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)로 이동합니다.
1. 위쪽에 있는 **+ 추가** 단추를 클릭합니다.
1. 메시지가 표시되면 로그인합니다.
1. 제안을 선택하고 새 구독을 만드는 단계를 수행합니다.

## <a name="configure-subscription-owner-permissions"></a>구독 소유자 권한 구성

구독에 대한 소유자 권한이 있는 사용자는 vFXT 클러스터를 만들어야 합니다. 클러스터를 만들려면 소유자가 소프트웨어 서비스 약관에 동의하고 네트워크 및 스토리지 리소스에 대한 변경을 승인해야 합니다.

비소유자가 Avere vFXT for Azure 클러스터를 만들 수 있도록 허용하는 몇 가지 해결 방법이 있습니다. 이러한 시나리오에는 리소스를 제한하고 작성자에게 추가적인 Azure 역할을 할당하는 작업이 포함됩니다. 이러한 모든 경우에 구독 소유자는 미리 [Avere vFXT 소프트웨어 약관에도 동의](#accept-software-terms)해야 합니다.

| 시나리오 | 제한 | Avere vFXT 클러스터를 만드는 데 필요한 액세스 역할 |
|----------|--------|-------|
| 리소스 그룹 관리자가 vFXT 생성 | 가상 네트워크, 클러스터 컨트롤러 및 클러스터 노드를 리소스 그룹 내에서 만들어야 합니다. | [사용자 액세스 관리자](../role-based-access-control/built-in-roles.md#user-access-administrator) 및 [참가자](../role-based-access-control/built-in-roles.md#contributor) 역할, 둘 다 대상 리소스 그룹으로 범위가 지정되었습니다. |
| 기존의 외부 가상 네트워크 사용 | 클러스터 컨트롤러와 클러스터 노드는 vFXT의 리소스 그룹 내에 만들어지지만 다른 리소스 그룹의 기존 가상 네트워크를 사용합니다. | (1) [사용자 액세스 관리자](../role-based-access-control/built-in-roles.md#user-access-administrator) 및 [참가자](../role-based-access-control/built-in-roles.md#contributor) 역할은 vFXT 리소스 그룹으로 범위가 지정됨, (2) [가상 머신 참가자](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [사용자 액세스 관리자](../role-based-access-control/built-in-roles.md#user-access-administrator) 및 [Avere 참가자](../role-based-access-control/built-in-roles.md#avere-contributor) 역할은 가상 네트워크 리소스 그룹으로 범위가 지정됨. |
| 클러스터 작성자를 위한 사용자 지정 역할 | 리소스 배치 제한이 없습니다. 이 메서드는 비소유자에게 중요한 권한을 제공합니다. | 구독 소유자는 [이 문서](avere-vfxt-non-owner.md)에 설명된 대로 Azure 사용자 지정 역할을 만듭니다. |

## <a name="quota-for-the-vfxt-cluster"></a>vFXT 클러스터에 대한 할당량

다음 Azure 구성 요소에 충분한 할당량이 있는지 확인합니다. 필요한 경우 [할당량 증가를 요청합니다](../azure-portal/supportability/regional-quota-requests.md).

> [!NOTE]
> 여기에 나와 있는 가상 머신 및 SSD 구성 요소는 vFXT 클러스터 자체에 사용되는 것입니다. 계산 팜에 사용할 VM 및 SSD에 대한 할당량도 필요합니다.
>
> 워크플로를 실행할 지역에 대한 할당량을 사용하도록 설정되어 있는지 확인하세요.

|Azure 구성 요소|할당량|
|----------|-----------|
|가상 머신|3개 이상의 E32s_v3(클러스터 노드당 1개) |
|프리미엄 SSD 스토리지|노드당 200GB OS 공간 + 1TB-4TB 캐시 공간 |
|스토리지 계정(선택 사항) |v2|
|데이터 백 엔드 스토리지(선택 사항) |새 LRS Blob 컨테이너 1개 |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>소프트웨어 약관 동의

> [!TIP]
> 구독 소유자가 Avere vFXT 클러스터를 만드는 경우 이 단계를 건너뜁니다.

클러스터를 만들 때 Avere vFXT 소프트웨어의 서비스 약관에 동의해야 합니다. 구독 소유자가 아닌 경우, 구독 소유자가 사용 약관에 미리 동의하도록 합니다.

이 단계는 구독당 한 번만 수행하면 됩니다.

소프트웨어 사용 약관에 미리 동의하려면 다음을 수행합니다.

1. Azure Portal에서 또는 <https://shell.azure.com>으로 이동하여 클라우드 셸을 엽니다. 구독 ID로 로그인합니다.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. 다음 명령을 실행하여 서비스 약관에 동의하고, Avere vFXT for Azure 소프트웨어 이미지에 프로그래밍 방식으로 액세스하도록 설정합니다.

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>가상 네트워크에서 스토리지 서비스 엔드포인트 만들기(필요한 경우)

[서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)는 Azure Blob 트래픽을 가상 네트워크 외부에서 라우팅하는 대신 로컬로 유지합니다. 백 엔드 데이터 스토리지에 Azure Blob을 사용하는 모든 Avere vFXT for Azure 클러스터에 권장됩니다.

클러스터를 만드는 동안 새 가상 네트워크를 만들면 엔드포인트가 자동으로 만들어집니다. 기존의 가상 네트워크를 제공할 때 클러스터를 만드는 동안 새 Blob Storage 컨테이너를 만들려는 경우 Microsoft 스토리지 서비스 엔드포인트가 있어야 합니다.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* 클러스터 만들기의 일부로 새 가상 네트워크를 만드는 경우 이 단계를 건너뜁니다.
>* 클러스터를 만드는 동안 Blob Storage를 만들지 않는 경우 엔드포인트는 선택 사항입니다. 이 경우 나중에 Azure Blob을 사용하기로 결정한 경우 서비스 엔드포인트를 만들 수 있습니다.

Azure Portal에서 스토리지 서비스 엔드포인트를 만듭니다.

1. 포털에서 가상 네트워크 목록을 엽니다.
1. 클러스터의 가상 네트워크를 선택합니다.
1. 왼쪽 메뉴에서 **서비스 엔드포인트** 를 클릭합니다.
1. 위쪽에서 **추가** 를 클릭합니다.
1. 서비스 ``Microsoft.Storage``를 선택합니다.
1. 클러스터 서브넷을 선택합니다.
1. 아래쪽에서 **추가** 를 클릭합니다.

   ![서비스 엔드포인트를 만드는 단계에 대한 주석이 있는 Azure Portal 스크린샷](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>다음 단계

이러한 사전 요구 사항을 완료한 후 클러스터를 만들 수 있습니다. 지침은 [vFXT 클러스터 배포](avere-vfxt-deploy.md)를 참조하세요.