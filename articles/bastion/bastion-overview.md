---
title: Azure Bastion | Microsoft Docs
description: RDP/SSH 포트를 외부에 노출하지 않고 가상 머신에 안전하고 원활한 RDP/SSH 연결을 제공하는 Azure Bastion에 대해 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: contperf-fy2q1-portal, ignite-fall-2021
ms.openlocfilehash: ea5fb6c17346059ab35467c654a17815e711d213
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087194"
---
# <a name="what-is-azure-bastion"></a>Azure Bastion 정보

Azure Bastion은 브라우저와 Azure Portal을 사용하여 가상 머신에 연결할 수 있도록 배포하는 서비스입니다. Azure Bastion 서비스는 가상 네트워크 내에서 프로비저닝하는 완전 플랫폼 관리형 PaaS 서비스입니다. TLS를 통해 Azure Portal에서 직접 가상 머신에 안전하고 원활한 RDP/SSH 연결을 제공합니다. Azure Bastion을 통해 연결할 때 가상 머신에 공용 IP 주소, 에이전트 또는 특수 클라이언트 소프트웨어가 필요하지 않습니다.

Bastion은 프로비저닝된 가상 네트워크의 모든 VM에 대한 안전한 RDP 및 SSH 연결을 제공합니다. Azure Bastion을 사용하면 가상 머신에서 RDP/SSH를 사용하여 안전한 액세스 권한을 계속 제공하며 RDP/SSH 포트가 외부 환경에 노출되는 상황으로부터 보호합니다.

:::image type="content" source="./media/bastion-overview/architecture.png" alt-text="Azure Bastion 아키텍처를 보여 주는 다이어그램":::

## <a name="key-benefits"></a><a name="key"></a>주요 이점

