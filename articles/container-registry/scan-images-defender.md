---
title: Azure Defender를 사용하여 레지스트리 이미지 검사
description: 컨테이너 레지스트리에서 Azure Defender를 사용하여 Azure 컨테이너 레지스트리에서 이미지를 검사하는 방법에 관해 알아봅니다.
ms.topic: article
ms.date: 05/19/2021
ms.openlocfilehash: d00e23ffa7e3bd2fc1084ba6253274d14031d624
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113553065"
---
# <a name="scan-registry-images-with-azure-defender"></a>Azure Defender를 사용하여 레지스트리 이미지 검사

Azure 컨테이너 레지스트리에서 취약성이 있는지 이미지를 검사하려면 사용 가능한 Azure Marketplace 솔루션 중 하나를 통합하거나, Azure Security Center를 사용하려면 필요에 따라 구독 수준에서 **컨테이너 레지스트리에서 Azure Defender** 를 사용하도록 설정할 수 있습니다. 

* [컨테이너 레지스트리에서 Azure Defender 사용](../security-center/defender-for-container-registries-introduction.md)에 관한 자세한 정보
* [Azure Security Center의 컨테이너 보안](../security-center/container-security.md)에 관한 자세한 정보

## <a name="registry-operations-by-azure-defender"></a>Azure Defender의 레지스트리 작업

Azure Defender는 레지스트리에 푸시되거나, 레지스트리로 가져온 이미지 또는 최근 30일 이내에 끌어온 모든 이미지를 검사합니다. 취약성이 검색되면 [권장되는 수정 사항](../security-center/defender-for-container-registries-usage.md#view-and-remediate-findings)이 Azure Security Center에 표시됩니다.

 보안 문제를 수정하는 데 권장되는 단계를 수행한 후 레지스트리의 이미지를 바꿉니다. Azure Defender는 이미지를 다시 검사하여 취약성이 수정되었는지 확인합니다. 

자세한 내용은 [컨테이너 레지스트리에서 Azure Defender 사용](../security-center/defender-for-container-registries-usage.md)을 참조하세요.

> [!TIP]
> Azure Defender는 취약성 검사할 이미지를 끌어오기 위해 레지스트리에서 인증합니다. 레지스트리의 [리소스 로그](monitor-service-reference.md#resource-logs)가 수집되면 Azure Defender에서 생성한 레지스트리 로그인 이벤트와 이미지 끌어오기 이벤트가 표시됩니다. 이 이벤트는 `b21cb118-5a59-4628-bab0-3c3f0e434cg6`과 같은 영숫자 ID와 연결됩니다.

## <a name="scanning-a-network-restricted-registry"></a>네트워크 제한 레지스트리 검사

Azure Defender는 공개적으로 액세스할 수 있는 컨테이너 레지스트리의 이미지 또는 네트워크 액세스 규칙으로 보호되는 이미지를 검사할 수 있습니다. 네트워크 규칙이 구성된 경우(즉, 퍼블릭 레지스트리 액세스를 사용하지 않도록 설정하거나, IP 액세스 규칙을 구성하거나, 프라이빗 엔드포인트를 만드는 경우) 레지스트리에 액세스하도록 [**신뢰할 수 있는 Microsoft 서비스를 허용**](allow-access-trusted-services.md)하려면 네트워크 설정을 사용하도록 설정해야 합니다. 기본적으로 이 설정은 새 컨테이너 레지스트리에서 사용하도록 설정됩니다.

## <a name="next-steps"></a>다음 단계

* [신뢰할 수 있는 서비스](allow-access-trusted-services.md)의 레지스트리 액세스에 관해 자세히 알아보세요.
* 가상 네트워크에서 프라이빗 엔드포인트를 사용하여 레지스트리에 대한 액세스를 제한하려면 [Azure 컨테이너에 대한 Azure Private Link 구성](container-registry-private-link.md)을 참조하세요.
* 레지스트리 방화벽 규칙을 설정하려면 [공개 IP 네트워크 규칙 구성](container-registry-access-selected-networks.md)을 참조하세요.
