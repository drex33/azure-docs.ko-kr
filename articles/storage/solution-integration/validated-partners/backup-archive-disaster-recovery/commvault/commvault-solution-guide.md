---
title: Commvault를 사용하여 Azure에 데이터 백업
titleSuffix: Azure Storage
description: Commvault Complete 백업 및 복구에 대한 스토리지 대상 및 복구 위치로 Azure를 사용하기 위해 고려해야 할 요소 및 수행할 단계에 대한 개요를 제공합니다.
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 8d897c4e4c76b42e980c23f1f18789248f04909a
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129270508"
---
# <a name="backup-to-azure-with-commvault"></a>Commvault를 사용하여 Azure에 백업

이 문서는 Commvault 인프라를 Azure Blob Storage와 통합하는 데 도움이 됩니다. 여기에는 필수 구성 요소, 고려 사항, 구현 및 작업 지침이 포함됩니다. 이 문서에서는 Azure를 오프사이트 백업 대상, 기본 사이트 내의 정상적인 작업을 방지하는 재해 발생 시 복구 사이트로 사용하는 방법을 설명합니다.

> [!NOTE]
> Commvault는 하위 RTO(복구 시간 목표) 솔루션인 Commvault Live Sync를 제공합니다. 이 솔루션을 사용하면 Azure 프로덕션 환경에서 재해가 발생하는 경우 보다 신속하게 복구하는 데 도움이 되는 대기 VM을 사용할 수 있습니다. 이러한 기능은 이 문서에서 다루지 않습니다.

## <a name="reference-architecture"></a>참조 아키텍처

다음 다이어그램에서는 온-프레미스에서 Azure로의 배포 및 Azure 내 배포에 대한 참조 아키텍처를 제공합니다.

![Commvault-Azure 참조 아키텍처 다이어그램](../media/commvault-diagram.png)

기존 Commvault 배포는 Azure Storage 계정 또는 여러 계정을 클라우드 스토리지 대상으로 추가하여 Azure와 쉽게 통합할 수 있습니다. Commvault를 사용하여 Azure 내의 온-프레미스에서 백업을 복구하여 Azure에서 주문형 복구 사이트를 제공할 수도 있습니다.

## <a name="commvault-interoperability-matrix"></a>Commvault 상호 운용성 매트릭스

| 작업 | GPv2 및 Blob Storage | 쿨 계층 지원 | 보관 계층 지원 | Data Box 제품군 지원 |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| 온-프레미스 VM/데이터 | v11.5 | v11.5 | v11.10 | v11.10 |
| Azure VM | v11.5 | v11.5 | v11.5 | 해당 없음 |
| Azure Blob | v11.6 | v11.6 | v11.6 | 해당 없음 |
| Azure 파일 | v11.6 | v11.6 | v11.6 | 해당 없음 |

## <a name="before-you-begin"></a>시작하기 전에

약간 미리 계획하면 Azure를 오프사이트 백업 대상 및 복구 사이트로 사용하는 데 도움이 됩니다.

### <a name="get-started-with-azure"></a>Azure 시작

Microsoft는 Azure를 시작하기 위해 수행할 수 있는 프레임워크를 제공합니다. CAF([클라우드 채택 프레임워크](/azure/architecture/cloud-adoption/))는 프로덕션 등급 클라우드 도입을 계획하기 위한 엔터프라이즈 디지털 변환 및 포괄적인 가이드에 대한 자세한 접근 방식입니다. CAF에는 빠르고 안전하게 시작 및 실행하는 데 도움이 되는 단계별 [Azure 설치 가이드](/azure/cloud-adoption-framework/ready/azure-setup-guide/)가 포함되어 있습니다. [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)에서 대화형 버전을 찾을 수 있습니다. 샘플 아키텍처, 애플리케이션 배포에 대한 특정 모범 사례 및 Azure 전문 지식을 얻을 수 있는 무료 교육 리소스를 찾을 수 있습니다.

### <a name="consider-the-network-between-your-location-and-azure"></a>사용자 위치와 Azure 간의 네트워크 고려

클라우드 리소스를 사용하여 프로덕션, 테스트 및 개발을 실행하거나 백업 대상 및 복구 사이트로 사용하는 경우, 초기 백업 시드 및 진행 중인 일별 전송에 대한 대역폭 요구 사항을 이해하는 것이 중요합니다.

