---
title: Komprise Intelligent Data Manager를 사용하여 파일 데이터를 분석하고 Azure로 마이그레이션
titleSuffix: Azure Storage
description: Komprise Intelligent Data Manager를 구현하기 위한 시작 가이드입니다. 이 가이드에서는 파일 인프라를 분석하고 데이터를 Azure Files, Azure NetApp Files, Azure Blob Storage 또는 사용 가능한 ISV NAS 솔루션으로 마이그레이션하는 방법을 보여줍니다.
author: dukicn
ms.author: nikoduki
ms.date: 05/20/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: a6333ef4385439afa2e2f0000ae3f452357aa958
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856823"
---
# <a name="analyze-and-migrate-to-azure-with-komprise"></a>Komprise를 사용하여 데이터를 분석하고 Azure로 마이그레이션

이 문서를 참조 하 여 Azure storage 서비스와 Komprise Intelligent 데이터 관리 인프라를 통합할 수 있습니다. 이 문서에는 데이터를 분석하고 마이그레이션하는 방법에 대한 고려 사항 및 구현 지침이 포함되어 있습니다.

Komprise는 파일에 대 한 분석 및 통찰력을 제공 하 고, 네트워크 연결 저장소 시스템 (NAS) 및 개체 저장소에 저장 된 개체 데이터를 온-프레미스와 클라우드에 제공 합니다.  Azure Files, Azure NetApp Files, Azure Blob Storage 또는 다른 ISV NAS 솔루션과 같은 Azure storage 서비스로 데이터를 마이그레이션할 수 있습니다. [기본 및 보조 스토리지에 대해 검증된 파트너 솔루션](../primary-secondary-storage/partner-overview.md)에 대해 자세히 알아보세요.

Kom의 Se에 대 한 일반적인 사용 사례는 다음과 같습니다.

- 구조화 되지 않은 파일 및 개체 데이터의 분석을 통해 데이터 관리, 이동, 위치 지정, 보관, 보호 및 confinement에 대 한 통찰력 얻기
- Azure Files, Azure NetApp Files 또는 ISV NAS 솔루션으로 파일 데이터 마이그레이션
- 원본 NAS 솔루션에서 투명 한 액세스를 유지 하 고 Azure에서 네이티브 개체에 액세스할 수 있도록 하는 동시에 Azure Blob Storage 파일 데이터에 대 한 정책 기반 계층화 및 보관
- Azure에서 네이티브 개체 액세스를 유지 하면서 구성 가능한 일정에 Azure Blob Storage 파일 데이터 복사
- 개체 데이터를 Azure Blob Storage로 마이그레이션
- 마지막 액세스 시간을 기반으로 Azure Blob Storage 핫, 쿨 및 보관 계층에서 개체의 계층화 및 데이터 수명 주기 관리

## <a name="reference-architecture"></a>참조 아키텍처

다음 다이어그램에서는 온-프레미스에서 Azure로의 배포 및 Azure 내 배포에 대한 참조 아키텍처를 제공합니다.

:::image type="content" source="./media/komprise-quick-start-guide/komprise-architecture.png" alt-text="Komprise Intelligent Data Manager의 기본 설정을 설명하는 참조 아키텍처":::

다음 다이어그램에서는 클라우드 및 온-프레미스 개체 작업을 Azure Blob Storage로 마이그레이션하기 위한 참조 아키텍처를 제공 합니다.

:::image type="content" source="./media/komprise-quick-start-guide/komprise-architecture-blob.png" alt-text="참조 아키텍처는 클라우드 및 온-프레미스 개체 작업을 Azure Blob Storage로 마이그레이션하기 위한 설정에 대해 설명 합니다.":::

Komprise는 가상 환경에 쉽게 배포할 수 있는 소프트웨어 솔루션입니다. 이 솔루션은 다음으로 구성됩니다.
- **Director** - Komprise Grid의 관리 콘솔입니다. 환경을 구성하고, 활동을 모니터링하고, 보고서와 그래프를 보고, 정책을 설정하는 데 사용됩니다.
- **관찰자** -공유를 관리 및 분석 하 고, 보고서를 요약 하 고, 디렉터와 통신 하 고, 개체 및 NFS 데이터 트래픽을 처리 합니다.
- **프록시** - SMB/CIFS 데이터 흐름을 단순화하고 가속화하여 점점 커지는 환경의 성능 요구 사항에 맞게 쉽게 스케일링할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

