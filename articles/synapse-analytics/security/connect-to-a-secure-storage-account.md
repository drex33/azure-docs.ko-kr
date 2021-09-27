---
title: Azure Synapse 작업 영역에서 보안 스토리지 계정에 연결
description: 이 문서에서는 Azure Synapse 작업 영역에서 보안 스토리지 계정에 연결하는 방법을 설명합니다.
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: seshin
ms.reviewer: jrasnick
ms.openlocfilehash: 103aaa913511c8d61e8e2a28ede81973fca98d1a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774648"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>Azure Synapse 작업 영역에서 보안 스토리지 계정에 연결

이 문서에서는 Azure Synapse 작업 영역에서 보안 Azure Storage 계정에 연결하는 방법을 설명합니다. 작업 영역을 만들 때 Azure Storage 계정을 Synapse 작업 영역에 연결할 수 있습니다. 작업 영역을 만든 후 더 많은 스토리지 계정을 연결할 수 있습니다.


## <a name="secured-azure-storage-accounts"></a>보안 Azure Storage 계정
Azure Storage는 스토리지 계정에 대한 액세스를 보호하고 제어하는 데 사용할 수 있는 계층화된 보안 모델을 제공합니다. 선택한 공용 IP 주소 범위에서 스토리지 계정에 대한 액세스를 허용하도록 IP 방화벽 규칙을 구성할 수 있습니다. 선택한 가상 네트워크에서 스토리지 계정에 대한 액세스를 허용하도록 네트워크 규칙을 구성할 수도 있습니다. 선택한 IP 주소 범위에서 액세스를 허용하는 IP 방화벽 규칙과 동일한 스토리지 계정에서 선택한 가상 네트워크의 액세스를 허용하는 네트워크 규칙을 결합할 수 있습니다. 스토리지 방화벽 규칙은 스토리지 계정의 퍼블릭 엔드포인트에 적용됩니다. 작업 영역에서 생성된 관리형 프라이빗 엔드포인트에서 스토리지 계정으로의 트래픽을 허용하는 액세스 규칙은 필요하지 않습니다. 스토리지 방화벽 규칙은 기존 스토리지 계정에 적용하거나 새 스토리지 계정을 만들 때 적용할 수 있습니다. 스토리지 계정을 보호하는 방법에 대한 자세한 내용은 [여기](../../storage/common/storage-network-security.md)를 참조하세요.

## <a name="synapse-workspaces-and-virtual-networks"></a>Synapse 작업 영역 및 가상 네트워크
Synapse 작업 영역을 만들 때 관리형 가상 네트워크를 연결할 수 있도록 선택할 수 있습니다. 작업 영역을 만들 때 작업 영역에 대해 관리형 가상 네트워크를 사용하도록 설정하지 않으면 작업 영역이 연결된 관리형 가상 네트워크가 없는 Synapse 작업 영역과 함께 공유 가상 네트워크에 있게 됩니다. 작업 영역을 만들 때 관리형 가상 네트워크를 사용하도록 설정한 경우 작업 영역은 Azure Synapse에서 관리형 전용 가상 네트워크와 연결됩니다. 이러한 가상 네트워크는 고객 구독에서 생성되지 않습니다. 따라서 위에서 설명한 네트워크 규칙을 사용하여 이러한 가상 네트워크의 트래픽이 보안 스토리지 계정에 액세스하도록 허용할 수 없습니다.  

## <a name="access-a-secured-storage-account"></a>보안 스토리지 계정에 액세스
Synapse는 네트워크 규칙에 포함할 수 없는 네트워크에서 작동합니다. 작업 영역에서 보안 스토리지 계정에 액세스할 수 있도록 하려면 다음을 수행해야 합니다.

* 연결 된 관리 되는 가상 네트워크를 사용 하 여 Azure Synapse 작업 영역을 만들고,이 작업 영역에서 보안 저장소 계정으로 관리 되는 개인 끝점을 만듭니다. 

    Azure Portal을 사용 하 여 작업 영역을 만드는 경우 아래와 같이 **네트워킹** 탭에서 관리 되는 가상 네트워크를 사용 하도록 설정할 수 있습니다. 관리 되는 가상 네트워크를 사용 하도록 설정 하거나 Synapse에서 기본 저장소 계정이 보안 저장소 계정인 것으로 확인 되 면 아래와 같이 보안 저장소 계정에 대 한 관리 되는 개인 끝점 연결 요청을 만드는 옵션을 사용할 수 있습니다. 저장소 계정 소유자가 연결 요청을 승인 하 여 개인 링크를 설정 해야 합니다. 또는 작업 영역에서 Apache Spark 풀을 만드는 사용자에 게 연결 요청을 승인할 수 있는 권한이 있는 경우 Synapse에서이 연결 요청을 승인 합니다.
![관리 되는 VNet 및 관리 되는 개인 끝점 사용](./media/connect-to-a-secure-storage-account/enable-managed-virtual-network-managed-private-endpoint.png) 
    


* 신뢰할 수 있는 Azure 서비스로 보안 스토리지 계정에 대한 Azure Synapse 작업 영역 액세스 권한을 부여합니다. 신뢰할 수 있는 서비스로서 Azure Synapse는 강력한 인증을 사용하여 스토리지 계정에 안전하게 연결합니다.   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>관리형 가상 네트워크를 사용하여 Synapse 작업 영역을 만들고 스토리지 계정에 대한 관리형 프라이빗 엔드포인트 만들기
[이러한 단계](./synapse-workspace-managed-vnet.md)를 수행하여 연결된 관리형 가상 네트워크를 포함하는 Synapse 작업 영역을 만들 수 있습니다. 연관된 관리형 가상 네트워크가 있는 작업 영역이 작성되면 [여기](./how-to-create-managed-private-endpoints.md)에 나열된 단계에 따라 보안 스토리지 계정에 대한 관리형 프라이빗 엔드포인트를 만들 수 있습니다. 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>신뢰할 수 있는 Azure 서비스로 보안 스토리지 계정에 대한 Azure Synapse 작업 영역 액세스 권한 부여
전용 SQL 풀 및 서버리스 SQL 풀과 같은 분석 기능은 관리형 가상 네트워크에 배포되지 않은 다중 테넌트 인프라를 사용합니다. 이러한 기능의 트래픽이 보안 스토리지 계정에 액세스하려면 다음 단계에 따라 작업 영역의 시스템 할당 관리 ID를 기반으로 스토리지 계정에 대한 액세스를 구성해야 합니다.

Azure Portal에서 보안 스토리지 계정으로 이동합니다. 왼쪽 탐색 창에서 **네트워킹** 을 선택합니다. **리소스 인스턴스** 섹션에서 **리소스 유형** 으로 *Microsoft.Synapse/workspaces* 를 선택하고 **인스턴스 이름** 에 작업 영역 이름을 입력합니다. **저장** 을 선택합니다.

![스토리지 계정 네트워크 구성입니다.](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

이제 작업 영역에서 보안 스토리지 계정에 액세스할 수 있습니다.


## <a name="next-steps"></a>다음 단계

[관리형 작업 영역 가상 네트워크](./synapse-workspace-managed-vnet.md)에 대한 자세히 알아봅니다.

[관리형 프라이빗 엔드포인트](./synapse-workspace-managed-private-endpoints.md)에 대해 자세히 알아봅니다.