Azure Data Box는 더 많은 대역폭을 요구하지 않으면서 초기 백업 기준을 Azure에 전송하는 방법을 제공합니다. 기본 전송이 사용자가 허용할 수 있는 것보다 더 오래 걸릴 것으로 예상되는 경우에 유용합니다. 스토리지 계정을 만들 때 데이터 전송 예측 도구를 사용하여 초기 백업을 전송하는 데 필요한 시간을 예상할 수 있습니다.

![Portal의 Azure Storage 데이터 전송 예측 도구를 보여 줍니다.](../media/az-storage-transfer.png)

프로덕션 애플리케이션에 영향을 주지 않고 필요한 전송 기간(백업 기간) 내에서 일별 데이터 전송을 지원하는 데 충분한 네트워크 용량이 필요합니다. 이 섹션에서는 네트워크 요구 사항을 평가하는 데 사용할 수 있는 도구와 기술에 대해 간략하게 설명합니다.

#### <a name="determine-how-much-bandwidth-youll-need"></a>필요한 대역폭 크기 결정

필요한 대역폭 크기를 확인하려면 다음 리소스를 사용합니다.

- 백업 소프트웨어의 보고서
- Commvault는 Azure로의 초기 기준 전송에 대한 [변경률](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) 및 [전체 백업 세트 크기](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm)를 결정하기 위한 표준 보고서를 제공합니다.
- 다음과 같은 백업 소프트웨어 독립적 평가 및 보고 도구
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>사용되지 않은 인터넷 대역폭 확인

일별로 사용할 수 있는 일반적으로 사용되지 않은 대역폭(또는 *여유 대역폭*)의 양을 파악하는 것이 중요합니다. 이를 통해 다음에 대한 목표를 충족할 수 있는지 여부를 평가할 수 있습니다.

- 오프라인 시드에 Azure Data Box를 사용하지 않는 경우 초기 업로드 시간
- 이전에 식별된 변경률과 백업 기간에 따라 일별 백업 완료

Azure에 대한 백업이 사용할 수 있는 여유 대역폭을 확인하려면 다음 방법을 사용합니다.

