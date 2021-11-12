---
title: Microsoft Defender for Cloud를 사용하여 레지스트리 이미지 검사
description: 컨테이너 레지스트리용 Microsoft Defender를 사용하여 Azure 컨테이너 레지스트리의 이미지를 검사하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 05/19/2021
ms.openlocfilehash: 0170895ede2be40cc57d9ecfd82806e1ee3a617c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323489"
---
# <a name="scan-registry-images-with-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud를 사용하여 레지스트리 이미지 검사

Azure 컨테이너 레지스트리의 이미지에서 취약성을 검사하려면 사용 가능한 Azure Marketplace 솔루션 중 하나를 통합하거나, 클라우드용 Microsoft Defender를 사용하려는 경우 필요에 따라 구독 수준에서 **컨테이너 레지스트리용 Microsoft Defender를** 사용하도록 설정할 수 있습니다. 

* [컨테이너 레지스트리용 Microsoft Defender에 대해](../security-center/defender-for-container-registries-introduction.md) 자세히 알아보기
* [클라우드용 Microsoft Defender의 컨테이너 보안에 대해](../security-center/container-security.md) 자세히 알아보기

## <a name="registry-operations-by-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud의 레지스트리 작업

Microsoft Defender for Cloud는 레지스트리에 푸시되거나 레지스트리로 가져온 이미지 또는 지난 30일 이내에 끌어온 이미지를 검사합니다. 취약성이 검색되면 [권장되는 수정이](../security-center/defender-for-container-registries-usage.md#view-and-remediate-findings) Microsoft Defender for Cloud에 표시됩니다.

 보안 문제를 수정하는 데 권장되는 단계를 수행한 후 레지스트리의 이미지를 바꿉니다. Microsoft Defender for Cloud는 이미지를 다시 검색하여 취약성이 수정되어 있는지 확인합니다. 

자세한 내용은 [컨테이너 레지스트리에 Microsoft Defender 사용을 참조하세요.](../security-center/defender-for-container-registries-usage.md)

> [!TIP]
> Microsoft Defender for Cloud는 레지스트리를 인증하여 취약성 검사를 위해 이미지를 끌어올 수 있습니다. 레지스트리에 대한 [리소스 로그가](monitor-service-reference.md#resource-logs) 수집되면 Microsoft Defender for Cloud에서 생성된 레지스트리 로그인 이벤트 및 이미지 끌어오기 이벤트가 표시됩니다. 이 이벤트는 `b21cb118-5a59-4628-bab0-3c3f0e434cg6`과 같은 영숫자 ID와 연결됩니다.

## <a name="scanning-a-network-restricted-registry"></a>네트워크 제한 레지스트리 검사

Microsoft Defender for Cloud는 공개적으로 액세스할 수 있는 컨테이너 레지스트리 또는 네트워크 액세스 규칙으로 보호되는 컨테이너 레지스트리의 이미지를 검색할 수 있습니다. 네트워크 규칙이 구성된 경우(즉, 퍼블릭 레지스트리 액세스를 사용하지 않도록 설정하거나, IP 액세스 규칙을 구성하거나, 프라이빗 엔드포인트를 만드는 경우) 레지스트리에 액세스하도록 [**신뢰할 수 있는 Microsoft 서비스를 허용**](allow-access-trusted-services.md)하려면 네트워크 설정을 사용하도록 설정해야 합니다. 기본적으로 이 설정은 새 컨테이너 레지스트리에서 사용하도록 설정됩니다.

## <a name="next-steps"></a>다음 단계

* [신뢰할 수 있는 서비스](allow-access-trusted-services.md)의 레지스트리 액세스에 관해 자세히 알아보세요.
* 가상 네트워크에서 프라이빗 엔드포인트를 사용하여 레지스트리에 대한 액세스를 제한하려면 [Azure 컨테이너에 대한 Azure Private Link 구성](container-registry-private-link.md)을 참조하세요.
* 레지스트리 방화벽 규칙을 설정하려면 [공개 IP 네트워크 규칙 구성](container-registry-access-selected-networks.md)을 참조하세요.
