---
title: Azure DNS 프라이빗 영역의 가상 네트워크 링크 하위 리소스란?
description: Azure DNS 프라이빗 영역의 가상 네트워크 링크 하위 리소스 개요
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/26/2021
ms.author: rohink
ms.openlocfilehash: fa1729029d7576dac1dab2ea77cfe1c257441b42
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500814"
---
# <a name="what-is-a-virtual-network-link"></a>가상 네트워크 링크란?

Azure에서 프라이빗 DNS 영역을 만든 후에는 가상 네트워크를 연결해야 합니다. 연결되면 해당 가상 네트워크에서 호스트되는 VM이 프라이빗 DNS 영역에 액세스할 수 있습니다. 모든 프라이빗 DNS 영역에는 가상 네트워크 링크 자식 리소스의 컬렉션이 있습니다. 이러한 각 리소스는 가상 네트워크에 대한 연결을 나타냅니다. 가상 네트워크는 등록 또는 확인 가상 네트워크로 프라이빗 DNS 영역에 연결할 수 있습니다.

## <a name="registration-virtual-network"></a>등록 가상 네트워크

프라이빗 DNS 영역과 가상 네트워크 간의 [링크를 만드는](./private-dns-getstarted-portal.md#link-the-virtual-network) 경우 [자동 등록](./private-dns-autoregistration.md)을 사용하도록 설정하는 옵션이 있습니다. 이 설정을 사용하도록 설정하면 가상 네트워크가 프라이빗 DNS 영역에 대한 등록 가상 네트워크가 됩니다. 가상 네트워크에 배포하는 모든 가상 머신에 대해 DNS 레코드가 자동으로 만들어집니다. 가상 네트워크에 이미 배포된 가상 머신에 대해서도 DNS 레코드가 생성됩니다.

가상 네트워크 관점에서 프라이빗 DNS 영역은 해당 가상 네트워크에 대한 등록 영역이 됩니다. 프라이빗 DNS 영역에 여러 등록 가상 네트워크가 있을 수 있습니다. 그러나 모든 가상 네트워크에는 연결된 등록 영역이 하나만 있을 수 있습니다.

## <a name="resolution-virtual-network"></a>확인 가상 네트워크

자동 등록 없이 가상 네트워크를 프라이빗 DNS 영역에 연결하도록 선택하는 경우 가상 네트워크는 확인 가상 네트워크로만 처리됩니다. 이 가상 네트워크에 배포된 가상 머신에 대한 DNS 레코드는 프라이빗 영역에 자동으로 만들어지지 않습니다. 그러나 가상 네트워크에 배포된 가상 머신은 프라이빗 영역의 DNS 레코드를 성공적으로 쿼리할 수 있습니다. 이러한 레코드에는 프라이빗 DNS 영역에 연결된 다른 가상 네트워크에서 수동으로 만든 레코드와 자동으로 등록된 레코드가 포함됩니다.

하나의 프라이빗 DNS 영역에는 확인 가상 네트워크가 여러 개 있을 수 있으며 가상 네트워크에는 연결된 확인 영역이 여러 개 있을 수 있습니다.

## <a name="limits"></a>제한

등록 및 확인 네트워크의 수를 파악하려면 프라이빗 DNS 영역에 연결합니다. [Azure DNS 한도](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)를 참조하세요.

## <a name="other-considerations"></a>기타 고려 사항

* 클래식 배포 모델을 사용하여 배포된 가상 네트워크는 지원되지 않습니다.

* 프라이빗 DNS 영역과 가상 네트워크 사이에는 하나의 링크만 만들 수 있습니다.

* 프라이빗 DNS 영역 아래의 각 가상 네트워크 링크는 프라이빗 DNS 영역의 컨텍스트 내에서 고유한 이름을 가져야 합니다. 다른 프라이빗 DNS 영역에 동일한 이름의 링크가 있을 수 있습니다.

* 가상 네트워크 링크를 만든 후 가상 네트워크 링크 리소스의 "링크 상태" 필드를 확인합니다. 가상 네트워크의 크기에 따라 링크가 작동하고 링크 상태가 *완료* 로 변경되기까지 몇 분 정도 걸릴 수 있습니다.

* 가상 네트워크를 삭제하면 다른 프라이빗 DNS 영역에서 이와 연결된 모든 가상 네트워크 링크와 자동 등록된 DNS 레코드가 자동으로 삭제됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](./private-dns-getstarted-portal.md#link-the-virtual-network)을 사용하여 프라이빗 DNS 영역에 가상 네트워크를 연결하는 방법을 알아봅니다.

* [Azure PowerShell](./private-dns-getstarted-powershell.md) 또는 [Azure CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 프라이빗 영역을 만드는 방법을 알아봅니다.

* Azure DNS에서 프라이빗 영역으로 실현할 수 있는 몇 가지 일반 [프라이빗 영역 시나리오](./private-dns-scenarios.md)에 대해 읽어보세요.

* 특정 종류의 작업에 필요한 특정 동작을 포함하여 Azure DNS의 프라이빗 영역에 대한 일반적인 질문과 대답은 [프라이빗 DNS FAQ](./dns-faq-private.yml)를 참조하세요.