* **Azure Portal에서 직접 RDP 및 SSH:** 클릭 한 번으로 원활한 환경을 사용하여 Azure Portal에서 직접 RDP 및 SSH 세션으로 이동할 수 있습니다.
* **RDP/SSH를 위한 TLS 및 방화벽 통과를 통한 원격 세션:** Azure Bastion은 로컬 디바이스로 자동 스트리밍되는 HTML5 기반 웹 클라이언트를 사용합니다. 포트 443에서 TLS를 통한 RDP/SSH 세션을 구현하여 회사 방화벽을 안전하게 트래버스하도록 합니다.
* **Azure VM에 공용 IP가 필요하지 않음:** Azure Bastion은 VM에서 개인 IP를 사용하여 Azure VM(가상 머신)에 대한 RDP/SSH 연결을 엽니다. 가상 머신에서 공용 IP가 필요하지 않습니다.
* **NSG(네트워크 보안 그룹)의 간편 관리**: Azure Bastion은 안전한 RDP/SSH 연결을 제공하기 위해 내부적으로 강화된 Azure의 완전 관리형 플랫폼 PaaS 서비스입니다. Azure Bastion 서브넷에 NSG를 적용할 필요가 없습니다. Azure Bastion은 개인 IP를 통해 가상 머신에 연결하므로 Azure Bastion의 RDP/SSH만 허용하도록 NSG를 구성할 수 있습니다. 이렇게 하면 가상 머신에 안전하게 연결하기 위해 매번 NSG를 관리하는 번거로움이 사라집니다. NSG에 대한 자세한 내용은 [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md#security-rules)을 참조하세요.
* **포트 검색으로부터 보호:** 가상 머신을 퍼블릭 인터넷에 노출하지 않으므로 가상 네트워크 외부에 있는 악의적인 사용자에 의한 포트 검색으로부터 VM을 보호합니다.
* **제로 데이 공격으로부터 보호 단일 지점에서 강화:** Azure Bastion은 완전 플랫폼 관리형 PaaS 서비스입니다. 가상 네트워크의 경계에 위치하므로 가상 네트워크의 각 가상 머신을 강화할 필요가 없습니다. Azure 플랫폼은 Azure Bastion을 강화하고 항상 최신 상태로 유지함으로써 제로 데이 공격으로부터 보호합니다.

## <a name="skus"></a><a name="sku"></a>SKU

Azure Bastion에는 기본 및 표준의 두 가지 사용 가능한 SKU가 있습니다. SKU를 업그레이드하는 방법을 비롯한 자세한 내용은 [구성 설정](configuration-settings.md#skus) 문서를 참조하세요.

다음 표에서는 기능 및 해당 SKU를 보여 줍니다.

[!INCLUDE [Azure Bastion SKUs](../../includes/bastion-sku.md)]

## <a name="architecture"></a><a name="architecture"></a>아키텍처

Azure Bastion은 가상 네트워크에 배포되고 가상 네트워크 피어링을 지원합니다. 특히 Azure Bastion은 로컬 또는 피어링된 가상 네트워크에서 만든 VM에 대한 RDP/SSH 연결을 관리합니다.

RDP 및 SSH는 Azure에서 실행 중인 워크로드에 연결하는 데 사용할 수 있는 기본 수단 중 일부입니다. 인터넷을 통해 RDP/SSH 포트를 노출하는 것은 바람직하지 않으며 중요한 위협 요소로 간주됩니다. 이는 프로토콜 취약성으로 인해 종종 발생합니다. 이 위협 요소를 포함하기 위해 경계 네트워크의 공용 측에 요새 호스트(점프 서버라고도 함)를 배포할 수 있습니다. Bastion 호스트 서버는 공격에 대응하도록 설계 및 구성됩니다. 또한 Bastion 서버는 요새 뒤와 네트워크 내부에 있는 워크로드 모두에 대한 RDP 및 SSH 연결을 제공합니다.

:::image type="content" source="./media/bastion-overview/architecture.png" alt-text="Azure Bastion 아키텍처를 보여 주는 다이어그램":::

이 그림은 Azure Bastion 배포 아키텍처를 보여줍니다. 이 다이어그램에서

* Bastion 호스트는 최소/27 접두사가 있는 AzureBastionSubnet 서브넷이 포함된 가상 네트워크에 배포됩니다.
* 사용자는 HTML5 브라우저를 사용하여 Azure Portal에 연결합니다.
* 사용자가 연결할 가상 머신을 선택합니다.
* 한 번의 클릭으로 RDP/SSH 세션이 브라우저에서 열립니다.
* Azure VM에 공용 IP가 필요하지 않습니다.

## <a name="host-scaling"></a><a name="host-scaling"></a>호스트 스케일링

Azure Bastion은 수동 호스트 스케일링을 지원합니다. Azure Bastion에서 지원할 수 있는 동시 RDP/SSH 연결 수를 관리하기 위해 호스트 인스턴스 수(스케일 단위)를 구성할 수 있습니다. 호스트 인스턴스 수를 늘리면 Azure Bastion이 더 많은 동시 세션을 관리할 수 있습니다. 인스턴스 수를 줄이면 지원되는 동시 세션 수가 줄어듭니다. Azure Bastion은 최대 50개의 호스트 인스턴스를 지원합니다. 이 기능은 Azure Bastion 표준 SKU에만 사용할 수 있습니다.

자세한 내용은 [구성 설정](configuration-settings.md#instance) 문서를 참조하세요.

## <a name="pricing"></a><a name="pricing"></a>가격 책정

Azure Bastion의 경우 SKU, 스케일 단위 및 데이터 전송 속도가 조합되어 시간별로 가격이 책정됩니다. 가격 책정 정보는 [가격 책정](https://azure.microsoft.com/pricing/details/azure-bastion) 페이지에 있습니다.

## <a name="whats-new"></a><a name="new"></a>새로운 기능

RSS 피드를 구독하고 [Azure 업데이트](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Bastion) 페이지에서 최신 Azure Bastion 기능 업데이트를 확인합니다.

## <a name="bastion-faq"></a>Bastion FAQ

자주 묻는 질문은 [FAQ](bastion-faq.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [자습서: Azure Bastion 호스트를 만들고 Windows VM에 연결](tutorial-create-host-portal.md)합니다.
* [학습 모듈: Azure Bastion 소개](/learn/modules/intro-to-azure-bastion/).
* Azure의 몇 가지 다른 주요 [네트워킹 기능](../networking/fundamentals/networking-overview.md)을 알아봅니다.
