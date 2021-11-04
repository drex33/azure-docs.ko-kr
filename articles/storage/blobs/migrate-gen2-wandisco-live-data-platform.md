---
title: Azure에 대 한 Data Lake Storage 및 WANdisco 사용 중인 데이터 플랫폼
description: 데이터 작업을 중단 하거나 가동 중지 시간을 요구 하지 않고 온-프레미스 Hadoop 데이터의 페타바이트를 Azure Data Lake Storage Gen2 파일 시스템으로 마이그레이션하는 방법에 대해 알아봅니다.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 10/26/2021
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: b0b78d1233d6e605691e06c69c9d2167c7ecb592
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019223"
---
# <a name="migrate-on-premises-hadoop-data-to-azure-data-lake-storage-gen2-with-wandisco-livedata-platform-for-azure"></a>Azure 용 WANdisco 사용 중인 데이터 Platform을 사용 하 여 온-프레미스 Hadoop 데이터를 Azure Data Lake Storage Gen2로 마이그레이션

[WANdisco 사용 중인 데이터 Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) 는 데이터 작업 Azure Data Lake Storage Gen2을 중단 하거나 가동 중지 시간을 요구 하지 않고 온-프레미스 Hadoop 데이터의 페타바이트를 파일 시스템으로 마이그레이션합니다. 플랫폼의 연속 검사는 수정이 발생 하더라도 데이터가 손실 되지 않도록 방지 하는 동시에 데이터가 손실 되지 않도록 합니다.

