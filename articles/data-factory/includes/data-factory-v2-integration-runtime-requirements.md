---
author: jianleishen
ms.service: data-factory
ms.topic: include
ms.date: 09/29/2021
ms.author: jianleishen
ms.openlocfilehash: a5fbf3071c134b52fb053afc88f6efb51a5ea2f8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129725467"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime setup from connector articles.
-->
데이터 저장소가 온-프레미스 네트워크, Azure 가상 네트워크 또는 Amazon Virtual Private Cloud 내에 있는 경우 [자체 호스팅된 통합 런타임](../create-self-hosted-integration-runtime.md)을 구성하여 연결해야 합니다.

데이터 저장소가 관리형 클라우드 데이터 서비스인 경우 Azure Integration Runtime을 사용할 수 있습니다. 액세스가 방화벽 규칙에서 승인된 IP로 제한되는 경우 허용 목록에 [Azure Integration Runtime IP](../azure-integration-runtime-ip-addresses.md)를 추가할 수 있습니다. 

또한 Azure Data Factory의 [관리형 가상 네트워크 통합 런타임](../tutorial-managed-virtual-network-on-premise-sql-server.md) 기능을 사용하면 자체 호스팅 통합 런타임을 설치하고 구성하지 않고도 온-프레미스 네트워크에 액세스할 수 있습니다.

Data Factory에서 지원하는 네트워크 보안 메커니즘 및 옵션에 대한 자세한 내용은 [데이터 액세스 전략](../data-access-strategies.md)을 참조하세요.
