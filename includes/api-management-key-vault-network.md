---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: 86b5a62d7aaf16009e59637ebe9a967fce1e0358
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128569619"
---
#### <a name="requirements-for-key-vault-firewall"></a>Key Vault 방화벽에 대한 요구 사항

키 자격 증명 모음에서 [Key Vault 방화벽](../articles/key-vault/general/network-security.md)을 사용하도록 설정한 경우 추가 요구 사항은 다음과 같습니다.

* 키 자격 증명 모음에 액세스하려면 API Management 인스턴스의 **시스템 할당** 관리 ID를 사용해야 합니다.
* Key Vault 방화벽에서 **신뢰할 수 있는 Microsoft 서비스가 이 방화벽을 우회하도록 허용** 옵션을 사용하도록 설정합니다.

#### <a name="virtual-network-requirements"></a>가상 네트워크 요구 사항

API Management 인스턴스가 가상 네트워크에 배포된 경우 다음 네트워크 설정도 구성합니다.

* API Management 서브넷에서 Azure Key Vault에 대한 [서비스 엔드포인트](../articles/key-vault/general/overview-vnet-service-endpoints.md)를 사용하도록 설정합니다.
* AzureKeyVault 및 AzureActiveDirectory [서비스 태그](../articles/virtual-network/service-tags-overview.md)에 대한 아웃바운드 트래픽을 허용하도록 NSG(네트워크 보안 그룹) 규칙을 구성합니다. 

자세한 내용은 [가상 네트워크에 연결](../articles/api-management/api-management-using-with-vnet.md#network-configuration-issues)의 네트워크 구성 정보를 참조하세요.