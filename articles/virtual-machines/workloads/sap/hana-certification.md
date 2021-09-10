---
title: Azure(대규모 인스턴스)의 SAP HANA 인증 | Microsoft Docs
description: Azure(대규모 인스턴스)에 있는 SAP HANA의 인증을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/14/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ade0e82131f65183ac9471bb77d0ab0cc92ae285
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114219643"
---
# <a name="certification"></a>인증

NetWeaver 인증 외에도, SAP에서는 Azure IaaS 및 BareMetal 인프라와 같은 특정 인프라에서 SAP HANA를 지원하기 위해 SAP HANA에 대한 특수한 인증을 요구합니다.

NetWeaver 및 약간의 SAP HANA 인증에 대한 핵심 SAP Note는 [SAP Note #1928533 – Azure의 SAP 애플리케이션: 지원되는 제품 및 Azure VM 유형](https://launchpad.support.sap.com/#/notes/1928533)입니다.

Azure Large Instance의 SAP HANA에 대한 인증 레코드는 [SAP HANA 인증 IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 사이트에서 제공합니다. 

SAP HANA 인증 IaaS Platforms 사이트에서 언급한 SAP HANA on Azure(Large Instances) 유형은 Microsoft 및 SAP 고객에게 다음을 배포할 수 있는 기능을 제공합니다.

- Large SAP Business Suite
- SAP BW
- S/4 HANA
- BW/4HANA
- Azure의 기타 SAP HANA 작업. 

이 솔루션은 SAP-HANA 인증 전용 하드웨어 스탬프([SAP HANA 맞춤형 TDI(데이터 센터 통합)](https://scn.sap.com/docs/DOC-63140))를 기반으로 합니다. SAP HANA TDI로 구성된 솔루션을 실행하는 경우, 위의 모든 SAP HANA 기반 애플리케이션은 하드웨어 인프라에서 작동합니다.

VM에서 SAP HANA를 실행하는 것과 비교할 때 이 솔루션은 훨씬 더 큰 메모리 볼륨의 이점을 제공합니다. 

## <a name="key-concepts"></a>주요 개념

이 솔루션을 사용하려면 다음 주요 사항을 이해해야 합니다.

- SAP 애플리케이션 계층 및 비SAP 애플리케이션은 일반적인 Azure 하드웨어 스탬프에서 호스팅되는 VM에서 실행됩니다.
- 고객 온-프레미스 인프라, 데이터 센터 및 애플리케이션 배포는 ExpressRoute(권장) 또는 VPN(가상 사설망)을 통해 클라우드 플랫폼에 연결됩니다. Active Directory 및 DNS도 Azure로 확장됩니다.
- HANA 워크로드에 대한 SAP HANA 데이터베이스 인스턴스는 Azure(큰 인스턴스)의 SAP HANA에서 실행됩니다. 대규모 인스턴스 스탬프는 Azure 네트워킹에 연결되므로 VM에서 실행되는 소프트웨어는 HANA 대규모 인스턴스에서 실행되는 HANA 인스턴스와 상호 작용할 수 있습니다.
- SAP HANA on Azure(대규모 인스턴스) 하드웨어는 SUSE Linux Enterprise Server 또는 Red Hat Enterprise Linux가 미리 설치되어 있는 IaaS에서 제공되는 전용 하드웨어입니다. 가상 머신과 마찬가지로 운영 체제에 대한 추가 업데이트 및 유지 관리는 사용자의 책임입니다.
- HANA 대규모 인스턴스 장치에서 SAP HANA를 실행하는 데 필요한 HANA 또는 기타 구성 요소를 설치하는 것은 사용자의 책임입니다. 진행 중인 SAP HANA on Azure 개별 작업 및 관리도 모두 사용자의 책임입니다.
- Azure(Large Instances)에서 SAP HANA에 연결되는 Azure 구독에 다른 구성 요소를 설치할 수도 있습니다. 예를 들어, SAP HANA 데이터베이스와 통신할 수 있도록 하는 구성 요소는 다음과 같습니다.        
    - 점프 서버
    - RDP 서버
    - SAP HANA Studio
    - SAP BI 시나리오용 SAP Data Services
    - 네트워크 모니터링 솔루션.
- Azure와 마찬가지로 HANA 대규모 인스턴스는 고가용성 및 재해 복구 기능을 지원합니다.

## <a name="next-steps"></a>다음 단계

HANA 대규모 인스턴스에 사용할 수 있는 SKU에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [HLI에 사용 가능한 SKU](hana-available-skus.md)