- 기존 Azure ExpressRoute 고객인 경우 Azure Portal에서 [회로 사용량](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics)을 확인합니다.
- ISP에 문의합니다. 기존 일별 및 월별 사용률을 표시하는 보고서를 공유할 수 있어야 합니다.
- 라우터/스위치 수준에서 네트워크 트래픽을 모니터링하여 사용률을 측정할 수 있는 몇 가지 도구가 있습니다. 여기에는 다음이 포함됩니다.
  - [Solarwinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>적절한 스토리지 옵션 선택

Azure를 백업 대상으로 사용하는 경우 [Azure Blob Storage](../../../../blobs/storage-blobs-introduction.md)를 사용하게 됩니다. Blob Storage는 Microsoft의 개체 스토리지 솔루션입니다. Blob Storage는 임의 데이터 모델이나 정의를 따르지 않는 비정형 데이터를 대량으로 저장하는 데 최적화되었습니다. 또한 Azure Storage는 내구성이 있고, 가용성이 높으며 안전하고 스케일링 가능합니다. 워크로드에 적합한 스토리지를 선택하여 내부 SLA를 충족하는 [복원력 수준](../../../../common/storage-redundancy.md)을 제공할 수 있습니다. Blob Storage는 사용량 과금 서비스입니다. 저장된 데이터의 양, 해당 데이터 액세스 및 쿨 및 보관 계층의 경우 필요한 최소 보존 기간에 대해 [월별 요금이 청구](../../../../blobs/access-tiers-overview.md#pricing-and-billing)됩니다. 백업 데이터에 적용할 수 있는 복원력 및 계층화 옵션은 다음 표에 요약되어 있습니다.

**Blob Storage 복원력 옵션:**

|  |로컬 중복  |Zone-redundant  |지역 중복  |지역 영역 중복  |
|---------|---------|---------|---------|---------|
|**유효 복사본 개수**     | 3         | 3         | 6         | 6 |
|**가용성 영역 개수**     | 1         | 3         | 2         | 4 |
|**지역 개수**     | 1         | 1         | 2         | 2 |
|**보조 지역으로 수동 장애 조치(failover)**     | 해당 없음         | 해당 없음         | 예         | 예 |

**Blob Storage 계층:**

|  | 핫 액세스 계층   |쿨 액세스 계층   | 보관 액세스 계층 |
| ----------- | ----------- | -----------  | -----------  |
| **가용성** | 99.9%         | 99%         | 오프라인      |
| **사용 요금** | 스토리지 비용 더 높음, 액세스 및 트랜잭션 비용 더 낮음 | 스토리지 비용 더 낮음, 액세스 및 트랜잭션 비용 더 높음 | 스토리지 비용 가장 낮음, 액세스 및 트랜잭션 비용 가장 높음 |
| **최소 데이터 보존 필요**| 해당 없음 | 30일 | 180일 |
| **대기 시간(첫 번째 바이트까지의 시간)** | 밀리초 | 밀리초 | 시간 |

#### <a name="sample-backup-to-azure-cost-model"></a>Azure 비용 모델에 대한 샘플 백업

클라우드를 처음 사용하는 고객에게는 사용량 과금 방식이 어려울 수 있습니다. 사용되는 용량에 대해서만 비용을 지불하지만, Azure에서 송신되는 데이터가 포함되는 [Azure Express Route 직접 로컬 또는 Express route 무제한 데이터 플랜](https://azure.microsoft.com/pricing/details/expressroute/)을 사용할 경우 트랜잭션(읽기 및/또는 쓰기)과 온-프레미스 환경으로 다시 읽어오는 [데이터 송신](https://azure.microsoft.com/pricing/details/bandwidth/)에 대해서도 비용이 부과됩니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 "가상 시나리오" 분석을 수행할 수 있습니다. 가격 책정 또는 [Azure Storage 예약 용량 가격 책정](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)에 대한 분석을 기준으로 최대 38% 비용을 절감할 수 있습니다. Azure에 백업하는 월간 비용을 모델링하는 예제 가격 책정 예제는 다음과 같습니다. 이것은 예에 불과합니다. *가격 책정은 여기에서 캡처되지 않은 활동으로 인해 달라질 수 있습니다.*

|비용 요소  |월간 비용  |
|---------|---------|
|쿨 스토리지에서 100TB의 백업 데이터     |$1556.48         |
|하루에 작성된 2TB의 새 데이터 x 30일     |트랜잭션의 $39          |
|월간 예상 합계     |$1595.48         |
|---------|---------|
|퍼블릭 인터넷을 통해 온-프레미스로의 5TB 일회성 복원   | $491.26         |

> [!NOTE]
> 이 추정치는 미국 동부 종량제 가격 책정을 사용하여 Azure 가격 계산기에서 생성되었으며, 일별로 65536 PUT 요청(쓰기 트랜잭션)을 생성하는 32MB 하위 청크 크기의 Commvault 기본값을 기준으로 합니다. 이 예는 사용자의 요구 사항과는 맞지 않을 수 있습니다.

## <a name="implementation-guidance"></a>구현 지침

이 섹션에서는 온-프레미스 Commvault 배포에 Azure Storage를 추가하는 방법에 대한 간략한 지침을 제공합니다. 자세한 지침 및 계획 고려 사항은 [Microsoft Azure에 대한 Commvault 퍼블릭 클라우드 아키텍처 가이드](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)를 참조하세요.

1. Azure Portal을 열고 **스토리지 계정** 을 검색합니다. 기본 **스토리지 계정** 아이콘을 클릭할 수도 있습니다.

    ![Azure Portal에서 스토리지 계정을 추가하는 방법을 보여 줍니다.](../media/azure-portal.png)
  
    ![Azure Portal의 검색 상자에서 스토리지를 입력한 경우를 보여 줍니다.](../media/locate-storage-account.png)

2. **만들기** 를 선택하여 계정을 추가합니다. 리소스 그룹을 선택하거나 만들고, 고유한 이름을 지정하고, 지역을 선택하고, **표준** 성능을 선택하고, 항상 계정 종류를 **Storage V2** 로 두고, SLA를 충족하는 복제 수준을 선택하고, 백업 소프트웨어가 적용될 기본 계층을 선택합니다. Azure Storage 계정은 단일 계정 내에서 사용할 수 있는 핫, 쿨 및 보관 계층을 설정하고 Commvault 정책을 통해 여러 계층을 사용하여 데이터의 수명 주기를 효과적으로 관리할 수 있습니다.

    ![Portal의 스토리지 계정 설정 표시](../media/account-create-1.png)

3. 지금은 기본 네트워킹 옵션을 그대로 유지하고 **데이터 보호** 로 이동합니다. 여기에서 정의된 보존 기간 내에 실수로 삭제된 백업 파일을 복구하고 실수로 인한 삭제 또는 악의적인 삭제로부터 .데이터를 보호할 수 있는 일시 삭제를 사용하도록 선택할 수 있습니다.

    ![Portal의 데이터 보호 설정을 보여 줍니다.](../media/account-create-2.png)

4. 다음으로, Azure로의 백업 사용 사례에 대한 **고급** 화면의 기본 설정을 사용하는 것이 좋습니다.

    ![Portal의 고급 설정 탭을 보여 줍니다.](../media/account-create-3.png)

5. 태그 지정을 사용하고 계정을 만드는 경우 조직에 대한 태그를 추가합니다.

6. 이제 Commvault 환경에 계정을 추가하려면 먼저 두 개의 빠른 단계를 수행해야 합니다. Azure Portal에서 만든 계정으로 이동하고 **Blob Service** 메뉴 아래에서 **컨테이너** 를 선택합니다. 컨테이너를 추가하고 의미 있는 이름을 선택합니다. 그런 다음, **설정** 에서 **액세스 키** 항목으로 이동하고 **스토리지 계정 이름** 과 두 액세스 키 중 하나를 복사합니다. 다음 단계에서 컨테이너 이름, 계정 이름 및 액세스 키가 필요합니다.

    ![Portal의 컨테이너 만들기를 보여 줍니다.](../media/container.png)

    ![Portal의 액세스 키 설정을 보여 줍니다.](../media/access-key.png)

7. (*선택 사항*) 배포에 더 많은 보안 계층을 추가할 수 있습니다.

    1. 역할 기반 액세스를 구성하여 스토리지 계정을 변경할 수 있는 사용자를 제한합니다. 자세한 내용은 [관리 작업을 위한 기본 제공 역할](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)을 참조하세요.
    1. 회사 네트워크 외부에서 액세스를 시도할 수 없도록 [스토리지 방화벽 설정](../../../../common/storage-network-security.md)을 사용하여 계정에 대한 액세스를 특정 네트워크 세그먼트로 제한합니다.

        ![Portal의 스토리지 방화벽 설정을 보여 줍니다.](../media/storage-firewall.png)

    1. 계정에 대해 [삭제 잠금](../../../../../azure-resource-manager/management/lock-resources.md)을 설정하여 스토리지 계정이 실수로 삭제되지 않도록 합니다.

        ![Portal에서 삭제 잠금을 설정하는 방법을 보여 줍니다.](../media/resource-lock.png)

    1. 추가 [보안 모범 사례](../../../../../storage/blobs/security-recommendations.md)를 구성합니다.

1. Commvault Command Center에서 **관리** -> **보안** -> **자격 증명 관리자** 로 이동합니다. **클라우드 계정**, **Microsoft Azure Storage** 의 **공급업체 유형** 을 선택하고, Azure 간에 데이터를 전송하는 **MediaAgent** 를 선택하고, 스토리지 계정 이름 및 액세스 키를 추가합니다.

    ![Commvault Command Center에서 자격 증명을 추가하는 방법을 보여 줍니다.](../media/commvault-credential.png)

9. 다음으로, Commvault Command Center에서 **스토리지** -> **클라우드** 로 이동합니다. **추가** 하도록 선택합니다. 스토리지 계정의 이름을 입력하고 **유형** 목록에서 **Microsoft Azure Storage** 를 선택합니다. Azure Storage에 백업을 전송하는 데 사용할 미디어 에이전트 서버를 선택합니다. 만든 컨테이너를 추가하고, Azure Storage 계정 내에서 사용할 스토리지 계층을 선택하고, 8단계에서 만든 자격 증명을 선택합니다. 마지막으로 중복 제거된 백업을 전송할지 여부 및 중복 제거 데이터베이스의 위치를 선택합니다.

     ![Commvault의 클라우드 사용자 추가 인터페이스 스크린샷 보관 드롭다운 메뉴에서 **보관**이 선택됩니다.](../media/commvault-add-storage.png)

10. 마지막으로 **관리** -> **플랜** 을 통해 Commvault Command Center의 기존 플랜 또는 새 플랜에 새 Azure Storage 리소스를 백업 대상으로 추가합니다.

    ![Commvault Command Center 사용자 인터페이스 스크린샷 왼쪽 탐색 영역의 **관리**에서 **플랜**이 선택됩니다.](../media/commvault-plan.png)

11. *(선택 사항)* Azure를 복구 사이트 또는 Commvault로 사용하여 서버 및 애플리케이션을 Azure로 마이그레이션할 계획인 경우 Azure에 VSA 프록시를 배포하는 것이 가장 좋습니다. 자세한 지침은 [여기](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)에서 찾을 수 있습니다.

## <a name="operational-guidance"></a>운영 가이드

### <a name="azure-alerts-and-performance-monitoring"></a>Azure 경고 및 성능 모니터링

백업을 저장하는 데 사용하는 모든 스토리지 대상의 경우와 마찬가지로 Azure 리소스와 이러한 리소스를 사용하는 Commvault의 기능을 모두 모니터링하는 것이 좋습니다. Azure Monitor와 Commvault Command Center 모니터링 기능을 함께 사용하면 환경을 정상 상태로 유지하는 데 도움이 됩니다.

#### <a name="azure-portal"></a>Azure portal

Azure는 [Azure Monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md) 형식으로 강력한 모니터링 솔루션을 제공합니다. [Azure Monitor를 구성](../../../../blobs/monitor-blob-storage.md)하여 Azure Storage 용량, 트랜잭션, 가용성, 인증 등을 추적할 수 있습니다. [여기](../../../../blobs/monitor-blob-storage-reference.md)에서 수집된 메트릭의 전체 참조를 찾을 수 있습니다. 추적할 몇 가지 유용한 메트릭으로는 최대 [스토리지 계정 용량 제한](../../../../common/scalability-targets-standard-account.md) 이내로 유지하도록 하는 BlobCapacity, Azure Storage 계정에서 읽고 쓰는 데이터의 양을 추적하는 수신/송신, Azure Storage 및 MediaAgent로 들어오고 나가는 요청의 왕복 시간을 추적하는 SuccessE2ELatency가 있습니다.

또한 [로그 경고를 만들어](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) Azure Storage 서비스 상태를 추적하고 언제든지 [Azure 상태 대시보드](https://status.azure.com/status)를 볼 수 있습니다.

#### <a name="commvault-command-center"></a>Commvault Command Center

- [클라우드 스토리지 풀에 대한 경고 만들기](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- 성능 보고서 및 작업 완료를 확인하고 기본적인 문제 해결을 시작할 수 있는 위치에 대한 자세한 내용은 [대시보드](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm)를 참조하세요.

### <a name="how-to-open-support-cases"></a>지원 사례를 여는 방법

Azure로의 백업 솔루션을 사용할 때 도움이 필요한 경우 Commvault와 Azure를 모두 사용하여 사례를 열어야 합니다. 이렇게 하면 필요한 경우 지원 조직에서 공동 작업을 진행할 수 있습니다.

#### <a name="to-open-a-case-with-commvault"></a>Commvault를 사용하여 사례를 열려면

[Commvault Support Site](https://www.commvault.com/support)에서 로그인하고 사례를 엽니다.

사용할 수 있는 지원 계약 옵션을 이해하려면 [Commvault 지원 옵션](https://ma.commvault.com/support)을 참조하세요.

전화를 통해 사례를 열거나 메일을 통해 Commvault 지원 서비스에 연결할 수도 있습니다.

- 수신자 부담: +1 877-780-3077
- [전 세계 지원 전화 번호](https://ma.commvault.com/Support/TelephoneSupport)
- [Commvault 지원 서비스에 메일 보내기](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Azure에서 사례를 열려면

[Azure Portal](https://portal.azure.com)의 맨 위에 있는 검색 표시줄에서 **지원** 을 검색합니다. **도움말 + 지원** -> **새 지원 요청** 을 선택합니다.

> [!NOTE]
> 사례를 열 때 Azure Storage 또는 Azure 네트워킹에 대한 도움이 필요할 수 있습니다. Azure Backup은 지정하지 마세요. Azure Backup은 Azure 서비스의 이름이며, 사례가 잘못 라우팅됩니다.

### <a name="links-to-relevant-commvault-documentation"></a>관련 Commvault 설명서에 대한 링크

추가 정보는 다음 Commvault 설명서를 참조하세요.

- [Commvault 사용자 가이드](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Commvault Azure 아키텍처 가이드](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)

### <a name="marketplace-offerings"></a>Marketplace 제품

Commvault를 통해 Azure에서 솔루션을 쉽게 배포하고 Azure Virtual Machines 및 기타 여러 Azure 서비스를 보호할 수 있습니다. 자세한 내용은 다음 참조 문서를 참조하세요.

- [Azure Marketplace를 통해 Commvault 배포](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [Azure용 Commvault 데이터시트](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [지원되는 Azure 기능 및 서비스의 Commvault 목록](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [Azure에서 Commvault를 사용하여 SAP HANA를 보호하는 방법](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>다음 단계

특수 사용 시나리오에 대한 내용은 이러한 추가 Commvault 리소스를 참조하세요.

- [Commvault를 사용하여 서버 및 애플리케이션을 Azure로 마이그레이션](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Commvault를 사용하여 Azure에서 SAP 보호](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Commvault를 사용하여 Office365 보호](https://www.youtube.com/watch?v=dl3nvAacxZU)