사전 계획은 위험도가 낮은 데이터를 마이그레이션하는 데 도움이 됩니다.

### <a name="get-started-with-azure"></a>Azure 시작

Microsoft는 Azure를 시작하기 위해 수행할 수 있는 프레임워크를 제공합니다. CAF([클라우드 채택 프레임워크](/azure/architecture/cloud-adoption/))는 프로덕션 등급 클라우드 도입을 계획하기 위한 엔터프라이즈 디지털 변환 및 포괄적인 가이드에 대한 자세한 접근 방식입니다. CAF에는 빠르고 안전하게 시작 및 실행하는 데 도움이 되는 단계별 [Azure 설치 가이드](/azure/cloud-adoption-framework/ready/azure-setup-guide/)가 포함되어 있습니다. [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)에서 대화형 버전을 찾을 수 있습니다. 샘플 아키텍처, 애플리케이션 배포에 대한 특정 모범 사례 및 Azure 전문 지식을 얻을 수 있는 무료 교육 리소스를 찾을 수 있습니다.

### <a name="considerations-for-migrations"></a>마이그레이션 고려 사항

Azure로 파일 데이터의 마이그레이션을 고려할 때 몇 가지 중요한 측면이 있습니다. 계속하기 전에 자세히 알아보세요.

- [스토리지 마이그레이션 개요](../../../common/storage-migration-overview.md)
- [마이그레이션 도구 비교 매트릭스](./migration-tools-comparison.md)의 Komprise Intelligent Data Management에서 지원되는 최신 기능

프로덕션 애플리케이션에 영향을 주지 않고 마이그레이션을 지원하기에 충분한 네트워크 용량이 필요합니다. 이 섹션에서는 네트워크 요구 사항을 평가하는 데 사용할 수 있는 도구와 기술에 대해 간략하게 설명합니다.

#### <a name="determine-unutilized-internet-bandwidth"></a>사용되지 않은 인터넷 대역폭 확인

일별로 사용할 수 있는, 일반적으로 사용되지 않은 대역폭(또는 *여유 대역폭*)의 양을 파악하는 것이 중요합니다. 이를 통해 다음에 대한 목표를 충족할 수 있는지 여부를 평가할 수 있습니다.

- 오프라인 방법에 Azure Data Box를 사용하지 않는 경우 마이그레이션의 초기 시간
- 대상 파일 서비스로 최종 전환하기 전에 증분 다시 동기화를 수행하는 데 필요한 시간

Azure가 사용할 수 있는 여유 대역폭을 확인하려면 다음 방법을 사용합니다.

