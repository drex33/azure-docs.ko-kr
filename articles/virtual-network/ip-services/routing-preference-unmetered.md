---
title: Azure의 무제한 라우팅 기본 설정
description: CDN 공급자에게 데이터를 송신하는 리소스에 대한 라우팅 기본 설정을 구성하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: 5d459c4a61518aae349184773561448148877afc
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129373033"
---
# <a name="what-is-routing-preference-unmetered"></a>무제한 라우팅 기본 설정이란?

무제한 라우팅 기본 설정은 Azure에서 고객의 원본 콘텐츠를 호스트하는 CDN(Content Delivery Network) 공급자가 사용할 수 있습니다. 이 서비스를 사용하면 CDN 공급자는 다양한 위치에서 Microsoft 글로벌 네트워크 에지 라우터와 직접 피어링 연결을 설정할 수 있습니다.

![무제한 라우팅 기본 설정](./media/routing-preference-unmetered/unmetered.png)

Azure의 원본에서 CDN 공급자에게 송신하는 네트워크 트래픽을 이용하여 직접 연결의 이점을 경험할 수 있습니다.
* 이러한 직접 링크를 통해 라우팅되는 Azure 리소스에서 송신되는 트래픽에 대한 데이터 전송 청구 금액은 무료입니다.
* CDN 공급자와 Azure 원본 간의 직접 연결은 사이에 홉이 없기 때문에 최적의 성능을 제공합니다. 이는 원본에서 데이터를 자주 가져오는 CDN 워크로드에 유용합니다.

## <a name="configuring-routing-preference-unmetered"></a>무제한 라우팅 기본 설정 구성

무제한 라우팅 기본 설정을 활용하려면 CDN 공급자가 이 프로그램의 일부가 되어야 합니다. CDN 공급자가 프로그램에 포함되지 않은 경우 CDN 공급자에게 문의하세요.

다음으로 리소스에 대한 라우팅 기본 설정을 구성하고 라우팅 기본 설정 유형을 **인터넷** 으로 설정합니다. 공용 IP 주소를 만드는 동안 라우팅 기본 설정을 구성한 다음 공용 IP를 가상 머신, 인터넷 연결 부하 분산 장치 등과 같은 리소스에 연결할 수 있습니다. [Azure Portal을 이용하여 공용 IP 주소에 대한 라우팅 기본 설정 구성 방법에 대해 알아봅니다.](../../virtual-network/routing-preference-portal.md)

또한 스토리지 계정에 대한 라우팅 기본 설정을 사용하도록 설정하고 스토리지 원본에서 데이터를 가져오기 위해 CDN 공급자가 사용해야 하는 두 번째 엔드포인트를 게시할 수 있습니다. 예를 들어 스토리지 계정 *StorageAccountA* 에 대한 인터넷 경로별 엔드포인트를 게시하면 다음과 같이 스토리지 서비스의 두 번째 엔드포인트가 게시됩니다.

![스토리지 계정에 대한 라우팅 기본 설정](./media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>다음 단계

* [Azure PowerShell을 사용하여 VM에 대한 라우팅 기본 설정 구성](../../virtual-network/configure-routing-preference-virtual-machine-powershell.md)
* [Azure CLI를 사용하여 VM에 대한 라우팅 기본 설정 구성](../../virtual-network/configure-routing-preference-virtual-machine-cli.md)
* [스토리지 계정에 대한 라우팅 기본 설정 구성](../../storage/common/network-routing-preference.md)