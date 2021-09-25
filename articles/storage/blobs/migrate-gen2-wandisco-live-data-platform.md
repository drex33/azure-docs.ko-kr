---
title: Azure Data Lake Storage 및 WANdisco LiveData Platform for Azure(미리 보기)
description: WANdisco LiveData Platform for Azure를 사용하여 온-프레미스 Hadoop 데이터를 Azure Data Lake Storage Gen2로 마이그레이션합니다.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/17/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: cee20d5104ceb09dc0ba3d826b28108e543cc3d1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664906"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>WANdisco LiveData Platform for Azure(미리 보기)를 사용하여 까다로운 마이그레이션 요구 사항 충족

[WANdisco LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/)를 사용하여 온-프레미스 Hadoop 데이터를 Azure Data Lake Storage Gen2로 마이그레이션합니다. 이 플랫폼은 애플리케이션 가동 중지 시간과 데이터 손실 가능성을 제거하며 작업이 온-프레미스로 계속되는 동안에도 데이터 일관성을 유지합니다.

> [!NOTE]
> WANdisco LiveData Platform for Azure는 공개 미리 보기로 제공됩니다. 지역별 가용성은 [지원되는 지역](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions)을 참조하세요.

플랫폼은 온-프레미스 환경에서 Azure 스토리지로 활발하게 사용되는 데이터를 마이그레이션하는 [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure)와 모든 수정된 데이터 또는 수집된 데이터가 일관되게 복제될 것을 보장하는 [LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure)의 두 가지 서비스로 구성됩니다.

