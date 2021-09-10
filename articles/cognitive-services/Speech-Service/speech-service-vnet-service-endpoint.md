---
title: Speech service를 통해 가상 네트워크 서비스 엔드포인트 사용
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Virtual Network 서비스 엔드포인트에서 Speech Service를 사용하는 방법을 설명합니다.
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 903ce0888aa04e7005468ca4f8aec9cf977ead5d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114490217"
---
# <a name="use-speech-service-through-a-virtual-network-service-endpoint"></a>Virtual Network 서비스 엔드포인트를 통해 Speech Service 사용

[Azure Virtual Network](../../virtual-network/virtual-networks-overview.md) [서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)는 Azure 백본 네트워크에서 최적화된 경로를 통해 Azure 서비스에 대한 안전한 직접 연결을 제공합니다. 엔드포인트를 사용하면 가상 네트워크에 대해 중요한 Azure 서비스 리소스를 보호할 수 있습니다. 서비스 엔드포인트를 통해 가상 네트워크의 개인 IP 주소는 가상 네트워크에 공용 IP 주소가 없어도 Azure 서비스의 엔드포인트에 연결할 수 있습니다.

이 문서에서는 Azure Cognitive Services에서 Speech Services를 사용하여 가상 네트워크 서비스 엔드포인트를 설정하고 사용하는 방법을 설명합니다.

> [!NOTE]
> 시작하기 전에, [Cognitive Services에서 가상 네트워크를 사용하는 방법](../cognitive-services-virtual-networks.md)을 검토합니다.

