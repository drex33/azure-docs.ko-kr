---
title: Azure(대규모 인스턴스)의 SAP HANA 개요 | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA를 배포하는 방법에 대한 개요입니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/04/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 364953de1c56bcf41efb1a3ac9481b17603f3dec
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108227797"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure(대규모 인스턴스)란?

SAP HANA on Azure(대규모 인스턴스)는 Azure의 고유한 솔루션입니다. Azure는 SAP HANA를 배포하고 실행하기 위한 가상 머신을 제공하는 것 외에도, 운영 체제 미설치 전용 서버에서 SAP HANA를 실행하고 배포할 수 있는 가능성을 제공합니다. SAP HANA on Azure(대규모 인스턴스) 솔루션은 사용자에게 할당된 비공유 호스트/서버 운영 체제 미설치 하드웨어를 기반으로 합니다. 서버 하드웨어는 계산/서버, 네트워킹 및 스토리지 인프라가 포함된 큰 스탬프 안에 내장됩니다. SAP HANA on Azure(대규모 인스턴스)는 서로 다른 서버 SKU 또는 크기를 제공합니다. 단위는 36개 Intel CPU 코어와 768GB 메모리를 가질 수 있고 최대 480개 Intel CPU 코어 및 24TB 메모리까지 증대할 수 있습니다.

인프라 스탬프 내의 고객 격리는 테넌트에서 수행되며 다음과 같습니다.

- **네트워킹**: 고객에게 할당된 테넌트별 가상 네트워크를 통해 인프라 스택 내에서 고객이 격리됩니다. 테넌트는 단일 고객에게 할당됩니다. 고객은 여러 개의 테넌트를 둘 수 있습니다. 테넌트가 동일한 고객에게 속해 있더라도 테넌트의 네트워크 격리는 인프라 스탬프 수준에서 테넌트 간 네트워크 통신을 금지합니다.
- **스토리지 구성 요소**: 스토리지 볼륨이 할당된 스토리지 가상 머신을 통해 격리됩니다. 저장소 볼륨은 하나의 스토리지 가상 머신에만 할당될 수 있습니다. 스토리지 가상 머신은 인프라 스택의 단일 테넌트에게 독점적으로 할당됩니다. 따라서 스토리지 가상 머신에 할당된 스토리지 볼륨은 관련된 특정 단일 테넌트에서만 액세스할 수 있으며, 배포된 다른 테넌트에서는 표시되지 않습니다.
- **서버 또는 호스트**: 서버 또는 호스트 장치가 고객 또는 테넌트 간에 공유되지 않습니다. 고객에게 배포되는 서버 또는 호스트는 단일 테넌트에 할당되는 원자성 베어 메탈 컴퓨팅 단위입니다. 다른 고객과 호스트 또는 서버를 다른 고객과 공유할 수 있는 하드웨어 분할 또는 소프트 분할은 *사용되지 않습니다*. 특정 테넌트의 스토리지 가상 머신에 할당되는 스토리지 볼륨은 그러한 서버에 탑재됩니다. 테넌트에는 다양한 SKU의 하나 또는 다수의 서버 단위가 할당될 수 있습니다.
- SAP HANA on Azure(대규모 인스턴스) 인프라 스탬프 내에서 네트워킹, 스토리지 및 컴퓨팅 수준에 대한 테넌트 개념을 통해 서로 다른 여러 테넌트가 배포되고 서로 간에 격리됩니다. 


이러한 베어 메탈 서버 단위는 SAP HANA 실행만 지원합니다. SAP 애플리케이션 계층 또는 워크로드 미들웨어 계층은 가상 머신에서 실행됩니다. SAP HANA on Azure(대규모 인스턴스) 장치를 실행하는 인프라 스탬프는 Azure 네트워크 서비스 백본에 연결됩니다. 이러한 방식으로 SAP HANA on Azure(대규모 인스턴스) 장치와 가상 머신 간의 짧은 대기 시간 연결이 제공됩니다.

2021년 1월부로, HANA 대규모 인스턴스 스탬프와 배포 위치에 대한 수정 버전 두 가지를 구분합니다.

- Rev 3(수정 버전 3): 고객이 2019년 7월 이전에 배포할 수 있도록 만든 스탬프입니다.
- Rev 4(수정 버전 4): Azure VM 호스트에 가깝게 배포되고 지금까지 다음 Azure 지역에 배포된 새로운 스탬프 디자인입니다.
    -  미국 서부2 
    -  미국 동부
    -  미국 동부2(두 가용성 영역 사이)
    -  미국 중남부2(두 가용성 영역 사이)
    -  서유럽
    -  북유럽


이 문서는 SAP HANA on Azure(대규모 인스턴스)를 다루는 여러 문서 중 하나입니다. 여기서는 솔루션에서 제공하는 기본 아키텍처, 역할 및 서비스를 소개합니다. 솔루션의 고급 기능에 대해서도 설명합니다. 네트워킹 및 연결과 같은 대부분의 다른 영역에 대해서는 네 가지 다른 문서에서 세부 정보와 드릴다운 정보를 다루고 있습니다. SAP HANA on Azure(대규모 인스턴스) 설명서에서는 VM에 SAP NetWeaver를 설치하거나 배포하는 상황에 대해 다루지 않습니다. Azure의 SAP NetWeaver는 동일한 Azure 문서 컨테이너에 있는 별도의 문서에서 다룹니다. 


HANA 대규모 인스턴스 지침의 다른 문서는 다음 영역을 다룹니다.

- [SAP HANA on Azure(대규모 인스턴스) 개요 및 아키텍처](hana-overview-architecture.md)
- [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md)
- [SAP HANA on Azure(대규모 인스턴스) 설치 및 구성](hana-installation.md)
- [SAP HANA on Azure(대규모 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md)
- [SAP HANA on Azure(대규모 인스턴스) 문제 해결 및 모니터링](troubleshooting-monitoring.md)
- [STONITH를 사용하여 SUSE에서 고가용성 설정](./ha-setup-with-stonith.md)
- [OS 백업](./large-instance-os-backup.md)
- [Azure 예약을 사용하여 SAP HANA(대규모 인스턴스) 비용 절감](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md)

**다음 단계**
- [Know the terms(용어 알아보기)](hana-know-terms.md) 참조