> [!div class="mx-imgBorder"]
> ![라이브 데이터 플랫폼 개요 설명](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Azure Portal 및 Azure CLI를 사용하여 두 서비스를 관리할 수 있으며, 다른 모든 Azure 서비스와 같이 요금제 및 종량제 청구 모델을 따릅니다. LiveData Platform for Azure에 대한 소비는 동일한 월간 Azure 청구서에 표시되며, 사용 현황을 추적하고 모니터링하는 일관되고 편리한 방법을 제공합니다.

[정적 정보를 Azure Data Box로 복사](./data-lake-storage-migrate-on-premises-hdfs-cluster.md)하거나 [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html)와 같은 Hadoop 도구를 사용하여 *오프라인* 으로 데이터를 마이그레이션하는 것과는 달리, WANdisco LiveData for Azure를 사용하여 *온라인* 마이그레이션 중에 비즈니스 시스템의 전체 작업을 유지할 수 있습니다. 데이터를 Azure로 이동하는 동안에도 빅 데이터 환경이 계속 작동하도록 합니다.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>WANdisco LiveData Platform for Azure의 주요 기능

[WANdisco LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/)는 고유한 광역 네트워크를 지원하는 합의 엔진을 사용하여 데이터 일관성을 구현하고 애플리케이션이 복제 중인 데이터를 계속 수정할 수 있게 하는 동안 대규모 데이터 복제를 수행합니다. <br><br>

> [!VIDEO https://www.youtube.com/embed/KRrmcYPxEho]

플랫폼의 주요 기능에는 다음이 포함됩니다.

- **데이터 일관성**: 환경 간에 대용량 데이터를 마이그레이션하고 스토리지 시스템 처리량 마이그레이션 전반에 걸쳐 이러한 데이터를 일관성 있게 유지할 수 있도록 합니다. WANdisco의 광역 네트워크 지원 합의 엔진을 Azure에서 직접 사용하여 데이터 일관성을 구현하고 데이터 변경 전반에 걸쳐 일관성을 보장하여 데이터를 마이그레이션합니다.

- **운영 유지 관리**: 애플리케이션은 마이그레이션 중에 데이터를 계속 생성, 수정, 읽기 및 삭제할 수 있으므로 빅 데이터를 Azure로 마이그레이션하기 위해 비즈니스 운영을 중단하거나 중단 기간을 도입할 필요가 없습니다. 애플리케이션, 분석 인프라, 수집 작업 및 기타 처리를 계속 수행합니다.

- **결과 유효성 검사**: Azure로 마이그레이션된 후 데이터를 효과적으로 사용할 수 있는지에 대한 엔드투엔드 검증을 수행하려면 해당 데이터에 대해 프로덕션 애플리케이션 워크로드를 실행해야 합니다. 오직 LiveData 서비스만 마이그레이션의 원본 또는 대상에서 변경이 발생하는지 여부에 관계없이 데이터 일관성을 유지하여 데이터 분기의 위험을 초래하지 않고 이를 제공합니다. 프로세스 및 시스템에 대한 변경이나 위험 없이 애플리케이션 동작을 테스트하고 유효성을 검사합니다.

- **복잡성 감소**: 자동화를 통해 데이터를 마이그레이션하여 데이터를 복사하기 위해 예약된 작업을 만들고 관리할 필요가 없습니다. Azure와의 긴밀한 통합을 컨트롤 플레인으로 사용하여 선택적 데이터 복제, Hive 메타데이터, 데이터 보안 및 기밀성을 비롯한 마이그레이션 진행 상황을 관리하고 모니터링합니다.

- **효율**: 높은 처리량과 성능을 유지하고 빅 데이터 볼륨으로 쉽게 확장할 수 있습니다. 대역폭 소비를 제어하면 다른 시스템 작업에 영향을 주지 않고 마이그레이션 목표를 충족할 수 있습니다.

## <a name="migrate-big-data-faster-without-risk"></a>위험 없이 빅 데이터를 더 빠르게 마이그레이션

WANdisco LiveData Platform for Azure의 첫 번째 서비스는 [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure)입니다. 온-프레미스 환경에서 Azure 스토리지로 활발하게 사용되는 데이터를 마이그레이션하기 위한 솔루션입니다. LiveData Migrator for Azure는 완전히 Azure Portal 또는 Azure CLI에서 프로비저닝되고 관리되며, 데이터 마이그레이션을 즉시 시작하기 위한 구성 변경, 애플리케이션 수정 또는 서비스 다시 시작 없이 온-프레미스의 Hadoop 클러스터와 함께 작동합니다.

> [!div class="mx-imgBorder"]
> ![LiveData Migrator for Azure 아키텍처](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

빅 데이터 마이그레이션은 복잡하고 어려울 수 있습니다. 비즈니스 작업을 중단하지 않고 페타바이트 단위의 정보를 이동하는 것은 오프라인 데이터 복사 기술로는 불가능했었습니다. [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure)는 간단한 배포를 제공하며 애플리케이션이 마이그레이션되는 데이터를 읽고, 쓰고, 수정하는 동안 지속적인 데이터 마이그레이션 및 복제를 통해 LiveData 서비스를 구축할 수 있습니다.

마이그레이션은 다음과 같은 세 단계로 간단히 수행됩니다.

1. Azure Portal에서 온-프레미스 Hadoop 클러스터로 LiveData Migrator 인스턴스를 프로비저닝합니다. 클러스터 변경 또는 가동 중지 시간이 필요하지 않으며 애플리케이션은 계속 작동할 수 있습니다.

   > [!div class="mx-imgBorder"]
   > ![LiveData Migrator 인스턴스 만들기](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. Azure Data Lake Storage Gen2를 사용하도록 설정한 대상 스토리지 계정을 정의합니다.

   > [!div class="mx-imgBorder"]
   > ![LiveData Migrator 대상 만들기](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. 마이그레이션할 데이터의 위치를 정의하고(예: `/user/hive/warehouse`) 마이그레이션을 시작합니다.

   > [!div class="mx-imgBorder"]
   > ![LiveData Migrator 마이그레이션 만들기](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

Azure CLI 및 Azure Portal을 비롯한 표준 Azure 도구를 통해 마이그레이션 진행 상황을 모니터링하고 온-프레미스 환경을 전체적으로 계속 사용합니다. 시작하기 전에 다음 [필수 구성 요소](https://docs.wandisco.com/live-data-platform/docs/prereq/)를 검토합니다.

## <a name="replicate-data-under-active-change"></a>활성 변경에서 데이터 복제

온-프레미스 데이터 레이크에서 Azure로 대규모 마이그레이션을 수행하려면 애플리케이션 테스트 및 유효성 검사가 필요합니다. 쉽게 조정할 수 없는 여러 가지 원인을 만드는 데이터 변경 사항을 도입하는 위험 없이 이 작업을 수행할 수 있는 것은 위험을 제거하고 Azure로 이동하는 비용을 최소화하는 데 매우 중요합니다. [LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure)는 WANdisco의 조정 엔진 기술을 사용하여 이러한 문제를 해결합니다.

> [!div class="mx-imgBorder"]
> ![LiveData Plane for Azure 아키텍처](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

초기 마이그레이션 후 LiveData Plane for Azure을 통해 온-프레미스 Hadoop 클러스터 및 Azure Storage에서 데이터를 일관되게 유지합니다.

1. Azure Portal에서 시작하여 온-프레미스 및 Azure에서 LiveData Plane for Azure를 프로비저닝합니다. 애플리케이션을 변경할 필요가 없습니다.

2. 일관성을 유지하려는 데이터 위치를 포괄하는 복제 규칙을 구성합니다(예: `/user/contoso/sales/region/WA`).

3. 필요에 따라 Hadoop 호환 파일 시스템으로 어느 위치의 데이터에 액세스하고 이를 수정하는 애플리케이션을 실행합니다.

LiveData Plane for Azure는 클러스터 작업 또는 애플리케이션 성능에 상당한 오버헤드를 부과하지 않고 데이터를 일관되게 유지합니다. 모든 변경 내용이 일관되게 복제되는 동안 데이터를 수정하거나 수집합니다.

## <a name="next-steps"></a>다음 단계

- Azure용 [LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/)는 다른 Azure 리소스처럼 사용되며 현재 미리 보기로 제공됩니다.

- [필수 구성 요소](https://docs.wandisco.com/live-data-platform/docs/prereq/)를 파악하고 마이그레이션을 계획하여 LiveData Migrator for Azure를 통해 대규모 마이그레이션을 신속하게 완료합니다.

- [HDFS 샌드박스](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/)를 사용하여 온-프레미스 Hadoop 클러스터 없이 LiveData Migrator를 사용해 보세요.

## <a name="see-also"></a>참고 항목

- [Azure Marketplace의 LiveData Migrator for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [Azure Marketplace의 LiveData Plane for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [LiveData Migrator for Azure 플랜 및 가격 책정](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [LiveData Plane for Azure 플랜 및 가격 책정](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice)

- [LiveData Platform for Azure FAQ](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [LiveData Platform for Azure의 알려진 문제](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Azure Data Lake Storage Gen2 소개](data-lake-storage-introduction.md)
