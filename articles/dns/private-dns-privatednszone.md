---
title: Azure DNS 프라이빗 영역이란?
description: 프라이빗 DNS 영역 개요
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 3a8ec174cb1be02389487c442b4e1b3ff35dbf27
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536686"
---
# <a name="what-is-a-private-azure-dns-zone"></a>프라이빗 Azure DNS 영역이란?

Azure 프라이빗 DNS는 사용자 지정 DNS 솔루션을 추가하지 않고도 가상 네트워크의 도메인 이름을 관리하고 확인할 수 있는 안정적이고 신뢰할 수 있는 DNS 서비스를 제공합니다. 프라이빗 DNS 영역을 사용하면 현재 Azure에서 제공하는 이름 대신 사용자 고유의 사용자 지정 도메인 이름을 사용할 수 있습니다. 

프라이빗 DNS 영역에 포함된 레코드는 인터넷에서 확인할 수 없습니다. 프라이빗 DNS 영역에 대한 DNS 확인은 연결된 가상 네트워크에서만 작동합니다.

[가상 네트워크 링크](./private-dns-virtual-network-links.md)를 만들어 프라이빗 DNS 영역을 하나 이상의 가상 네트워크에 연결할 수 있습니다.
[자동 등록](./private-dns-autoregistration.md) 기능을 사용하도록 설정하여 가상 네트워크에 배포된 가상 머신에 대한 DNS 레코드의 수명 주기를 자동으로 관리할 수도 있습니다.

## <a name="limits"></a>제한

구독에서 만들 수 있는 프라이빗 DNS 영역 수와 프라이빗 DNS 영역에서 지원되는 레코드 집합 수를 이해하려면 [Azure DNS 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits)을 참조하세요.

## <a name="restrictions"></a>제한

* 레이블이 지정된 단일 프라이빗 DNS 영역은 지원되지 않습니다. 프라이빗 DNS 영역에는 두 개 이상의 레이블이 있어야 합니다. 예를 들어 contoso.com에는 점으로 구분된 두 개의 레이블이 있습니다. 프라이빗 DNS 영역에는 최대 34개의 레이블이 있을 수 있습니다.
* 프라이빗 DNS 영역에는 영역 위임(NS 레코드)을 만들 수 없습니다. 자식 도메인을 사용하려는 경우 도메인을 직접 프라이빗 DNS 영역으로 생성할 수 있습니다. 그런 다음, 부모 영역에서 nameserver 위임을 설정하지 않고 가상 네트워크에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure PowerShell](./private-dns-getstarted-powershell.md) 또는 [Azure CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 프라이빗 영역을 만드는 방법을 알아봅니다.

* Azure DNS에서 프라이빗 영역으로 실현할 수 있는 몇 가지 일반 [프라이빗 영역 시나리오](./private-dns-scenarios.md)에 대해 읽어보세요.

* Azure DNS의 프라이빗 영역에 대한 일반적인 질문과 답변은 [프라이빗 DNS FAQ](./dns-faq-private.yml)를 참조하세요.