- 기존 Azure ExpressRoute 고객인 경우 Azure Portal에서 [회로 사용량](../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics)을 확인합니다.
- ISP에 연락하여 기존 일별 및 월별 사용률을 보여 주는 보고서를 요청합니다.
- 라우터/스위치 수준에서 네트워크 트래픽을 모니터링하여 사용률을 측정할 수 있는 몇 가지 도구가 있습니다.
  - [SolarWinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

## <a name="migration-planning-guide"></a>마이그레이션 계획 가이드

Komprise는 다음과 같은 세 단계로 간단하게 설정하고 여러 마이그레이션을 동시에 실행할 수 있습니다.

1.  데이터를 분석 하 여 마이그레이션하거나 보관할 파일 및 개체를 식별 합니다.
1.  비정형 데이터를 Azure Storage로 마이그레이션, 이동 또는 복사하는 정책을 정의합니다.
1.  데이터를 자동으로 이동하는 정책을 활성화합니다.

첫 번째 단계는 마이그레이션할 올바른 데이터를 찾아서 우선 순위를 지정하는 데 중요합니다. Komprise 분석은 다음 정보를 제공합니다.

- 다음 항목을 찾을 수 있는 액세스 시간에 대한 정보:
  - 자주 액세스하지 않아서 온-프레미스에 캐시하거나 빠른 파일 서비스에 저장할 수 있는 파일
  - Blob 스토리지에 보관할 수 있는 콜드 데이터
- 마이그레이션 순서를 결정하는 상위 사용자, 그룹 또는 공유에 대한 정보와 비즈니스 영향을 평가하는 조직 내에서 가장 영향을 받은 그룹에 대한 정보
- 저장되는 파일의 형식과 콘텐츠 정리 가능성이 있는지 여부를 결정하는 파일 수 또는 파일 형식별 용량. 정리 하면 마이그레이션 작업이 줄어들고 대상 저장소의 비용이 줄어듭니다. 개체 데이터에도 유사한 분석을 사용할 수 있습니다.
- 마이그레이션 기간을 결정하는 파일 수 또는 파일 크기당 용량. 적은 수의 작은 파일을 마이그레이션하는 데 더 많은 시간이 소요 됩니다. 개체 데이터에도 유사한 분석을 사용할 수 있습니다.
- 저장소 계층을 기준으로 하는 개체의 비용으로, 콜드 데이터가 비싼 계층에 잘못 배치 되었는지 또는 높은 액세스 비용이 있는 저렴 한 계층에 핫 데이터가 잘못 배치 되었는지 여부를 확인 합니다. 액세스 패턴을 기반으로 데이터를 적절 하 게 배치 하면 전체 클라우드 저장소 비용을 최적화할 수 있습니다.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-1.png" alt-text="파일 형식 및 액세스 시간별 분석":::

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-shares.png" alt-text="공유 분석의 예":::

- 특정 요구 사항에 맞게 정확한 파일 및 개체 집합을 필터링 하는 사용자 지정 쿼리 기능 필터

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-analyze-custom.png" alt-text="사용자 지정 쿼리에 대한 분석":::

## <a name="deployment-guide"></a>배포 가이드

Kom의 Se를 배포 하기 전에 대상 서비스를 배포 해야 합니다. 여기에서 자세히 알아볼 수 있습니다.

- [Azure 파일 공유](../../../files/storage-how-to-create-file-share.md)를 만드는 방법
- Azure NetApp Files에서 [SMB 볼륨](../../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md) 또는 [NFS 내보내기](../../../../azure-netapp-files/azure-netapp-files-create-volumes.md)를 만드는 방법

Komprise Grid는 뛰어난 속도, 확장성 및 복원력을 제공할 수 있도록 가상 환경(Hyper-V, VMware, KVM)에 배포됩니다. 또는 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management)를 사용 하 여 Azure 구독에서 환경을 설정할 수 있습니다.

1. Azure Portal을 열고 **스토리지 계정** 을 검색합니다. 

    :::image type="content" source="./media/komprise-quick-start-guide/azure-locate-storage-account.png" alt-text="Azure Portal의 검색 상자에서 스토리지를 입력한 경우를 보여 줍니다.":::

    기본 **스토리지 계정** 아이콘을 클릭할 수도 있습니다.

    :::image type="content" source="./media/komprise-quick-start-guide/azure-portal.png" alt-text="Azure Portal에서 스토리지 계정을 추가하는 방법을 보여 줍니다.":::

2. **만들기** 를 선택하여 계정을 추가합니다.
   1. 기존 리소스 그룹을 선택하거나 **새로 만듭니다**.
   2. 스토리지 계정의 고유한 이름을 입력합니다.
   3. 지역을 선택합니다.
   4. 필요에 따라 **표준** 또는 **프리미엄** 성능을 선택합니다. **프리미엄** 을 선택하는 경우 **프리미엄 계정 유형** 에서 **파일 공유** 를 선택합니다.
   5. 데이터 보호 요구 사항을 충족하는 **[중복도](../../../common/storage-redundancy.md)** 를 선택합니다.
   
   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-1.png" alt-text="Portal의 스토리지 계정 설정을 보여 줍니다.":::

3. 다음으로, **고급** 화면에서 권장되는 기본 설정을 알아봅니다. Azure Files로 마이그레이션하는 경우, **대량 파일 공유** 를 사용하는 것이 좋습니다(사용 가능한 경우).

   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-2.png" alt-text="Portal의 고급 설정 탭을 보여 줍니다.":::

