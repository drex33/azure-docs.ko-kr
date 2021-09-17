---
title: StorSimple 8000 시리즈 디바이스에서 데이터 마이그레이션 옵션
description: StorSimple 8000 시리즈에서 데이터를 마이그레이션하는 옵션에 대해 간략히 설명합니다.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 09/02/2021
ms.author: alkohli
ms.openlocfilehash: 863082490963b4a8d117a271588afcd0f86a4665
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123478683"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>StorSimple 8000 시리즈에서 데이터를 마이그레이션하는 옵션

> [!IMPORTANT]
> 2022년 12월이면 StorSimple 8000 시리즈는 지원 종료(EOS) 상태에 도달합니다. Microsoft는 이러한 디바이스의 하드웨어 및 소프트웨어를 더 이상 지원하지 않으며 서비스가 중단될 예정입니다.</br></br>
> StorSimple 8000 시리즈 고객은 이 문서에 설명된 대안 제품 중 하나로 마이그레이션하는 것이 좋습니다.

StorSimple 8000 시리즈에 대한 지원은 2022년 12월에 [종료](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)됩니다. StorSimple 8000 시리즈를 실행하고 있는 고객은 다른 Azure 자사 하이브리드 서비스로 업그레이드할 수 있습니다. 이 문서에서는 데이터를 마이그레이션하는 데 사용할 수 있는 Azure 하이브리드 옵션에 대해 설명합니다.

## <a name="migration-options"></a>마이그레이션 옵션

StorSimple 8000 시리즈를 사용하는 고객에게는 Azure 또는 타사 옵션이 있습니다.

### <a name="azure-options"></a>Azure 옵션

#### <a name="migrate-to-azure-file-sync"></a>Azure 파일 동기화로 마이그레이션

이 새로운 마이그레이션 옵션을 사용하면 고객이 조직의 파일 공유를 Azure Files에 저장할 수 있습니다. 그러면 이러한 파일 공유에서 AFS(Azure 파일 동기화)를 사용하여 온-프레미스 액세스를 위해 중앙 집중화됩니다. AFS는 Windows Server 호스트에 배포할 수 있습니다. 그러면 실제 데이터 마이그레이션이 호스트 복사본으로 수행되거나 마이그레이션 도구를 사용하여 수행됩니다.

데이터를 Azure 파일 동기화로 마이그레이션하는 방법에 대한 자세한 내용은 [Azure 파일 동기화로 StorSimple 8100 및 8600 마이그레이션](../storage/files/storage-files-migration-storsimple-8000.md)을 참조하세요.

#### <a name="migrate-to-azure-netapp-files"></a>Azure NetApp Files 마이그레이션

StorSimple 8000 시리즈 고객은 NetApp GFC(전역 파일 캐시)와 연결된 ANF(Azure NetApp Files)로 마이그레이션하여 원격 사이트에서 콘텐츠를 유지하면서 Azure에 중요한 데이터를 계속 저장할 수 있습니다. 고객은 Azure NetApp Files를 사용하여 Microsoft Azure에서 구조화되지 않은 데이터를 중앙 집중화하여 NETApp 전역 파일 캐시를 통해 빠른 로컬 액세스 및 지리적으로 분산된 액세스를 제공함으로써 IT 스토리지 및 인프라를 능률화하고 간소화할 수 있습니다.

