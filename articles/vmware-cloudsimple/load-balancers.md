---
title: Azure VMware Solution by CloudSimple - CloudSimple 프라이빗 클라우드에 대한 부하 분산 솔루션 선택
description: 프라이빗 클라우드에 애플리케이션을 배포하는 부하 분산 옵션을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36ed6b55b3f97f53a0b8be4829dff97bd0d9c120
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616382"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>CloudSimple 프라이빗 클라우드에 대한 부하 분산 솔루션 선택

CloudSimple 프라이빗 클라우드에 애플리케이션을 배포하는 경우 부하 분산에 대한 여러 옵션 중 하나를 선택할 수 있습니다.

CloudSimple 프라이빗 클라우드에서 가상 또는 소프트웨어 기반 부하 분산 장치를 선택하거나 Azure 구독에서 실행되는 Azure L7 부하 분산 장치를 사용하여 CloudSimple 프라이빗 클라우드에서 실행되는 웹 계층 VM을 프런트 엔드할 수도 있습니다. 여기에 몇 가지 옵션을 나열합니다.

## <a name="virtual-load-balancers"></a>가상 부하 분산 장치

vCenter 인터페이스를 통해 VMware 환경에 가상 부하 분산 장치 어플라이언스를 배포하고 애플리케이션 트래픽을 프런트 엔드하도록 구성할 수 있습니다.

널리 사용되는 공급 업체는 다음과 같습니다. NginX: http://nginx.org/en/docs/http/load_balancing.html F5- BigIP - Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 부하 분산 장치

Azure Application Gateway를 프라이빗 클라우드에서 실행 중인 애플리케이션의 L7 부하 분산 장치로 사용하는 경우 부하 분산 장치 소프트웨어를 관리할 필요가 없습니다. 부하 분산 장치 소프트웨어는 Azure에서 관리합니다. 프라이빗 클라우드의 모든 웹 계층 VM은 개인 IP 주소를 사용하며 이름을 확인하는 데 추가 NAT 규칙 또는 공용 IP 주소가 필요하지 않습니다. 웹 계층 VM은 대기 시간이 짧은 프라이빗 고대역폭 연결을 통해 Azure Application Gateway와 통신합니다.

이 솔루션을 구성하는 방법에 대해 자세히 알아보려면 [Azure Application Gateway를 L7 부하 분산 장치로 사용]에 대한 솔루션 가이드를 참조하세요.

## <a name="azure-internal-load-balancer"></a>Azure 내부 부하 분산 장치

Azure 구독의 Azure vNet 내에서 웹 프런트 엔드 계층이 실행되고 애플리케이션의 DB 계층이 CloudSimple 프라이빗 클라우드의 VMware VM에서 실행되는 하이브리드 배포에서 애플리케이션을 실행하도록 선택한 경우, 트래픽 관리를 위해 DB 계층 VM 앞에 Azure 내부 부하 분산 장치(L4 부하 분산 장치)를 사용할 수 있습니다.

자세히 알아보려면 Azure [내부 Load Balancer](../load-balancer/components.md#frontend-ip-configurations) 설명서를 참조하세요.

## <a name="global-server-load-balancer"></a>글로벌 서버 부하 분산 장치

DNS 기반 부하 분산 장치를 찾고 있는 경우 Azure Marketplace에서 사용할 수 있는 타사 솔루션을 사용하거나 네이티브 Azure 솔루션으로 이동할 수 있습니다.

Azure Traffic Manager는 고가용성 및 응답성을 제공하면서 글로벌 Azure 지역 및 온-프레미스의 서비스에 적절하게 분산할 수 있는 DNS 기반 트래픽 부하 분산 장치입니다. 자세히 알아보려면 [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) 설명서를 참조하세요.