4. 지금은 기본 네트워킹 옵션을 그대로 유지하고 **데이터 보호** 로 이동합니다. 일시 삭제를 사용하도록 선택하여 정의된 보존 기간 내에 실수로 삭제된 데이터를 복구할 수 있습니다. 일시 삭제는 실수로 또는 악의적으로 삭제되지 않도록 보호 기능을 제공합니다.

   :::image type="content" source="./media/komprise-quick-start-guide/azure-account-create-3.png" alt-text="Portal의 데이터 보호 설정을 보여 줍니다.":::

5. 태그 지정을 사용하는 경우 조직에 대한 태그를 추가하고 계정을 **생성** 합니다.
 
6. 이제 Komprise 환경에 계정을 추가하려면 먼저 두 개의 빠른 단계를 수행해야 합니다. Azure Portal에서 만든 계정으로 이동하고 파일 서비스 메뉴 아래에서 파일 공유를 선택합니다. 파일 공유를 추가하고 의미 있는 이름을 선택합니다. 그런 다음, 설정에서 액세스 키 항목으로 이동하고 스토리지 계정 이름과 두 액세스 키 중 하나를 복사합니다. 키가 보이지 않으면 **키 표시** 를 클릭합니다.

   :::image type="content" source="./media/komprise-quick-start-guide/azure-access-key.png" alt-text="Portal의 액세스 키 설정을 보여 줍니다.":::

7. Azure 파일 공유의 **속성** 으로 이동합니다. URL 주소를 적어 둡니다. Komprise 대상 파일 공유에 Azure 연결을 추가하는 데 필요합니다.

   :::image type="content" source="./media/komprise-quick-start-guide/azure-files-endpoint.png" alt-text="Azure 파일 엔드포인트를 찾습니다.":::

8. (_선택 사항_) 배포에 더 많은 보안 계층을 추가할 수 있습니다.
 
   1. 역할 기반 액세스를 구성하여 스토리지 계정을 변경할 수 있는 사용자를 제한합니다. 자세한 내용은 [관리 작업을 위한 기본 제공 역할](../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)을 참조하세요.
 
   2.  [스토리지 방화벽 설정](../../../common/storage-network-security.md)을 사용하여 특정 네트워크 세그먼트로 계정에 대한 액세스를 제한합니다. 회사 네트워크 외부에서 액세스하지 못하도록 방화벽 설정을 구성합니다.

       :::image type="content" source="./media/komprise-quick-start-guide/azure-storage-firewall.png" alt-text="Portal의 스토리지 방화벽 설정을 보여 줍니다.":::

   3.  계정에 대해 [삭제 잠금](../../../../azure-resource-manager/management/lock-resources.md)을 설정하여 스토리지 계정이 실수로 삭제되지 않도록 합니다.

       :::image type="content" source="./media/komprise-quick-start-guide/azure-resource-lock.png" alt-text="Portal에서 삭제 잠금을 설정하는 방법을 보여 줍니다.":::

   4.  추가 [보안 모범 사례](../../../blobs/security-recommendations.md)를 구성합니다.

### <a name="deployment-instructions-for-managing-file-data"></a>파일 데이터 관리에 대 한 배포 지침

1.  Director에서 Komprise Observer를 **다운로드** 하여 하이퍼바이저에 배포하고, 네트워크 및 도메인을 구성합니다. Director는 Komprise에서 관리하는 클라우드 서비스로 제공됩니다. 디렉터에 액세스하는 데 필요한 정보는 솔루션 구매 시 환영 이메일과 함께 전송됩니다.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-1.png" alt-text="Director에서 Komprise Observer에 적합한 이미지를 다운로드":::

