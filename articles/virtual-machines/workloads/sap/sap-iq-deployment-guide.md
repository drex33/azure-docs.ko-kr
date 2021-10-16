---
title: Azure의 SAP IQ를 사용하여 SAP BW NLS 구현 | Microsoft Docs
description: Azure의 SAP IQ를 사용하여 SAP BW NLS 솔루션을 계획, 배포 및 구성합니다.
services: virtual-machines,virtual-machines-windows,virtual-machines-linux,virtual-network,storage,azure-netapp-files,azure-shared-disk,managed-disk
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2021
ms.author: depadia
ms.openlocfilehash: 7ece73f467e2dc148e79512c7dd1a6fcc19cf270
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072635"
---
# <a name="sap-bw-nls-implementation-guide-with-sap-iq-on-azure"></a>Azure의 SAP IQ를 사용한 SAP BW NLS 구현 가이드

SAP BW(Business Warehouse) 시스템을 몇 년 동안 실행한 고객들은 데이터베이스 크기가 기하급수적으로 증가하여 컴퓨팅 비용이 상승하고 있습니다. 비용과 성능의 적절한 균형을 달성하기 위해 고객은 NLS(니어라인 스토리지)를 사용하여 기록 데이터를 마이그레이션할 수 있습니다. 

SAP IQ 기반 NLS 구현은 SAP이 기본 데이터베이스(SAP HANA 또는 AnyDB)에서 기록 데이터를 이동하는 표준 방법입니다. SAP IQ의 통합으로 자주 액세스하는 데이터와 자주 액세스하지 않는 데이터를 분리할 수 있으므로 SAP BW 시스템에서 리소스 요구량이 줄어듭니다. 

이 가이드에서는 Azure의 SAP IQ를 사용하여 SAP BW NLS를 계획, 배포 및 구성하기 위한 지침을 제공합니다. 이 가이드는 SAP IQ NLS 배포와 관련된 일반적인 Azure 서비스 및 기능만 다루고 NLS 파트너 솔루션은 다루지 않습니다. 

이 가이드는 SAP IQ를 사용한 NLS 배포에 대한 SAP의 표준 설명서를 대체하지 않습니다. 그보다는 공식 설치 및 관리 설명서를 보완합니다. 

## <a name="solution-overview"></a>솔루션 개요

운영 중인 SAP BW 시스템에서 데이터의 볼륨은 비즈니스 및 법적 요구 사항으로 인해 지속적으로 늘어납니다. 많은 양의 데이터는 시스템 성능에 영향을 미치고 관리 부하를 증가시킬 수 있으므로 데이터 에이징 전략을 구현해야 합니다. 

삭제하지 않고 SAP BW 시스템의 데이터 양을 유지하려면 데이터 보관을 사용할 수 있습니다. 데이터는 먼저 보관 또는 니어라인 스토리지로 이동된 다음 SAP BW 시스템에서 삭제됩니다. 데이터가 보관된 방식에 따라 데이터에 직접 액세스하거나 필요에 따라 다시 로드할 수 있습니다. 

SAP BW 사용자는 SAP IQ를 니어라인 스토리지 솔루션으로 사용할 수 있습니다. 니어라인 솔루션인 SAP IQ용 어댑터는 SAP BW 시스템과 함께 제공됩니다. NLS를 구현하면 자주 사용되는 데이터는 SAP BW 온라인 데이터베이스(SAP HANA 또는 AnyDB)에 저장됩니다. 자주 액세스하지 않는 데이터는 SAP IQ에 저장되므로 데이터 관리 비용이 감소하고 SAP BW 시스템의 성능이 향상됩니다. 온라인 데이터와 니어라인 데이터 간의 일관성을 보장하기 위해 보관된 파티션은 잠겨 있고 읽기 전용입니다. 

SAP IQ는 심플렉스 및 멀티플렉스의 두 가지 유형의 아키텍처를 지원합니다. 심플렉스 아키텍처에서는 SAP IQ 서버의 단일 인스턴스가 단일 가상 머신에서 실행됩니다. 파일은 호스트 컴퓨터 또는 네트워크 스토리지 디바이스에 있을 수 있습니다. 