이 문서에서는 [나중에 가상 네트워크 서비스 엔드포인트를 제거하지만 여전히 Speech 리소스를 사용하는 방법](#use-a-speech-resource-that-has-a-custom-domain-name-but-that-doesnt-have-allowed-virtual-networks)을 설명합니다.

Virtual Network 서비스 엔드포인트 시나리오에 대한 Speech 리소스를 설정하려면 다음을 수행합니다.
1. [Speech 리소스에 대한 사용자 지정 도메인 이름을 생성합니다](#create-a-custom-domain-name).
1. [Speech 리소스에 대한 가상 네트워크 및 네트워킹 설정을 구성합니다](#configure-virtual-networks-and-the-speech-resource-networking-settings).
1. [기존 애플리케이션 및 솔루션 조정](#adjust-existing-applications-and-solutions).

> [!NOTE]
> Speech Service에 Virtual Network 서비스 엔드포인트를 설정하고 사용하는 것은 프라이빗 엔드포인트를 설정하고 사용하는 것과 비슷합니다. 이 문서에서는 프로시저가 동일한 경우, [프라이빗 엔드포인트 사용에 대한 문서](speech-services-private-link.md)의 해당 섹션을 참조합니다.

[!INCLUDE [](includes/speech-vnet-service-enpoints-private-endpoints.md)]

이 문서에서는 Speech Service에서 Virtual Network 서비스 엔드포인트를 사용하는 방법을 설명합니다. 프라이빗 엔드포인트에 대한 자세한 내용은 [프라이빗 엔드포인트를 통해 Speech Service 사용](speech-services-private-link.md)을 참조하세요.

## <a name="create-a-custom-domain-name"></a>사용자 지정 도메인 이름 만들기

가상 네트워크 서비스 엔드포인트에는 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](../cognitive-services-custom-subdomains.md)이 필요합니다. 프라이빗 엔드포인트 문서의 [지침](speech-services-private-link.md#create-a-custom-domain-name)에 따라 사용자 지정 도메인을 생성합니다. 섹션의 모든 경고는 Virtual Network 서비스 엔드포인트에도 적용됩니다.

## <a name="configure-virtual-networks-and-the-speech-resource-networking-settings"></a>가상 네트워크 및 Speech 리소스 네트워킹 설정 구성

서비스 엔드포인트를 통해 액세스가 허용되는 모든 가상 네트워크를 Speech 리소스 네트워킹 속성에 추가해야 합니다.

> [!NOTE]
> Virtual Network 서비스 엔드포인트를 통해 Speech 리소스에 액세스하려면, 가상 네트워크의 필수 서브넷에 대해 `Microsoft.CognitiveServices` 서비스 엔드포인트 유형을 사용하도록 설정해야 합니다. 이렇게 하면 프라이빗 백본 네트워크를 통해 Cognitive Services 관련된 모든 서브넷 트래픽을 라우팅합니다. 동일한 서브넷에서 다른 Cognitive Services 리소스에 액세스하려는 경우, 이러한 리소스가 가상 네트워크를 허용하도록 구성되어 있는지 확인합니다. 
>
> 가상 네트워크가 음성 리소스 네트워킹 속성에 *허용되는* 대로 추가되지 않으면, 서비스 엔드포인트가 가상 네트워크에 대해 사용하도록 설정되어 있더라도 `Microsoft.CognitiveServices` 서비스 엔드포인트를 통해 Speech 리소스에 액세스할 수 없습니다. 또한, 서비스 엔드포인트를 사용하도록 설정했지만 가상 네트워크가 허용되지 않는 경우, Speech 리소스의 다른 네트워크 보안 설정에 관계없이 공용 IP 주소를 통해 가상 네트워크에 Speech 리소스에 액세스할 수 없습니다. 이는 `Microsoft.CognitiveServices` 엔드포인트를 사용하도록 설정하면 프라이빗 백본 네트워크를 통해 Cognitive Services 관련된 모든 트래픽을 라우팅하고, 이 경우 가상 네트워크가 리소스에 명시적으로 액세스할 수 있도록 허용되어야 하기 때문입니다. 이 지침은 Speech 리소스뿐만 아니라, 모든 Cognitive Services 리소스에 적용됩니다.  
  
1. [Azure Portal](https://portal.azure.com/)로 이동하여 Azure 계정에 로그인합니다.
1. Speech 리소스를 선택합니다.
1. 왼쪽 창의 **리소스 관리** 그룹에서 **네트워킹** 을 선택합니다.
1. **방화벽 및 가상 네트워크** 탭에서 **선택한 네트워크 및 프라이빗 엔드포인트** 를 선택합니다. 

   > [!NOTE]
   > Virtual Network 서비스 엔드포인트를 사용하려면, **선택한 네트워크 및 프라이빗 엔드포인트** 네트워크 보안 옵션을 선택해야 합니다. 다른 옵션은 지원되지 않습니다. 시나리오에 **모든 네트워크** 옵션이 필요한 경우, 세 가지 네트워크 보안 옵션을 모두 지원하는 [프라이빗 엔드포인트](speech-services-private-link.md)를 사용하는 것이 좋습니다.

5. **기존 가상 네트워크 추가** 또는 **신규 가상 네트워크 추가** 를 선택하여 필요한 매개 변수를 제공합니다. 기존 가상 네트워크의 경우 **추가** 를 선택하고, 새 가상 네트워크의 경우 **생성** 을 선택합니다. 기존 가상 네트워크를 추가하면, 선택한 서브넷에 대해 `Microsoft.CognitiveServices` 서비스 엔드포인트가 자동으로 활성화됩니다. 이 작업은 최대 15분이 걸릴 수 있습니다. 또한 이 섹션의 시작 부분에 있는 참고 사항도 참조하세요.

### <a name="enabling-service-endpoint-for-an-existing-virtual-network"></a>기존 가상 네트워크에 대한 서비스 엔드포인트 사용 설정 

이전 섹션에서 설명한 대로, Speech 리소스에 *허용되는* 대로 가상 네트워크를 구성하면 `Microsoft.CognitiveServices` 서비스 엔드포인트가 자동으로 활성화됩니다. 나중에 비활성화 하는 경우, Speech 리소스(및 다른 Cognitive Services 리소스)에 대한 서비스 엔드포인트 액세스를 복원하기 위해 수동으로 다시 활성화해야 합니다.

1. [Azure Portal](https://portal.azure.com/)로 이동하여 Azure 계정에 로그인합니다.
1. 가상 네트워크를 선택합니다.
1. 왼쪽 창의 **설정** 그룹에서 **서브넷** 을 선택합니다.
1. 필요한 서브넷을 선택합니다.
1. 윈도우의 오른쪽에 새 패널이 나타납니다. **서비스 엔드포인트** 섹션의 이 패널에서 **서비스** 목록에서 `Microsoft.CognitiveServices`을 선택합니다.
1. **저장** 을 선택합니다.

## <a name="adjust-existing-applications-and-solutions"></a>기존 애플리케이션 및 솔루션 조정

활성화된 사용자 지정 도메인을 사용하는 Speech 리소스는 다른 방식으로 Speech Services와 상호 작용합니다. 이는 서비스 엔드포인트가 구성되었는지 여부에 관계없이, 사용자 지정 도메인 사용 Speech 리소스에 적용됩니다. 이 섹션의 정보는 두 시나리오에 모두 적용됩니다.

### <a name="use-a-speech-resource-that-has-a-custom-domain-name-and-allowed-virtual-networks"></a>사용자 지정 도메인 이름 및 허용된 가상 네트워크가 있는 Speech 리소스 사용 

이 시나리오에서는 Speech 리소스의 네트워킹 설정에서 **선택한 네트워크 및 프라이빗 엔드포인트** 옵션이 선택되고 하나 이상의 가상 네트워크가 허용됩니다. 이 시나리오는 [사용자 지정 도메인 이름과 프라이빗 엔드포인트가 사용하도록 설정된 Speech 리소스 사용](speech-services-private-link.md#adjust-an-application-to-use-a-speech-resource-with-a-private-endpoint)과 같습니다.


### <a name="use-a-speech-resource-that-has-a-custom-domain-name-but-that-doesnt-have-allowed-virtual-networks"></a>사용자 지정 도메인 이름이 있지만 가상 네트워크가 허용되지 않는 Speech 리소스 사용

이 시나리오에서는 프라이빗 엔드포인트가 사용하도록 설정되지 않았으며 다음 설명 중 하나는 참입니다.

- **선택한 네트워크 및 프라이빗 엔드포인트** 옵션은 Speech 리소스의 네트워킹 설정에서 선택되지만 허용되는 가상 네트워크는 구성되지 않습니다.
- **모든 네트워크** 옵션은 Speech 리소스의 네트워킹 설정에서 선택됩니다.

이 시나리오는 [사용자 지정 도메인 이름이 있고 프라이빗 엔드포인트가 없는 Speech 리소스 사용](speech-services-private-link.md#adjust-an-application-to-use-a-speech-resource-without-private-endpoints)과 같습니다.


[!INCLUDE [](includes/speech-vnet-service-enpoints-private-endpoints-simultaneously.md)]


## <a name="learn-more"></a>자세한 정보

* [프라이빗 엔드포인트를 통해 Speech Service 사용](speech-services-private-link.md)
* [Azure Virtual Network 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)
* [Azure Private Link](../../private-link/private-link-overview.md)
* [Speech SDK](speech-sdk.md)
* [Speech-to-Text REST API](rest-speech-to-text.md)
* [Text-to-Speech REST API](rest-text-to-speech.md)
