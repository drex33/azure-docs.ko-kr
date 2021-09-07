---
title: 가상 네트워크에서 풀 프로비저닝
description: 컴퓨팅 노드가 파일 서버와 같은 네트워크의 다른 VM과 안전하게 통신할 수 있도록 Azure 가상 네트워크에 Batch 풀을 만드는 방법입니다.
ms.topic: how-to
ms.date: 08/20/2021
ms.custom: seodec18
ms.openlocfilehash: bc8f63af713b3b56d85426ce9be86214572506be
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122635133"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>가상 네트워크에서 Azure Batch 만들기

Azure Batch 풀을 만들 때는 지정한 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)(VNet)의 하위 집합에서 풀을 프로비전할 수 있습니다. 이 문서에서는 VNet에서 Batch 풀을 설정하는 방법을 설명합니다.

## <a name="why-use-a-vnet"></a>VNet을 사용하는 이유

풀의 컴퓨팅 노드는 별도의 VNet 없이도 다중 인스턴스 작업을 실행하는 등 서로 통신할 수 있습니다. 그러나 기본적으로 풀의 노드는 라이선스 서버나 파일 서버와 같이 풀에 속하지 않는 가상 머신과는 통신할 수 없습니다.

컴퓨팅 노드가 다른 가상 머신 또는 온-프레미스 네트워크와 안전하게 통신하도록 Azure VNet의 서브넷에서 풀을 프로비저닝할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- **인증**. Azure VNet을 사용하려면 Batch 클라이언트 API가 Azure AD(Active Directory) 인증을 사용해야 합니다. Azure AD에 대한 Azure Batch 지원은 [Active Directory를 사용하여 Batch 서비스 솔루션 인증](batch-aad-auth.md)에 설명되어 있습니다.

- **Azure VNet**. VNet 요구 사항 및 구성에 대한 다음 섹션을 참조하세요. 하나 이상의 서브넷으로 VNet을 미리 준비하기 위해 Azure Portal, Azure PowerShell, Azure CLI(명령줄 인터페이스) 또는 기타 방법을 사용할 수 있습니다.
  - Azure Resource Manager VNet을 만들려면 [가상 네트워크 만들기](../virtual-network/manage-virtual-network.md#create-a-virtual-network)를 참조하세요. Resource Manager 기반 VNet은 새 배포에 권장되며, 가상 머신 구성을 사용하는 풀에서만 지원됩니다.
  - 클래식 VNet을 만들려면 [여러 서브넷이 있는 가상 네트워크(클래식) 만들기](/previous-versions/azure/virtual-network/create-virtual-network-classic)를 참조하세요. 클래식 VNet은 Cloud Services 구성을 사용하는 풀에서만 지원됩니다.

## <a name="vnet-requirements"></a>VNet 요구 사항

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-azure-portal"></a>Azure Portal에서 VNet이 있는 풀 만들기

VNet을 만들고 서브넷을 할당한 후에는 해당 VNet으로 Batch 풀을 만들 수 있습니다. Azure Portal에서 풀을 만들려면 다음 단계를 수행합니다. 

1. Azure Portal에서 Batch 계정으로 이동합니다. 이 계정은 사용할 VNet이 포함된 리소스 그룹과 동일한 구독 및 지역에 있어야 합니다.
1. 왼쪽의 **설정** 창에서 **풀** 메뉴 항목을 선택합니다.
1. **풀** 창에서 **추가** 를 선택합니다.
1. **풀 추가** 창의 **이미지 형식** 드롭다운에서 사용할 옵션을 선택합니다.
1. 사용자 지정 이미지에 대해 올바른 **게시자/제품/Sku** 를 선택합니다.
1. **노드 크기**, **대상 전용 노드** 및 **낮은 우선 순위 노드** 등 나머지 필수 설정과 선택 사항인 설정을 원하는 대로 지정합니다.
1. **가상 네트워크** 에서 사용할 가상 네트워크와 서브넷을 선택합니다.
1. **확인** 을 선택하여 풀을 만듭니다.

> [!IMPORTANT]
> 풀에서 사용 중인 서브넷을 삭제하려고 하면 오류 메시지가 표시됩니다. 서브넷을 사용하는 모든 풀은 해당 서브넷을 삭제하기 전에 삭제해야 합니다.

## <a name="user-defined-routes-for-forced-tunneling"></a>강제 터널링을 위한 사용자 정의 경로

조직에서 검사와 로깅을 위해 서브넷에서 인터넷으로 가는 트래픽을 온-프레미스 위치로 다시 리디렉션(강제)해야 하는 요구 사항이 있을 수 있습니다. 또한 VNet에서 서브넷에 강제 터널링을 사용할 수 있습니다.

강제 터널링을 사용하는 VNet에서 풀의 노드가 작동하도록 해당 서브넷에 대해 다음 UDR([사용자 정의 경로](../virtual-network/virtual-networks-udr-overview.md))을 추가해야 합니다.

- Batch 서비스는 작업 예약을 위해 노드와 통신해야 합니다. 이 통신을 사용하려면 Batch 계정이 존재하는 지역의 Batch 서비스에서 사용하는 각 IP 주소에 대해 UDR을 추가해야 합니다. Batch 서비스의 IP 주소는 `BatchNodeManagement.<region>` 서비스 태그에서 찾을 수 있습니다. IP 주소 목록을 가져오려면 [온-프레미스 서비스 태그](../virtual-network/service-tags-overview.md)를 참조하세요.

- 대상 포트 443의 Azure Batch 서비스에 대한 아웃바운드 TCP 트래픽이 온-프레미스 네트워크에 의해 차단되지 않는지 확인합니다. 이러한 Azure Batch 서비스 대상 IP 주소는 위의 경로에 사용된 `BatchNodeManagement.<region>` 서비스 태그에 있는 것과 동일합니다.

- 대상 포트 443(특히 `*.table.core.windows.net`, `*.queue.core.windows.net`, `*.blob.core.windows.net` 형식의 URL)에서 Azure Storage에 대한 아웃바운드 TCP 트래픽이 온-프레미스 네트워크에 의해 차단되지 않는지 확인합니다.

- 가상 파일 탑재를 사용하는 경우 [네트워킹 요구 사항](virtual-file-mount.md#networking-requirements)을 검토하고 필요한 트래픽이 차단되지 않았는지 확인합니다.

UDR을 추가할 때 관련된 각 Batch IP 주소 접두사에 대한 경로를 정의하고 **다음 홉 유형** 을 **인터넷** 으로 설정합니다.

> [!WARNING]
> Batch 서비스 IP 주소는 시간이 지남에 따라 변경될 수 있습니다. IP 주소 변경으로 인한 가동 중단을 방지하려면 Batch 서비스 IP 주소를 자동으로 새로 고치는 프로세스를 설정하고 경로 테이블을 최신 상태로 유지합니다.

## <a name="next-steps"></a>다음 단계

- 풀, 노드, 작업 및 태스크와 같은 [Batch 서비스 워크플로 및 기본 리소스](batch-service-workflow-features.md)에 대해 알아봅니다.
- [Azure Portal에 사용자 정의 경로 만들기](../virtual-network/tutorial-create-route-table-portal.md) 방법을 알아봅니다.