> [!Important]
> SAP-NLS 솔루션의 경우 SAP에서 심플렉스 아키텍처만 사용 가능하고 평가됩니다.

![SAP IQ 솔루션에 대한 개요를 보여 주는 다이어그램.](media/sap-iq-deployment-guide/sap-iq-solution-overview.png)

Azure에서 SAP IQ 서버는 별도의 VM(가상 머신)에 구현해야 합니다. 이미 다른 데이터베이스 인스턴스가 실행 중인 기존 서버에 SAP IQ 소프트웨어를 설치하지 않는 것이 좋습니다. SAP IQ가 전체 CPU 및 메모리를 사용하기 때문입니다. 하나의 SAP IQ 서버를 여러 SAP NLS 구현에 사용할 수 있습니다. 

## <a name="support-matrix"></a>지원 매트릭스

SAP IQ NLS 솔루션에 대한 지원 매트릭스에는 다음이 포함됩니다.

- **운영 체제**: SAP IQ는 운영 체제 수준에서만 인증됩니다. Azure 인프라에서 실행하도록 호환되는 한 Azure 환경에서 SAP IQ 인증 운영 체제를 실행할 수 있습니다. 자세한 내용은 [SAP Note 2133194](https://launchpad.support.sap.com/#/notes/2133194)를 참조하세요.

- **SAP BW 호환성**: SAP IQ용 니어라인 스토리지는 이미 유니코드로 실행되는 SAP BW 시스템용으로만 출시됩니다. [SAP Note 1796393](https://launchpad.support.sap.com/#/notes/1796393)에는 SAP BW에 대한 정보가 포함되어 있습니다.

- **스토리지**: Azure에서 SAP IQ는 프리미엄 관리 디스크(Windows 및 Linux), Azure 공유 디스크(Windows만 해당) 및 Azure NetApp Files(Linux만 해당)를 지원합니다. 

SAP IQ 릴리스에 기반한 최신 정보는 [제품 가용성 매트릭스](https://userapps.support.sap.com/sap/support/pam)를 참조하세요. 

## <a name="sizing"></a>크기 조정

SAP IQ의 크기는 CPU, 메모리 및 스토리지로 제한됩니다. Azure의 SAP IQ에 대한 일반적인 크기 조정 지침은 [SAP Note 1951789](https://launchpad.support.sap.com/#/notes/1951789)에서 찾을 수 있습니다. 지침을 따라 얻은 크기 권장 사항은 인증된 SAP용 Azure 가상 머신 유형에 매핑되어야 합니다. [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533)에는 지원되는 SAP 제품 및 Azure VM 유형 목록이 제공됩니다. 

[SAP Note 1951789](https://launchpad.support.sap.com/#/notes/1951789)에 설명된 SAP IQ 크기 조정 가이드 및 크기 조정 워크시트는 SAP IQ 데이터베이스의 기본 사용을 위해 개발되었습니다. SAP IQ 데이터베이스 계획을 위한 리소스는 반영되지 않기 때문에 SAP NLS용 리소스가 사용하지 않을 수 있습니다.

## <a name="azure-resources"></a>Azure 리소스

### <a name="regions"></a>영역

Azure에서 SAP 시스템을 이미 실행 중인 경우에는 아마도 지역을 확인했을 것입니다. SAP IQ 배포는 NLS 솔루션을 구현하는 SAP BW 시스템과 동일한 지역에 있어야 합니다. 

SAP IQ의 아키텍처를 결정하려면 해당 지역에서 Azure NetApp Files(Linux용 NFS만 해당)와 같이 SAP IQ에 필요한 서비스를 사용할 수 있는지 확인해야 합니다. 해당 지역의 서비스 가용성을 확인하려면 [지역별 사용 가능 제품](https://azure.microsoft.com/global-infrastructure/services/) 웹 페이지를 참조하세요.

### <a name="availability-sets"></a>가용성 집합 

Azure 인프라에서 SAP 시스템의 중복성을 달성하려면 애플리케이션을 가용성 집합 또는 가용성 영역에 배포해야 합니다. SAP IQ 멀티플렉스 아키텍처를 사용하여 SAP IQ 고가용성을 달성할 수 있지만 멀티플렉스 아키텍처는 NLS 솔루션의 요구 사항을 충족하지 않습니다. 

SAP IQ 심플렉스 아키텍처의 고가용성을 달성하려면 사용자 지정 솔루션이 포함된 2노드 클러스터를 구성해야 합니다. 2노드 SAP IQ 클러스터는 가용성 집합 또는 가용성 영역에 배포할 수 있지만 노드에 연결되는 Azure 스토리지 유형에 따라 배포 방법이 결정됩니다. 현재 Azure 공유 프리미엄 디스크 및 Azure NetApp Files는 영역 배포를 지원하지 않습니다. 따라서 가용성 집합이 유일한 SAP IQ 배포 옵션으로 남습니다. 

### <a name="virtual-machines"></a>가상 머신

SAP IQ 크기 조정에 따라 요구 사항을 Azure 가상 머신에 매핑해야 합니다. 이 방법은 Azure에서 SAP 제품에 대해 지원됩니다. [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533)은 Windows 및 Linux의 SAP 제품에 대해 지원되는 Azure VM 유형을 확인하기 좋은 출발점입니다. 

지원되는 VM 유형만 선택하는 것 외에도 특정 지역에서 해당 VM 유형을 사용할 수 있는지 여부도 확인해야 합니다. VM 유형의 가용성은 [지역별 사용 가능 제품](https://azure.microsoft.com/global-infrastructure/services/) 웹 페이지에서 확인할 수 있습니다. 가격 책정 모델을 선택하려면 [SAP 워크로드를 위한 Azure 가상 머신](planning-guide.md#azure-virtual-machines-for-sap-workload)을 참조하세요. 

> [!TIP]
> 프로덕션 시스템의 경우 코어 대 메모리 비율 때문에 E-Series 가상 머신을 사용하는 것이 좋습니다.

### <a name="storage"></a>Storage

Azure Storage에는 고객이 사용할 수 있는 다양한 스토리지 유형이 있습니다. 이에 대한 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형](../../disks-types.md) 문서에서 확인할 수 있습니다. 

Azure의 스토리지 유형 중 일부는 SAP 시나리오에서 제한적으로 사용되지만 다른 유형은 특정 SAP 워크로드 시나리오에 적합하거나 최적화되어 있습니다. 자세한 내용은 [SAP 워크로드를 위한 Azure Storage 유형](planning-guide-storage.md) 가이드를 참조하세요. SAP에 적합한 스토리지 옵션이 강조 표시되어 있습니다. 

Azure의 SAP IQ의 경우 다음 Azure 스토리지 유형을 사용할 수 있습니다. 선택은 운영 체제(Windows 또는 Linux) 및 배포 방법(독립 실행형 또는 고가용성)에 따라 달라집니다.

- Azure Managed Disks

  [관리 디스크](../../managed-disks-overview.md)는 Azure에서 관리하는 블록 수준 스토리지 볼륨입니다. SAP IQ 심플렉스 배포에 관리 디스크를 사용할 수 있습니다. 다양한 유형의 관리 디스크를 사용할 수 있지만 SAP IQ에는 [프리미엄 SSD](../../disks-types.md#premium-ssds)를 사용하는 것이 좋습니다. 

- Azure 공유 디스크

  [공유 디스크](../../disks-shared.md)는 여러 VM에 관리 디스크를 동시에 연결할 수 있게 해주는 Azure 관리 디스크의 새로운 기능입니다. 공유 관리 디스크는 기본적으로 SMB 또는 NFS를 통해 액세스할 수 있는 완전 관리형 파일 시스템을 제공하지 않습니다. 클러스터 노드 통신 및 쓰기 잠금을 처리하는 [Windows Server 장애 조치(Failover) 클러스터](https://github.com/MicrosoftDocs/windowsserverdocs/blob/master/WindowsServerDocs/failover-clustering/failover-clustering-overview.md)(WSFC)와 같은 클러스터 관리자를 사용해야 합니다. 
  
  Windows에서 SAP IQ 심플렉스 아키텍처에 대한 고가용성 솔루션을 배포하려면 WSFC에서 관리하는 두 노드 간에 Azure 공유 디스크를 사용할 수 있습니다. Azure 공유 디스크를 사용한 SAP IQ 배포 아키텍처는 [Windows Server에서 Azure 공유 디스크를 사용하여 SAP IQ NLS HA 솔루션 배포](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089) 문서에서 설명합니다.

- Azure NetApp Files

  Linux에서 SAP IQ 배포는 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)를 파일 시스템(NFS 프로토콜)으로 사용하여 독립 실행형 또는 고가용성 솔루션을 설치할 수 있습니다. 이 스토리지 제품을 일부 지역에서는 사용할 수 없습니다. 최신 정보는 [지역별 사용 가능 제품](https://azure.microsoft.com/global-infrastructure/services/) 웹 페이지를 참조하세요. Azure NetApp Files를 사용한 SAP IQ 배포 아키텍처는 [SUSE Linux Enterprise Server에서 Azure NetApp Files를 사용하여 SAP IQ-NLS HA 솔루션 배포](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172) 문서에서 설명합니다.

다음 표에는 운영 체제에 따른 각 스토리지 유형의 권장 사항이 나와 있습니다. 

| 스토리지 유형        | Windows | Linux |
| ------------------- | ------- | ----- |
| Azure Managed Disks | 예     | 예   |
| Azure 공유 디스크  | 예     | 아니요    |
| Azure NetApp Files  | 아니요      | 예   |

### <a name="networking"></a>네트워킹

Azure는 SAP IQ를 니어라인 스토리지로 사용하는 SAP BW 시스템으로 실현할 수 있는 모든 시나리오를 매핑할 수 있는 네트워크 인프라를 제공합니다. 이러한 시나리오에는 온-프레미스 시스템에 연결, 다른 가상 네트워크의 시스템에 연결 등이 포함됩니다. 자세한 내용은 [SAP 워크로드를 위한 Microsoft Azure 네트워킹](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking)을 참조하세요.

## <a name="deploy-sap-iq-on-windows"></a>Windows에 SAP IQ 배포

### <a name="server-preparation-and-installation"></a>소프트웨어 준비 및 설치

Windows에서 SAP IQ를 사용한 NLS 구현을 위해 서버를 준비하려면 [SAP Note 2780668 - SAP First Guidance - SAP IQ를 사용한 BW NLS 구현](https://launchpad.support.sap.com/#/notes/0002780668)에서 최신 정보를 얻을 수 있습니다. 여기에는 SAP BW 시스템 사전 요구 사항, SAP IQ 파일 시스템 레이아웃, 설치, 구성 후 작업, SAP BW NLS와 SAP IQ 통합에 대한 포괄적인 정보가 수록되어 있습니다.

### <a name="high-availability-deployment"></a>고가용성 배포

SAP IQ는 단순 및 다중 아키텍처를 모두 지원합니다. NLS 솔루션의 경우 단방향 서버 아키텍처만 사용 가능하고 평가됩니다. Simplex는 단일 가상 머신에서 실행되는 SAP IQ 서버의 단일 인스턴스입니다. 

기술적으로 멀티플렉스 서버 아키텍처를 사용하여 SAP IQ 고가용성을 달성할 수 있지만 멀티플렉스 아키텍처는 NLS 솔루션의 요구 사항을 충족하지 않습니다. 심플렉스 서버 아키텍처의 경우 SAP는 고가용성 구성에서 SAP IQ를 실행하기 위한 기능이나 절차를 제공하지 않습니다. 

Windows에서 심플렉스 서버 아키텍처에 대한 SAP IQ 고가용성을 설정하려면 Microsoft Windows Server 장애 조치(failover) 클러스터, 공유 디스크 같은 추가 구성이 필요한 사용자 지정 솔루션을 설정해야 합니다. Windows에서 SAP IQ에 대한 사용자 지정 솔루션 중 하나는 [Windows Server에서 Azure 공유 디스크를 사용하여 SAP IQ NLS HA 솔루션 배포](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-shared-disk-on-windows/ba-p/2433089)에 자세히 설명되어 있습니다.

### <a name="backup-and-restore"></a>백업 및 복원

Azure에서는 [SAP IQ 관리: 백업, 복원 및 데이터 복구](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html)에 설명된 대로 SAP IQ 데이터베이스 백업을 예약할 수 있습니다. SAP IQ는 다음과 같은 유형의 데이터베이스 백업을 제공합니다. [백업 시나리오](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html)에서 각 백업 유형에 대한 세부 정보를 찾을 수 있습니다.

- **전체 백업**: 데이터베이스의 전체 복사본을 만듭니다. 
- **증분 백업**: 모든 유형의 마지막 백업 이후의 모든 트랜잭션을 복사합니다. 
- **전체 백업 이후 증분**: 마지막 전체 백업 이후 데이터베이스의 모든 변경 사항을 백업합니다.
- **가상 백업**: SAP IQ 저장소의 테이블 데이터와 메타데이터를 제외한 모든 데이터베이스를 복사합니다.

SAP IQ 데이터베이스 크기에 따라 모든 백업 시나리오에서 데이터베이스 백업을 예약할 수 있습니다. 그러나 SAP에서 제공하는 NLS 인터페이스와 함께 SAP IQ를 사용하는 경우 SAP IQ 데이터베이스의 백업 프로세스를 자동화할 수 있습니다. 자동화를 사용하면 주 데이터베이스와 SAP IQ 데이터베이스 간에 이동되는 데이터의 손실 없이 SAP IQ 데이터베이스를 항상 일관된 상태로 복구할 수 있습니다. SAP IQ 니어라인 스토리지의 자동화 설정에 대한 자세한 내용은 [SAP Note 2741824 - SAP IQ 콜드 저장소/니어라인 스토리지의 백업 자동화 설정 방법](https://launchpad.support.sap.com/#/notes/2741824)을 참조하세요. 

대규모 SAP IQ 데이터베이스의 경우 가상 백업을 사용할 수 있습니다. 자세한 내용은 [가상 백업](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html), [SAP Sybase IQ의 가상 백업 소개](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ)를 참조하세요. 또한 [SAP Note 2461985 - 대규모 SAP IQ 데이터베이스를 백업하는 방법](https://launchpad.support.sap.com/#/notes/0002461985)도 참조하세요.

네트워크 드라이브(SMB 프로토콜)를 사용하여 Windows에서 SAP IQ 서버를 백업 및 복원하는 경우 백업에 UNC 경로를 사용해야 합니다. 백업 및 복원에 UNC 경로를 사용하는 경우 세 개의 백슬래시(`\\\`)가 필요합니다.

```sql
BACKUP DATABASE FULL TO '\\\sapiq.internal.contoso.net\sapiq-backup\backup\data\<filename>'
```

## <a name="deploy-sap-iq-on-linux"></a>Linux에 SAP IQ 배포

### <a name="server-preparation-and-installation"></a>소프트웨어 준비 및 설치

Linux에서 SAP IQ를 사용한 NLS 구현을 위해 서버를 준비하려면 [SAP Note 2780668 - SAP First Guidance - SAP IQ를 사용한 BW NLS 구현](https://launchpad.support.sap.com/#/notes/0002780668)에서 최신 정보를 얻을 수 있습니다. 여기에는 SAP BW 시스템 사전 요구 사항, SAP IQ 파일 시스템 레이아웃, 설치, 구성 후 작업, SAP BW NLS와 SAP IQ 통합에 대한 포괄적인 정보가 수록되어 있습니다.

### <a name="high-availability-deployment"></a>고가용성 배포

SAP IQ는 단순 및 다중 아키텍처를 모두 지원합니다. NLS 솔루션의 경우 단방향 서버 아키텍처만 사용 가능하고 평가됩니다. Simplex는 단일 가상 머신에서 실행되는 SAP IQ 서버의 단일 인스턴스입니다. 

기술적으로 멀티플렉스 서버 아키텍처를 사용하여 SAP IQ 고가용성을 달성할 수 있지만 멀티플렉스 아키텍처는 NLS 솔루션의 요구 사항을 충족하지 않습니다. 심플렉스 서버 아키텍처의 경우 SAP는 고가용성 구성에서 SAP IQ를 실행하기 위한 기능이나 절차를 제공하지 않습니다.

Linux에서 심플렉스 서버 아키텍처에 대한 SAP IQ 고가용성을 설정하려면 Pacemaker와 같은 추가 구성이 필요한 사용자 지정 솔루션을 설정해야 합니다. Linux의 SAP IQ에 대한 사용자 지정 솔루션 중 하나는 [SUSE Linux Enterprise Server에서 Azure NetApp Files를 사용하여 SAP IQ-NLS HA 솔루션 배포](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/deploy-sap-iq-nls-ha-solution-using-azure-netapp-files-on-suse/ba-p/1651172)에 자세히 설명되어 있습니다.

### <a name="backup-and-restore"></a>백업 및 복원

Azure에서는 [SAP IQ 관리: 백업, 복원 및 데이터 복구](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/5b8309b37f4e46b089465e380c24df59.html)에 설명된 대로 SAP IQ 데이터베이스 백업을 예약할 수 있습니다. SAP IQ는 다음과 같은 유형의 데이터베이스 백업을 제공합니다. [백업 시나리오](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880dc1f84f21015af84f1a6b629dd7a.html)에서 각 백업 유형에 대한 세부 정보를 찾을 수 있습니다.

- **전체 백업**: 데이터베이스의 전체 복사본을 만듭니다. 
- **증분 백업**: 모든 유형의 마지막 백업 이후의 모든 트랜잭션을 복사합니다. 
- **전체 백업 이후 증분**: 마지막 전체 백업 이후 데이터베이스의 모든 변경 사항을 백업합니다.
- **가상 백업**: SAP IQ 저장소의 테이블 데이터와 메타데이터를 제외한 모든 데이터베이스를 복사합니다.

SAP IQ 데이터베이스 크기에 따라 모든 백업 시나리오에서 데이터베이스 백업을 예약할 수 있습니다. 그러나 SAP에서 제공하는 NLS 인터페이스와 함께 SAP IQ를 사용하는 경우 SAP IQ 데이터베이스의 백업 프로세스를 자동화할 수 있습니다. 자동화를 사용하면 주 데이터베이스와 SAP IQ 데이터베이스 간에 이동되는 데이터의 손실 없이 SAP IQ 데이터베이스를 항상 일관된 상태로 복구할 수 있습니다. SAP IQ 니어라인 스토리지의 자동화 설정에 대한 자세한 내용은 [SAP Note 2741824 - SAP IQ 콜드 저장소/니어라인 스토리지의 백업 자동화 설정 방법](https://launchpad.support.sap.com/#/notes/2741824)을 참조하세요. 

대규모 SAP IQ 데이터베이스의 경우 가상 백업을 사용할 수 있습니다. 자세한 내용은 [가상 백업](https://help.sap.com/viewer/a893f37e84f210158511c41edb6a6367/16.1.4.7/a880672184f21015a08dceedc7d19776.html), [SAP Sybase IQ의 가상 백업 소개](https://wiki.scn.sap.com/wiki/display/SYBIQ/Introduction+Virtual+BackUp+(+general++back+up+method+)+in+SAP+Sybase+IQ)를 참조하세요. 또한 [SAP Note 2461985 - 대규모 SAP IQ 데이터베이스를 백업하는 방법](https://launchpad.support.sap.com/#/notes/0002461985)도 참조하세요.

## <a name="disaster-recovery"></a>재해 복구

이 섹션에서는 SAP IQ NLS 솔루션에 DR(재해 복구) 보호를 제공하는 전략에 대해 설명합니다. 이 내용은 전반적인 SAP DR 접근 방식의 기본 리소스를 보여 주는 [SAP에 대한 재해 복구 설정](../../../site-recovery/site-recovery-sap.md) 문서를 보완합니다. 이 문서에 설명된 프로세스는 추상적인 수준에서 제공됩니다. DR 전략은 정확한 단계의 유효성을 검사하고 철저히 테스트해야 합니다. 

SAP IQ의 경우 DR 환경을 안전하게 구현하는 방법을 설명하는 [SAP Note 2566083](https://launchpad.support.sap.com/#/notes/0002566083)을 참조하세요. Azure에서는 SAP IQ DR 전략에 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)를 사용할 수도 있습니다. SAP IQ DR에 대한 전략은 Azure에 배포되는 방식에 따라 다르며 SAP BW 시스템과도 일치해야 합니다. 

### <a name="standalone-deployment-of-sap-iq"></a>SAP IQ의 독립 실행형 배포

애플리케이션 수준 중복성 또는 고가용성을 포함하지 않는 독립 실행형 시스템으로 SAP IQ를 설치했지만 비즈니스에서 DR 설정을 필요로 하는 경우 가상 머신에 연결된 모든 디스크(Azure 관리 디스크)는 로컬이 됩니다. 
  
[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)를 사용하여 보조 지역에서 독립 실행형 SAP IQ 가상 머신을 복제할 수 있습니다. 보조 지역에 서버 및 연결된 모든 관리 디스크를 복제하므로 재해 또는 중단 발생 시 복제 환경으로 쉽게 장애 조치(failover)하여 작업을 계속할 수 있습니다. Azure DR 지역에 SAP IQ VM 복제를 시작하려면 [Azure에 가상 머신 복제](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md)의 지침을 따르세요. 

### <a name="highly-available-deployment-of-sap-iq"></a>SAP IQ의 고가용성 배포

SAP IQ 바이너리 및 데이터베이스 파일이 Azure 공유 디스크(Windows만 해당) 또는 Azure NetApp Files(Linux만 해당)와 같은 네트워크 드라이브에 있는 고가용성 시스템으로 SAP IQ를 설치한 경우 다음을 식별해야 합니다.

- DR 사이트에서 동일한 고가용성 SAP IQ 시스템이 필요한지 여부
- 독립 실행형 SAP IQ 인스턴스가 비즈니스 요구 사항에 충분한지 여부 
  
DR 사이트에 독립 실행형 SAP IQ 인스턴스가 필요한 경우 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)를 사용하여 보조 지역에서 주 SAP IQ 가상 머신을 복제할 수 있습니다. 서버 및 연결된 모든 로컬 관리 디스크를 보조 지역에 복제하지만 Azure 공유 디스크 또는 Azure NetApp Files와 같은 네트워크 드라이브는 복제하지 않습니다. 
  
Azure 공유 디스크 또는 네트워크 드라이브에서 데이터를 복사하려면 모든 파일 기반 복사 도구를 사용하여 Azure 지역 간에 데이터를 복제할 수 있습니다. 다른 지역의 Azure NetApp Files 볼륨을 복사하는 방법에 대한 자세한 내용은 [Azure NetApp Files에 대한 FAQ](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [다중 계층 SAP 앱 배포를 위한 재해 복구 설정](../../../site-recovery/site-recovery-sap.md)
- [SAP용 Azure Virtual Machines 계획 및 구현](planning-guide.md)
- [SAP용 Azure Virtual Machines 배포](deployment-guide.md)