플랫폼은 두 개의 서비스로 구성 됩니다. [Azure 용 사용 중인 데이터 Migrator](https://www.wandisco.com/products/livedata-migrator-for-azure) 는 온-프레미스 환경에서 azure storage로 적극적으로 사용 되는 데이터를 마이그레이션하고, [Azure 용 사용 중인 데이터 평면은](https://www.wandisco.com/products/livedata-plane-for-azure) 모든 수정 된 데이터 또는 수집 데이터가 일관 되 게 복제 되도록 합니다.

> [!div class="mx-imgBorder"]
> ![라이브 데이터 플랫폼 개요 그림](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Azure Portal 및 Azure CLI를 사용 하 여 두 서비스를 관리 합니다. 각 서비스는 다른 모든 Azure 서비스와 동일한 요금제 (종 량 제) 청구 모델을 따릅니다. Azure 용 사용 중인 데이터 Platform의 데이터 사용은 월간 Azure 청구서에 표시 되므로 사용 메트릭을 제공 합니다.

[정적 정보를 Azure Data Box로 복사](./data-lake-storage-migrate-on-premises-hdfs-cluster.md)하거나 [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html)와 같은 Hadoop 도구를 사용하여 *오프라인* 으로 데이터를 마이그레이션하는 것과는 달리, WANdisco LiveData for Azure를 사용하여 *온라인* 마이그레이션 중에 비즈니스 시스템의 전체 작업을 유지할 수 있습니다. 데이터를 Azure로 이동하는 동안에도 빅 데이터 환경이 계속 작동하도록 합니다.

## <a name="key-benefits-of-wandisco-livedata-platform-for-azure"></a>Azure 용 WANdisco 사용 중인 데이터 Platform의 주요 이점

Azure의 광역 네트워크를 지 원하는 [WANdisco 사용 중인 데이터 플랫폼](https://docs.wandisco.com/live-data-platform/docs/landing/)은 데이터 일관성을 달성 하 고 대규모로 실시간 데이터 복제를 수행 합니다. 자세한 내용은 다음 비디오를 참조 하세요.<br><br>

>[!VIDEO https://www.youtube.com/embed/KRrmcYPxEho]

플랫폼의 주요 이점은 다음과 같습니다.

- **데이터 정확도**: 데이터의 종단 간 유효성 검사를 통해 데이터 손실을 방지 하 고 전송 된 데이터를 사용할 수 있도록 합니다.

- **데이터 일관성**: 연속 변경에도 불구 하 고 데이터 볼륨을 환경 간에 자동으로 일관 되 게 유지 합니다.

- **데이터 효율성**: 대역폭 사용량에 대 한 모든 권한으로 대용량 데이터 볼륨을 지속적으로 전송 합니다.

- **가동 중지 시간 소거**: Azure에 데이터를 추가 하는 동안 비즈니스 작업을 중단 하지 않고도 마이그레이션하는 동안 다른 응용 프로그램에서 데이터를 자유롭게 만들고, 수정 하 고, 읽고, 삭제할 수 있습니다. 애플리케이션, 분석 인프라, 수집 작업 및 기타 처리를 계속 수행합니다.

- **간단한 사용**: 플랫폼의 Azure 통합을 사용 하 여 자동화 된 마이그레이션의 진행률을 만들고, 구성 하 고, 예약 하 고, 추적 합니다. 또한 필요에 따라 선택적 데이터 복제, Hive 메타 데이터, 데이터 보안 및 기밀성을 구성 합니다.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>WANdisco LiveData Platform for Azure의 주요 기능

플랫폼의 주요 기능에는 다음이 포함됩니다.

- **메타 데이터 마이그레이션**: HDFS 데이터 외에 Azure 용 사용 중인 데이터 Migrator를 사용 하 여 Hive 및 기타 저장소에서 메타 데이터를 마이그레이션합니다.

- **예약 된 전송**: Azure 용 사용 중인 데이터 Migrator를 사용 하 여 데이터 전송이 시작 될 때 제어 하 고 자동화 하 여 데이터 변경 내용을 수동으로 마이그레이션할 필요가 없습니다.

- **Kerberos**: 사용 중인 데이터 Migrator for Azure는 kerberize 되었다면 클러스터를 지원 합니다.

- **제외 템플릿**: 특정 파일 크기나 파일 이름 (glob 패턴을 사용 하 여 정의 됨)이 대상 저장소로 마이그레이션되지 않도록 Azure에 대 한 사용 중인 데이터 Migrator에서 규칙을 만듭니다. Azure Portal 또는 CLI를 사용 하 여 제외 템플릿을 만들고 원하는 수 만큼의 마이그레이션에 적용 합니다.

- **경로 매핑**: 지정 된 디렉터리에 전송 된 데이터를 자동으로 이동 하는 특정 대상 파일 시스템에 대 한 대체 대상 경로를 정의 합니다.

- **대역폭 관리**: Azure에 대 한 사용 중인 데이터 Migrator의 최대 네트워크 대역폭 크기를 구성 하 여 사용량에 대 한 대역폭을 방지 합니다.

- **제외**: 조건에 맞는 파일 및 디렉터리의 마이그레이션을 방해 하는 템플릿 쿼리를 정의 하 여 원본 시스템에서 데이터를 선택적으로 마이그레이션할 수 있습니다.

- **메트릭**: 시간이 지남에 따라 전송 되는 파일, 제외 된 경로, 전송에 실패 한 항목 등 Azure에 대 한 사용 중인 데이터 Migrator의 데이터 전송에 대 한 세부 정보를 확인 합니다.


   > [!div class="mx-imgBorder"]
   > ![사용 중인 데이터](./media/migrate-gen2-wandisco-live-data-platform/metrics-1.png)

## <a name="migrate-big-data-faster-without-risk"></a>위험 없이 빅 데이터를 더 빠르게 마이그레이션

WANdisco 사용 중인 데이터 Platform for Azure에 포함 된 첫 번째 서비스는 온-프레미스 환경에서 Azure Storage로 데이터를 마이그레이션하는 [azure 용 사용 중인 데이터 Migrator](https://www.wandisco.com/products/livedata-migrator-for-azure)입니다. 사용 중인 데이터 Migrator를 온-프레미스 Hadoop 클러스터에 배포한 후에는 파일 시스템에 대 한 최상의 구성을 자동으로 만듭니다. 여기에서 시스템에 대 한 Kerberos 세부 정보를 제공 합니다. 사용 중인 데이터 Migrator for Azure는 데이터를 Azure Storage 마이그레이션할 준비가 됩니다.

> [!div class="mx-imgBorder"]
> ![LiveData Migrator for Azure 아키텍처](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture-1.png)

Azure 용 사용 중인 데이터 Migrator를 시작 하기 전에 이러한 [필수 구성 요소](https://docs.wandisco.com/live-data-platform/docs/prereq/)를 검토 합니다.

마이그레이션을 수행 하려면 다음을 수행 합니다.

1. Azure CLI에서 다음을 수행 합니다. 

   - 을 실행 하 여 Azure CLI에서 WANdisco 리소스 공급자에 등록 `az provider register --namespace Wandisco.Fusion --consent-to-permissions` 합니다.
   - 을 실행 하 여 사용 중인 데이터 플랫폼의 요금제 청구 약관에 동의 합니다 `az vm image terms accept --offer ldma --plan metered-v1 --publisher Wandisco --subscription <subscriptionID>` .

2. Azure Portal에서 온-프레미스 Hadoop 클러스터로 사용 중인 데이터 Migrator 인스턴스를 배포 합니다. 클러스터를 변경 하거나 다시 시작할 필요는 없습니다.

   > [!div class="mx-imgBorder"]
   > ![LiveData Migrator 인스턴스 만들기](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

   > [!NOTE]
   > WANdisco 사용 중인 데이터 Migrator for Azure는 Hadoop 테스트 클러스터를 만드는 옵션을 제공 합니다.

3. 해당 하는 경우 Kerberos 세부 정보를 구성 합니다.

4. Azure Data Lake Storage Gen2를 사용하도록 설정한 대상 스토리지 계정을 정의합니다.

   > [!div class="mx-imgBorder"]
   > ![LiveData Migrator 대상 만들기](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

5. 마이그레이션할 데이터의 위치를 정의 합니다 (예:) `/user/hive/warehouse` .

   > [!div class="mx-imgBorder"]
   > ![LiveData Migrator 마이그레이션 만들기](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

6. 마이그레이션 시작

Azure CLI 및 Azure Portal를 비롯 한 표준 Azure 도구를 통해 마이그레이션 진행 상황을 모니터링 합니다.

자세한 지침은 [사용 중인 데이터 Migrator For Azure How-To video 시리즈](https://fast.wistia.com/embed/channel/qg51p8erky)를 참조 하세요.

## <a name="bidirectionally-replicate-data-under-active-change-with-livedata-plane-for-azure"></a>Azure에 대 한 사용 중인 데이터 평면을 사용 하 여 활성 변경 양방향으로 데이터를 복제 합니다.

사용 중인 데이터 플랫폼에 포함 된 두 번째 서비스는 [Azure에 대 한 사용 중인 데이터 평면](https://www.wandisco.com/products/livedata-plane-for-azure)입니다. 사용 중인 데이터 평면은 WANdisco의 조정 엔진을 사용 하 여 여러 온-프레미스 Hadoop 클러스터에서 데이터 일관성을 유지 하 Azure Storage 고, 모든 시스템의 데이터에 변경 내용을 지능적으로 적용 하 여 서로 다른 사용 지점에서 데이터 충돌 위험을 제거 합니다.

> [!div class="mx-imgBorder"]
> ![LiveData Plane for Azure 아키텍처](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

초기 마이그레이션 후 Azure에 대 한 사용 중인 데이터 평면과 데이터의 일관성을 유지 합니다.

1. Azure Portal에서 시작 하 여 azure 온-프레미스 및 Azure에 대 한 사용 중인 데이터 평면을 배포 합니다. 애플리케이션을 변경할 필요가 없습니다.

2. 일관 되 게 유지 하려는 데이터 위치를 포함 하는 복제 규칙을 구성 합니다 (예:) `/user/contoso/sales/region/WA` .

3. 필요에 따라 두 위치 중 하나에서 데이터에 액세스 하 고 수정 하는 응용 프로그램을 실행 합니다.

Azure 용 사용 중인 데이터 평면은 클러스터 작업 또는 응용 프로그램 성능에 상당한 영향 없이 모든 환경에서 데이터 변경 내용을 일관 되 게 복제 합니다.

## <a name="test-drive-or-trial"></a>시험 또는 시험

[Azure의 Marketplace에 대 한 사용 중인 데이터 플랫폼](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=Overview)에서 다음 두 가지 옵션을 사용할 수 있습니다.

- **지금 가져오기** 단추는 구독에서 서비스를 시작 합니다. 여기에서 사용자 고유의 Hadoop 클러스터 또는 WANdisco의 평가판 클러스터를 사용할 수 있습니다.

- **테스트 드라이브** 를 클릭 하 여 미리 구성 되 고 호스팅되는 환경에서 사용 중인 데이터 Migrator for Azure를 테스트 합니다. 이를 통해 데이터에 대 한 비용 또는 위험 없이 구독에 추가 하기 전에 Azure에 대 한 사용 중인 데이터 Migrator을 시도할 수 있습니다.

테스트 드라이브 [데모 비디오](https://fast.wistia.net/embed/channel/qg51p8erky?wchannelid=qg51p8erky&wmediaid=ute6gsc60w) 를 시청 하 여 작동 중인 테스트 드라이브를 확인 합니다.

## <a name="next-steps"></a>다음 단계

- [사용 중인 데이터 Migrator For Azure에서 마이그레이션을 계획 하 고 만듭니다](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma).

## <a name="see-also"></a>참고 항목

- [Azure Marketplace의 LiveData Migrator for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=Overview)

- [LiveData Migrator for Azure 플랜 및 가격 책정](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=PlansAndPricee)

- [LiveData Platform for Azure FAQ](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [LiveData Platform for Azure의 알려진 문제](https://docs.wandisco.com/live-data-platform/docs/known-issues/)
