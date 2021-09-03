---
title: Azure(대규모 인스턴스)에서 SAP HANA의 아키텍처 | Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스)를 배포하기 위한 아키텍처를 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/21/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fee1aab009bdbf84acf1a73244d6686db50e4e3f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114450709"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Azure의 SAP HANA(대규모 인스턴스) 아키텍처

이 문서에서는 Azure의 SAP HANA(대규모 인스턴스)(BareMetal Infrastructure라고도 함)를 배포하기 위한 아키텍처를 설명합니다. 

Azure의 SAP HANA(대규모 인스턴스) 솔루션에는 높은 수준에서 VM(가상 머신)에 상주하는 SAP 애플리케이션 계층이 있습니다. 데이터베이스 계층은 SAP 인증 HLI(HANA 대규모 인스턴스)에 있습니다. HLI는 Azure IaaS VM과 동일한 Azure 지역에 있습니다.

> [!NOTE]
> SAP DBMS(데이터베이스 관리 시스템) 계층과 동일한 Azure 지역에 SAP 애플리케이션 계층을 배포합니다. 이 규칙은 Azure의 SAP 워크로드에 대해 게시된 정보에 잘 설명되어 있습니다. 

## <a name="architectural-overview"></a>아키텍처 개요

Azure의 SAP HANA(대규모 인스턴스)의 전체 아키텍처는 SAP TDI 인증 하드웨어 구성을 제공합니다. 하드웨어는 SAP HANA 데이터베이스를 위한 가상화되지 않은 운영 체제 미설치 고성능 서버입니다. SAP 애플리케이션 계층의 리소스를 필요에 따라 확장할 수 있는 유연성을 제공합니다.

![Azure(큰 인스턴스)에서 SAP HANA의 아키텍처 개요](./media/hana-overview-architecture/image1-architecture.png)

표시된 아키텍처는 다음과 같은 세 가지 섹션으로 구분됩니다.

- **오른쪽**: 최종 사용자가 SAP와 같은 LOB(기간 업무) 애플리케이션에 액세스할 수 있도록 데이터 센터에서 서로 다른 애플리케이션을 실행하는 온-프레미스 인프라를 보여 줍니다. 이상적으로 이 온-프레미스 인프라는 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)를 통해 Azure에 연결됩니다.

- **가운데:** Azure IaaS를 보여 주고, 이 경우 VM을 사용하여 SAP HANA를 DBMS로 사용하는 SAP 또는 다른 애플리케이션을 호스팅합니다. VM에서 제공하는 메모리에서 작동하는 더 작은 HANA 인스턴스는 애플리케이션 계층과 함께 VM에 배포됩니다. 가상 머신에 대한 자세한 내용은 [가상 머신](https://azure.microsoft.com/services/virtual-machines/)을 참조하세요.

   Azure 네트워크 서비스는 다른 애플리케이션과 함께 SAP 시스템을 가상 네트워크로 그룹화하는 데 사용됩니다. 이러한 가상 네트워크는 온-프레미스 시스템 및 Azure의 SAP HANA(대규모 인스턴스)에 연결됩니다.

   Azure에서 실행하도록 지원되는 SAP NetWeaver 애플리케이션 및 데이터베이스의 경우 [SAP Support Note #1928533 – Azure의 SAP 애플리케이션: 지원되는 제품 및 Azure VM 유형](https://launchpad.support.sap.com/#/notes/1928533)을 참조하세요. Azure에 SAP 솔루션을 배포하는 방법에 대한 설명서는 다음을 참조하세요.

  -  [Windows 가상 머신에서 SAP 사용](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [Azure 가상 머신에서 SAP 솔루션 사용](get-started.md)

- **왼쪽:** Azure 대규모 인스턴스 스탬프의 SAP HANA TDI 인증 하드웨어를 보여 줍니다. HANA(대규모 인스턴스) 디바이스는 온-프레미스 서버가 Azure로 연결할 때와 동일한 기술을 사용하여 Azure 구독의 가상 네트워크에 연결합니다. 2019년 5월에 ExpressRoute 게이트웨이 없이 HANA(대규모 인스턴스) 단위와 Azure VM 간의 통신을 허용하는 최적화를 도입했습니다. ExpressRoute FastPath라고 하는 이 최적화는 앞의 다이어그램에서 빨간색 선으로 표시됩니다.

## <a name="components-of-the-azure-large-instance-stamp"></a>Azure(대규모 인스턴스) 스탬프의 구성 요소

Azure 큰 인스턴스 스탬프 자체는 다음 구성 요소를 결합합니다.

- **컴퓨팅:** 필요한 컴퓨팅 기능을 제공하고 SAP HANA 인증된 차세대 Intel Xeon 프로세서 기반의 서버입니다.
- **네트워크:** 컴퓨팅, 스토리지 및 LAN 구성 요소를 상호 연결하는 통합된 고속 네트워크 패브릭입니다.
- **스토리지:** 통합된 네트워크 패브릭을 통해 액세스되는 스토리지 인프라입니다. 제공되는 스토리지 용량은 배포된 Azure의 SAP HANA(대규모 인스턴스) 구성에 따라 다릅니다. 월별 추가 비용으로 더 많은 스토리지 용량을 사용할 수 있습니다.

## <a name="tenants"></a>테넌트

큰 인스턴스 스탬프의 다중 테넌트 인프라 내에서 고객이 격리된 테넌트로 배포됩니다. 테넌트 배포 시 Azure 등록 내에서 Azure 구독 이름을 지정합니다. Azure 구독은 HANA 대규모 인스턴스가 청구되는 구독입니다. 이러한 테넌트는 Azure 구독과 1:1 관계를 포함합니다. 

네트워크의 경우 서로 다른 Azure 구독에 속한 다른 가상 네트워크에서 한 Azure 지역의 한 테넌트에 배포된 HANA 대규모 인스턴스에 액세스할 수 있습니다. 이러한 Azure 구독은 동일한 Azure 등록에 속해야 합니다.

## <a name="availability-across-regions"></a>지역 간 기능 가용성

VM과 마찬가지로, SAP HANA on Azure(대규모 인스턴스)가 여러 Azure 지역에 제공됩니다. 재해 복구 기능을 제공하려면 옵트인하도록 선택할 수 있습니다. 하나의 지정학적 지역의 서로 다른 큰 인스턴스 스탬프가 서로 연결됩니다. 예를 들어 미국 서부 및 미국 동부에 있는 HANA 대규모 인스턴스 스탬프가 재해 복구 복제를 위한 전용 네트워크 링크를 통해 연결됩니다.

## <a name="available-skus"></a>사용 가능한 SKU

Azure에서 다양한 VM 유형 중에서 선택할 수 있는 것처럼 HANA(대규모 인스턴스)의 다양한 SKU에서 선택할 수 있습니다. 특정 SAP HANA 워크로드 유형에 적합한 SKU를 선택할 수 있습니다. SAP는 Intel 프로세서 세대에 따라 다양한 워크로드에 대한 메모리 대 프로세서 소켓 비율을 적용합니다. 사용 가능한 SKU에 대한 자세한 내용은 [HLI에 사용 가능한 SKU](hana-available-skus.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

SAP HANA(대규모 인스턴스) 네트워크 아키텍처에 알아봅니다.

> [!div class="nextstepaction"]
> [SAP HANA(대규모 인스턴스) 네트워크 아키텍처](hana-network-architecture.md)