기능, 배포 방법 및 마이그레이션에 대한 개요는 NetApp의 [참조 아키텍처: Azure NetApp Files 및 NetApp 전역 파일 캐시를 사용하여 전역적으로 분산된 Enterprise 파일 공유](https://f.hubspotusercontent20.net/hubfs/525875/r3_NA-581-0521-Ref-Arch-ANF-GFC-StorSimple%20(1)%20(1)%20(2).pdf)를 참조하세요.<!--Not included: 1) Partnership with MS; 2) How to initiate migration with NetApp (other providers point people to their site).-->

### <a name="third-party-options"></a>타사 옵션

#### <a name="migrate-to-panzura-freedom-nas"></a>Panzura Freedom NAS로 마이그레이션

StorSimple 5000-7000 시리즈와 StorSimple 8000 시리즈 고객은 Azure에서 데이터를 유지하기 위해 Panzura Freedom NAS로 마이그레이션하도록 선택할 수 있습니다. Panzura Freedom 솔루션은 데이터 센터, 사무실, 공용 및 프라이빗 클라우드를 포괄한 NAS 솔루션을 제공합니다. 솔루션을 통해 NFS, SMB 및 모바일 클라이언트에 대한 로컬, 하이브리드 및 클라우드 내 데이터 워크플로를 활성화할 수 있습니다.

이 마이그레이션은 Panzura에서 지원하며, 고객은 [Panzura 웹 사이트](https://panzura.com/migrate-storsimple-panzura/)에서 마이그레이션 지원을 요청하여 시작할 수 있습니다.

#### <a name="migrate-to-nasuni"></a>Nasuni로 마이그레이션

Nasuni를 사용하면 전체 StorSimple 환경을 안정적이고 안전한 고성능 파일 서비스 플랫폼으로 이동할 수 있습니다. Nasuni는 온-프레미스 파일 저장소의 보안 및 성능을 제공하는 동시에 Azure의 확장성 및 내구성과 결합합니다. 최고의 Azure ISV(독립 소프트웨어 공급업체)로서, Nasuni는 StorSimple 데이터를 여러 위치에서 공유하고 공동 작업할 수 있는 최신 플랫폼으로 이동하는 데 필요한 모든 도구를 제공합니다.

지금 시작: [Nasuni 웹 사이트](https://info.nasuni.com/storsimple8000-webinar).

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>마이그레이션 - 질문과 대답

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>17. StorSimple 8000 시리즈 디바이스에 대한 서비스는 언제 종료되나요?

A. StorSimple 8000 시리즈에 대한 지원은 2022년 12월에 [종료](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)됩니다. 지원이 종료되면 Microsoft는 더 이상 이러한 디바이스의 하드웨어 및 소프트웨어를 지원할 수 없게 됩니다. 여기에는 서비스 지원 및 소프트웨어 패치/보안 패치가 포함되며 서비스 자체는 중단될 예정입니다. 이제는 디바이스에서 데이터를 마이그레이션하도록 계획하는 것이 좋습니다.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>17. Azure에 저장된 데이터는 어떻게 되나요?  

A. 새 서비스로 마이그레이션한 후에도 Azure에서 데이터를 계속 사용할 수 있습니다.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>17. StorSimple 디바이스에 로컬로 저장된 데이터는 어떻게 되나요?

A. 마이그레이션 문서에서 설명한 대로 로컬 디바이스에 있는 데이터는 새 서비스에 복사할 수 있습니다.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>17. StorSimple 8000 시리즈 어플라이언스를 유지할 경우 어떻게 되나요?

A. Microsoft는 더 이상 하드웨어 및 소프트웨어 지원을 제공하지 않습니다. StorSimple 서비스가 작동하지 않습니다. 비즈니스 연속성을 위해 마이그레이션을 사용하는 것이 좋습니다.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>17. StorSimple 8000 시리즈 디바이스에서 데이터를 마이그레이션하는 데 사용할 수 있는 옵션은 무엇인가요?

A. StorSimple 8000 시리즈 사용자는 시나리오에 따라 다음과 같은 마이그레이션 옵션을 사용할 수 있습니다.

* **Azure 파일 동기화로 마이그레이션**: Azure 원시 형식으로 전환하려면 이 옵션을 사용합니다. Azure 파일 동기화는 파일 공유를 중앙 집중식으로 관리하는 데 사용할 수 있습니다.

* **다른 옵션**: Microsoft 지원에 문의하여 여기서 설명하지 않은 마이그레이션 옵션에 대해 논의할 수 있습니다.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>17. 다른 스토리지 솔루션으로 마이그레이션할 수 있나요?

A. 예. 데이터의 호스트 복사본을 사용하여 다른 스토리지 솔루션으로 마이그레이션할 수 있습니다.

### <a name="q-is-migration-supported-by-microsoft"></a>17. Microsoft에서 마이그레이션을 지원하나요?

A. 8000 시리즈에서 마이그레이션하는 작업은 완벽하게 지원됩니다. 실제로는 먼저 고객 지원팀에 문의한 후에 마이그레이션을 시작하는 것이 좋습니다. 마이그레이션은 현재 지원형 작업입니다. StorSimple 8000 시리즈 디바이스에서 데이터를 마이그레이션하려면 [StorSimple 고객 지원팀에 문의하세요](mailto:storsimp@microsoft.com).

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>17. Azure 파일 동기화로 마이그레이션하기 위한 가격 책정 모델은 무엇인가요?

A. Azure 파일 동기화를 사용하는 경우 서비스 구독 요금이 적용될 수 있습니다. 고객은 진행 중인 스토리지 비용을 지불해야 합니다. 예측에 대한 자세한 내용은 [AFS 가격]( https://azure.microsoft.com/pricing/details/storage/files/)을 참조하세요.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>17. 마이그레이션을 완료하는 데 얼마나 오래 걸리나요?

A. 데이터 마이그레이션을 완료하는 데 걸리는 시간은 선택한 데이터의 양과 선택한 업그레이드 옵션에 따라 다릅니다.

## <a name="next-steps"></a>다음 단계

* [StorSimple 8000 시리즈에서 Azure 파일 동기화로 데이터 마이그레이션](../storage/files/storage-files-migration-storsimple-8000.md)