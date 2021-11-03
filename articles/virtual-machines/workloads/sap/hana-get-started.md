---
title: Azure 가상 머신에 SAP HANA 설치 | Microsoft Docs
description: Azure VM에 SAP HANA를 설치하기 위한 가이드
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: 65de72576ac484ea29710baaba9f391bfdcbc7b6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421592"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Azure 가상 머신에 SAP HANA 설치
## <a name="introduction"></a>소개
이 가이드를 통해 Azure 가상 머신에 HANA를 성공적으로 배포하는 데 적합한 리소스를 파악할 수 있습니다. 이 가이드에서는 Azure VM에 SAP HANA를 설치하기 전에 확인해야 하는 설명서 리소스를 설명합니다. 따라서 Azure VM에서 지원되는 SAP HANA 구성을 통해 마무리할 수 있는 올바른 단계를 수행할 수 있습니다.  

> [!NOTE]
> 이 가이드에서는 Azure VM에 SAP HANA를 배포하는 방법에 대해 설명합니다. HANA 대규모 인스턴스에 SAP HANA를 배포하는 방법에 대한 자세한 내용은 [Azure에서 SAP HANA(대규모 인스턴스)를 설치하고 구성하는 방법](./hana-installation.md)을 참조하세요.
 
## <a name="prerequisites"></a>사전 요구 사항
이 가이드에서는 다음에 대해서도 잘 알고 있다고 가정합니다.
* SAP HANA, SAP NetWeaver 및 온-프레미스에 설치하는 방법
* Azure에서 SAP HANA 및 SAP 애플리케이션 인스턴스를 설치하고 운영하는 방법
* 아래에 설명된 개념 및 절차는 다음과 같습니다.
   * Azure Virtual Network 계획 및 Azure Storage 사용량을 포함하는 Azure에서의 SAP 배포 계획. [Azure Virtual Machines의 SAP NetWeaver - 계획 및 구현 가이드](./planning-guide.md) 참조
   * Azure에 VM을 배포하는 배포 원칙 및 방법 - [SAP용 Azure Virtual Machines 배포](./deployment-guide.md) 참조
   * [Azure 가상 머신의 SAP HANA 고가용성](./sap-hana-availability-overview.md)에서 설명된 SAP HANA의 고가용성 개념

## <a name="step-by-step-before-deploying"></a>배포 전 단계별 과정
이 섹션에는 Azure 가상 머신에 SAP HANA 설치를 시작하기 전에 수행해야 하는 여러 단계가 나열되어 있습니다. 순서는 열거되어 있으며 열거된 대로 따라야 합니다.