1. 분석 및 마이그레이션할 공유를 추가하려면 다음과 같은 두 가지 옵션이 있습니다.
   1. 다음 정보를 입력하여 스토리지 환경의 모든 공유를 **검색** 합니다.
       - 원본 NAS용 플랫폼
       - 호스트 이름 또는 IP 주소
       - 표시 이름
       - 자격 증명(SMB 공유인 경우)

        :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-2.png" alt-text="검색할 NAS 시스템 지정":::

    1. 다음 정보를 입력하여 파일 공유를 **지정** 합니다.
       - 스토리지 정보
       - 프로토콜
       - 경로
       - 표시 이름
       - 자격 증명(SMB 공유인 경우)
  
        :::image type="content" source="./media/komprise-quick-start-guide/komprise-setup-3.png" alt-text="검색할 NAS 솔루션 지정":::

    다른 원본 및 대상 공유를 추가하려면 이 단계를 반복해야 합니다. Azure Files를 대상으로 추가하려면 Azure 스토리지 계정 및 파일 공유 세부 정보를 제공해야 합니다.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-azure-files-1.png" alt-text="대상 서비스로 Azure Files 선택":::

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-azure-files-2.png" alt-text="Azure Files 세부 정보 입력":::

### <a name="deployment-instructions-for-managing-object-data"></a>개체 데이터 관리에 대 한 배포 지침

개체 관리는 다양 한 환경을 제공 합니다. 디렉터와 관찰자는 Komprise에서 관리 하는 클라우드 서비스로 제공 됩니다. Azure Blob Storage 데이터를 분석 하 고 보관 하기만 하면 추가 배포가 필요 하지 않습니다. Azure Blob Storage에 대 한 마이그레이션을 수행 해야 하는 경우 시작 전자 메일을 통해 전송 된 komprise 관찰자 가상 어플라이언스를 가져와 Azure 클라우드 인프라의 Linux 가상 머신에 배포 합니다. 배포한 후 Kom의 Se Director의 단계를 따릅니다.

1. **데이터 저장소** 로 이동 하 여 **새 개체 저장소를 추가** 합니다. 공급자로 **Microsoft Azure** 를 선택 합니다.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-add-object-store.png" alt-text="새 개체 저장소를 추가 하는 것을 보여 주는 스크린샷":::