1. 가능한 모든 배포 시나리오가 Azure에서 지원되는 것은 아닙니다. 따라서 SAP HANA 배포에서 염두에 둔 시나리오에 대한 [Azure 가상 머신의 SAP 워크로드 지원 시나리오](./sap-planning-supported-configurations.md) 문서를 확인해야 합니다. 시나리오가 나열되지 않은 경우 테스트되지 않았으며 결과적으로 지원되지 않는다고 가정해야 합니다.
2. SAP HANA 배포를 위한 메모리 요구 사항에 대한 대략적인 아이디어가 있다고 가정하고 적절한 Azure VM을 찾아야 합니다. [SAP support note #1928533](https://launchpad.support.sap.com/#/notes/1928533)에 설명된 것처럼 SAP NetWeaver에 대해 인증된 모든 VM이 SAP HANA 인증을 받은 것은 아닙니다. SAP HANA 인증 Azure VM의 출처는 웹 사이트 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)입니다. **S** 로 시작하는 단위는 Azure VM이 아닌 [HANA 대규모 인스턴스](./hana-overview-architecture.md) 단위입니다.
3. Azure VM 유형에 따라 SUSE Linux 또는 Red Hat Linux의 최소 운영 체제 릴리스가 다릅니다. 웹 사이트 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에서 SAP HANA 인증 단위 목록의 항목을 클릭하면 이 단위의 상세 데이터를 가져올 수 있습니다. 지원되는 HANA 워크로드 외에도 SAP HANA의 해당 단위에서 지원되는 OS 릴리스가 나열됩니다.
4. 운영 체제 릴리스에서는 특정 최소 커널 릴리스를 고려해야 합니다. 이러한 최소 릴리스는 다음 SAP 지원 참고 사항에 설명되어 있습니다.
    - [SAP support note #2814271 - Azure에서 SAP HANA 백업이 체크섬 오류로 실패](https://launchpad.support.sap.com/#/notes/2814271)
    - [SAP support note #2753418 - 타이머 폴백으로 인한 잠재적 성능 저하](https://launchpad.support.sap.com/#/notes/2753418)
    - [SAP support note #2791572 - Azure의 Hyper-V에 대한 VDSO 지원 누락에 따른 성능 저하](https://launchpad.support.sap.com/#/notes/2791572)
4. 선택한 가상 머신 유형에 지원되는 OS 릴리스에 따라 원하는 SAP HANA 릴리스가 해당 운영 체제 릴리스에서 지원되는지 확인해야 합니다. 다른 운영 체제 릴리스를 사용하는 SAP HANA 릴리스의 지원 매트릭스는 [SAP support note #2235581](https://launchpad.support.sap.com/#/notes/2235581)을 참조하세요.
5. Azure VM 유형, 운영 체제 릴리스 및 SAP HANA 릴리스의 유효한 조합을 찾았을 수 있으므로 SAP 제품 가용성 매트릭스를 확인해야 합니다. SAP 가용성 매트릭에서 SAP HANA 데이터베이스에 대해 실행하려는 SAP 제품이 지원되는지 여부를 확인할 수 있습니다.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>단계별 VM 배포 및 게스트 OS 고려 사항
이 단계에서는 VM을 배포하는 단계를 거쳐 HANA를 설치하고 결국 설치 후 선택한 운영 체제를 최적화해야 합니다.

1. Azure 갤러리에서 기본 이미지를 선택합니다. SAP HANA에 대해 고유의 운영 체제 이미지를 빌드하려면 성공적인 SAP HANA 설치에 필요한 다른 모든 패키지를 알아야 합니다. 그렇지 않으면 SAP 용 SUSE 및 Red Hat 이미지를 사용 하거나 갤러리에서 SAP HANA 하는 것이 좋습니다. 이러한 이미지에는 성공적인 HANA 설치에 필요한 패키지가 포함되어 있습니다. 운영 체제 공급자의 지원 계약에 따라 자체 라이선스를 불러오는 이미지를 선택해야 합니다. 또는 지원이 포함된 OS 이미지를 선택합니다.
2. 자체 라이선스를 가져와야 하는 게스트 OS 이미지를 선택한 경우 구독에 OS 이미지를 등록해야 최신 패치를 다운로드하고 적용할 수 있습니다. 이 단계에서는 공용 인터넷 액세스가 필요합니다. 예를 들어, Azure에서 SMT 서버의 프라이빗 인스턴스를 설정하지 않는 한 말입니다.
3. VM의 네트워크 구성을 결정합니다. [Azure에서 SAP HANA 인프라 구성 및 작업](./hana-vm-operations.md) 문서에서 자세한 내용을 확인할 수 있습니다. Azure의 가상 네트워크 카드에 할당할 수 있는 네트워크 처리량 할당량은 없습니다. 따라서 다른 vNIC를 통해 트래픽을 전달하는 유일한 목적은 보안 고려 사항을 기반으로 합니다. 여러 vNIC를 통한 트래픽 라우팅의 복잡성과 보안 측면에서 적용되는 요구 사항 간에 지원 가능한 절충을 찾을 수 있습니다.
3. VM이 배포되고 등록된 후에는 운영 체제에 최신 패치를 적용합니다. 사용자 고유의 구독으로 등록됩니다. 또는 운영 체제 지원을 포함하는 이미지를 선택한 경우 VM은 이미 패치에 액세스할 수 있어야 합니다. 
4. SAP HANA에 필요한 튜닝을 적용합니다. 이러한 튜닝은 다음 SAP 지원 참고 사항에 나열되어 있습니다.

    - [SAP Support Note #2694118 - Azure의 Red Hat Enterprise Linux HA 추가 항목](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP Support Note #1984787 - SUSE LINUX Enterprise Server 12: 설치 노트](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP Support Note #2578899 - SUSE Linux Enterprise Server 15: 설치 노트](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP Support Note #2002167 - Red Hat Enterprise Linux 7.x: 설치 및 업그레이드](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP Support Note #2292690 - SAP HANA DB: RHEL 7에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/0002292690)(영문) 
    -  [SAP 지원 노트 #2772999 - Red Hat Enterprise Linux 8.x: 설치 및 구성](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP Support Note #2777782 - SAP HANA DB: RHEL 8에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP support note #2455582 - Linux: GCC 6.x로 컴파일된 SAP 애플리케이션 실행](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [SAP support note #2382421 - HANA 및 OS 수준에서 네트워크 구성 최적화](https://launchpad.support.sap.com/#/notes/2382421)

1. SAP HANA에 대한 Azure 스토리지 유형을 선택합니다. 이 단계에서는 SAP HANA 설치를 위한 스토리지 레이아웃을 결정해야 합니다. 연결된 Azure 디스크 또는 기본 Azure NFS 공유를 사용하게 됩니다. 지원되는 Azure 스토리지 유형 및 사용할 수 있는 다양한 Azure 스토리지 유형의 조합은 [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md)에 설명되어 있습니다. 시작 지점으로 문서화된 구성을 사용합니다. 비프로덕션 시스템의 경우 처리량이나 IOPS를 더 낮게 구성할 수 있습니다. 프로덕션용으로는 더 높은 처리량과 IOPS를 구성해야 할 수 있습니다.
2. M 시리즈 또는 Mv2 시리즈 VM을 사용하는 경우 DBMS 트랜잭션 로그 또는 다시 실행 로그를 포함하는 볼륨에 대해 [Azure 쓰기 가속기](../../how-to-enable-write-accelerator.md)를 구성했는지 확인합니다. 문서화된 쓰기 가속기에 대한 제한 사항에 유의하세요.
2. 배포된 VM에서 [Azure 가속화된 네트워킹](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)을 사용하도록 설정했는지 확인합니다.

> [!NOTE]
> 다른 sap-tune 프로필의 일부 명령이나 참고 사항에 설명된 대로 일부 명령은 Azure에서 성공적으로 실행되지 않을 수 있습니다. 기본 Azure 호스트 하드웨어의 전원 모드를 조작할 수 없으므로 VM의 전원 모드를 조작하는 명령은 일반적으로 오류와 함께 반환됩니다.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Azure 가상 머신과 관련된 단계별 준비
Azure의 세부 사항 중 하나는 SAP 호스트 에이전트에 대한 모니터링 데이터를 제공하는 Azure VM 확장의 설치입니다. 이 모니터링 확장의 설치에 대한 세부 정보는 다음에 설명되어 있습니다.

-  [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)은 Azure에서 Linux VM을 사용한 SAP 고급 모니터링에 대해 설명합니다. 
-  [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)는 Linux의 SAPOSCOL에 대한 정보를 설명합니다. 
-  [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)는 Microsoft Azure의 SAP용 주요 모니터링 메트릭에 대해 설명합니다.
-  [SAP NetWeaver에 대한 Azure Virtual Machines 배포](./deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>SAP HANA 설치
Azure 가상 머신을 배포하고 운영 체제를 등록 및 구성한 상태에서 SAP 설치에 따라 SAP HANA를 설치할 수 있습니다. 이 설명서에 대한 좋은 시작으로 이 SAP 웹 사이트 [HANA 리소스](https://www.sap.com/products/s4hana-erp.html?btp=9d3e6f82-d8ab-4122-8d2d-bf4971217afd)부터 시작하세요.

Azure Premium Storage 또는 Ultra Disk의 직접 연결된 디스크를 사용하는 SAP HANA 스케일 아웃 구성의 경우 [Azure에서 SAP HANA 인프라 구성 및 작업](./hana-vm-operations.md#configuring-azure-infrastructure-for-sap-hana-scale-out) 문서에서 세부 사항을 확인하세요.


## <a name="additional-resources-for-sap-hana-backup"></a>SAP HANA 백업을 위한 추가 리소스
Azure VM의 SAP HANA 데이터베이스를 백업하는 방법에 대한 자세한 내용은 다음을 참조하세요.
* [Azure Virtual Machines의 SAP HANA 백업 가이드](../../../backup/sap-hana-db-about.md)
* [파일 수준의 SAP HANA Azure Backup](../../../backup/sap-hana-db-about.md)

## <a name="next-steps"></a>다음 단계
다음 설명서를 참조하세요.

- [Azure에서 SAP HANA 인프라 구성 및 작업](./hana-vm-operations.md)
- [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md)