1. 분석 및 마이그레이션할 공유를 추가 합니다. 모든 원본, 대상 공유 또는 컨테이너에 대해 이러한 단계를 반복 해야 합니다. 동일한 작업을 수행 하는 두 가지 옵션이 있습니다.
    1. 다음을 입력 하 여 모든 컨테이너를 **검색** 합니다.
        - 스토리지 계정 이름
        - 기본 액세스 키
        - 표시 이름
    
        :::image type="content" source="./media/komprise-quick-start-guide/komprise-discover-storage-account.png" alt-text="저장소 계정에서 컨테이너를 검색 하는 방법을 보여 주는 스크린샷":::

        필요한 정보는 저장소 계정에 대 한 **설정** 아래의 **액세스 키** 항목으로 이동 하 여 **[Azure Portal](https://portal.azure.com/)** 에서 찾을 수 있습니다. 키가 보이지 않으면 **키 표시** 를 클릭합니다.

    1. 다음을 입력 하 여 컨테이너를 **지정** 합니다.
        - 컨테이너 이름
        - 스토리지 계정 이름
        - 기본 액세스 키
        - 표시 이름

        :::image type="content" source="./media/komprise-quick-start-guide/komprise-add-container.png" alt-text="저장소 계정에서 컨테이너를 추가 하는 방법을 보여 주는 스크린샷":::

        컨테이너 이름은 마이그레이션의 대상 컨테이너를 나타내며 마이그레이션하기 전에 만들어야 합니다. 다른 필수 정보는 저장소 계정에 대 한 **설정** 아래의 **액세스 키** 항목으로 이동 하 여 **[Azure Portal](https://portal.azure.com/)** 에서 찾을 수 있습니다. 키가 보이지 않으면 **키 표시** 를 클릭합니다.

## <a name="migration-guide"></a>마이그레이션 가이드

Komprise는 최종 사용자 및 애플리케이션이 중단되지 않고 마이그레이션 중에 데이터에 계속 액세스할 수 있는 실시간 마이그레이션을 제공합니다. 마이그레이션 프로세스는 원본의 디렉터리, 파일 및 링크를 대상으로 마이그레이션하는 작업을 자동화합니다. 각 단계에서 데이터 무결성이 검사됩니다. 원본의 모든 특성, 권한 및 액세스 제어가 적용됩니다. 개체 마이그레이션에서는 각 개체의 개체, 접두사 및 메타 데이터가 마이그레이션됩니다.

마이그레이션을 구성하고 실행하려면 다음 단계를 수행합니다.

1. Komprise 콘솔에 로그인합니다. 콘솔에 액세스하는 데 필요한 정보는 솔루션 구매 시 환영 이메일과 함께 전송됩니다.
1. **마이그레이션** 으로 이동하여 **마이그레이션 추가** 를 클릭합니다.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-new-migrate.png" alt-text="새 마이그레이션 작업 추가":::

1. 적절한 원본 및 대상 공유를 선택하여 마이그레이션 작업을 추가합니다. 마이그레이션 이름을 입력합니다. 구성을 마쳤으면 **마이그레이션 시작** 을 클릭합니다. 파일 및 개체 데이터 마이그레이션에 대 한이 단계는 약간 다릅니다.
   
    1. 파일 마이그레이션

       :::image type="content" source="./media/komprise-quick-start-guide/komprise-add-migration.png" alt-text="마이그레이션 작업에 대한 세부 정보 지정":::

       파일 마이그레이션은 대상에 대 한 액세스 시간 및 SMB Acl을 유지 하는 옵션을 제공 합니다. 이 옵션은 선택한 원본/대상 파일 서비스 및 프로토콜에 따라 달라집니다.

    1. 개체 마이그레이션

        :::image type="content" source="./media/komprise-quick-start-guide/komprise-add-object-migration.png" alt-text="개체 마이그레이션 추가를 보여 주는 스크린샷":::

        개체 마이그레이션은 대상 Azure storage 계층 (핫, 쿨, 보관)을 선택 하는 옵션을 제공 합니다. MD5 체크섬을 사용 하 여 각 데이터 전송을 확인 하도록 선택할 수도 있습니다. md5 체크섬을 계산 하기 위해 클라우드 개체를 검색 해야 하므로 md5 체크섬을 사용 하 여 Egress 비용이 발생할 수 있습니다.

2. 마이그레이션이 시작되면 **마이그레이션** 으로 이동하여 진행 상황을 모니터링할 수 있습니다.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-monitor-migrations.png" alt-text="모든 마이그레이션 작업 모니터링":::

3. 모든 변경 내용이 마이그레이션되면 **작업** 을 클릭하고 **최종 반복 시작** 을 선택하여 최종 마이그레이션을 실행합니다. 최종 마이그레이션을 시작하기 전에 원본 파일 공유에 대한 액세스를 중지하거나 읽기 전용 모드(사용자 및 애플리케이션의 경우)로 전환하는 것이 좋습니다. 이렇게 하면 원본이 변경되지 않습니다.

    :::image type="content" source="./media/komprise-quick-start-guide/komprise-final-migration.png" alt-text="전환하기 전에 마지막 마이그레이션 수행":::

    최종 마이그레이션이 완료되면 모든 사용자 및 애플리케이션을 대상 공유로 전환합니다. 새 파일 서비스로 전환하려면 일반적으로 DNS 서버, DFS 서버의 구성을 변경하거나 탑재 지점을 새 대상으로 변경해야 합니다. 

4. 마지막 단계로 마이그레이션이 완료되었음을 표시합니다.

## <a name="support"></a>지원

Komprise 관련 사례를 개설하려면 [Komprise 지원 사이트](https://komprise.freshdesk.com/)에 로그인합니다.

## <a name="marketplace"></a>Marketplace

[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)에서 Komprise 목록을 얻을 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세히 알아볼 수 있는 다양한 리소스가 제공됩니다.

- [스토리지 마이그레이션 개요](../../../common/storage-migration-overview.md)
- [마이그레이션 도구 비교 매트릭스](./migration-tools-comparison.md)의 Komprise Intelligent Data Management에서 지원되는 기능
- [Komprise 호환성 매트릭스](https://www.komprise.com/partners/microsoft-azure/)
