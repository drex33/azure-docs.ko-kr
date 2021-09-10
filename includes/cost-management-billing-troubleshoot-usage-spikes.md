---
title: 포함 파일
description: 포함 파일
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 02/09/2021
ms.author: banders
ms.reviewer: isvargas
ms.custom: include file
ms.openlocfilehash: efd08ec98533bd78d6a7cc606e11cbde1b4e33e3
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "122528060"
---
## <a name="troubleshoot-usage-spikes"></a>사용량 급증 문제 해결

이 섹션은 Azure 사용량 파일에 사용량 급증이 표시되는 방법, 사용량 급증을 방지하는 방법, 리소스 모니터링 방법 및 Azure 지원에 문의하는 시기를 파악하는 데 도움이 됩니다. 이는 EA(기업계약) 또는 Microsoft 고객 계약이 있는 고객을 위한 것입니다. 해당 고객은 EA 관리자 또는 대금 청구 관리자 역할이 있어야 합니다. 권한에 대한 자세한 내용은 [Azure 청구서 및 일간 사용량 데이터 다운로드 또는 보기](../articles/cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)를 참조하세요.

특정 서비스 또는 리소스에 대한 Azure 사용량 급증 또는 예기치 않은 요금은 종종 인시던트 또는 의도하지 않은 오용으로 인해 발생합니다.

두 경우 모두 적절한 지원 영역을 선택할 수 있도록 지원 담당자에게 문의하기 전에 영향을 받는 서비스 및 리소스의 범위를 좁혀야 합니다.

Microsoft가 사용량 증가 및 관련 요금의 근본 원인을 확인할 가능성은 낮다는 것을 이해하는 것이 중요합니다. 따라서 고객은 Azure Portal에서 자신의 자세한 사용량 현황 데이터를 다운로드할 수 있습니다.

Microsoft는 보안 문제 및 고객 개인 정보 보호로 인해 가상 머신, 네트워크 또는 데이터 전송과 같은 배포된 Azure 리소스를 모니터링하지 않습니다. 그러나 Microsoft는 Azure 사용량을 모니터링하는 방법을 알려 주려고 합니다. 궁극적으로 사용자 고유의 사용량을 모니터링하는 것은 사용자의 책임입니다.

### <a name="what-a-spike-looks-like-in-the-usage-file"></a>사용량 파일에 표시되는 급증

이전 섹션에서 설명한 필터를 적용한 후에는 비정상적인 항목을 찾을 수 있습니다. 예를 들어 **대역폭** 측정기 범주에 대한 급증 문제를 해결할 수 있습니다.

피벗 테이블 도구의 행 섹션에서 **제품** 및 **인스턴스 ID**(Microsoft 고객 계약의 리소스 ID)를 입력합니다. 그런 다음, 값에 **비용**, 필터에 **구독 ID**, 열에 **날짜** 를 추가합니다. 그런 다음, 필터링하여 구독 ID에 대한 데이터만 표시합니다. `111111111111-1111-1111-111111111111`)을 입력합니다.

다음 이미지는 대역폭(데이터 전송) 급증이 어떻게 나타나는지 보여줍니다.

:::image type="content" source="./media/cost-management-billing-troubleshoot-usage-spikes/data-usage-spike.png" alt-text="사용량 급증을 보여주는 Excel의 스크린샷" lightbox="./media/cost-management-billing-troubleshoot-usage-spikes/data-usage-spike.png" :::

급증은 특정 리소스에 대한 것입니다. 이 경우 Excel 파일의 7행에 스토리지 계정 **storageaccountnameazurefile1** 에 대한 비용 값이 표시됩니다. 2020년 10월 1일에 비용은 0 USD(2.23043E-06, 0.000002230431449와 동일)에 가까운 값을 갖습니다. 비용이 10,000 USD 및 28,000 USD가 되는 2020년 10월 2일과 2020년 10월 3일에 크게 급증하는 것을 볼 수 있습니다. 2020년 10월 4일(9.29E-07)에 비용이 정상으로 돌아왔습니다.

이 예제에서는 대역폭 요금이 많이 발생한 리소스, 발생한 날짜 및 특정 제품(지역 간 – 데이터 전송 - 유럽)을 식별했습니다. 급증이 대용량 데이터 전송으로 인해 발생했는지 확인합니다. 이전 섹션의 정보를 사용하여 영향을 받는 리소스를 확인합니다.

언급된 날짜 동안 리소스에서 전송이 없는 것으로 확인되면 Azure 기술 팀에 문의하세요. 이 팀은 문제를 야기하는 버그 또는 인시던트가 있는지 확인하는 데 도움을 줄 수 있습니다. 이 예제에서 영향을 받는 리소스는 스토리지 계정입니다. 따라서, Azure Storage 기술 팀에 문의합니다. 마찬가지로 급증이 가상 머신에 영향을 주는 경우 Azure Virtual Machines 기술 팀에 문의하여 Virtual Machine 서비스에 영향을 주는 진행 중인 인시던트가 있는지 확인합니다.

진행 중인 인시던트가 있는 경우 Azure 기술 팀은 Azure 대금 청구 팀과 협력하여 환불 요청을 검토합니다.

### <a name="tools-to-monitor-azure-usage"></a>Azure 사용량을 모니터링하는 도구

언제든지 Azure Cost Management를 사용하여 비용을 관리하고 예산을 책정할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [Azure 예산으로 비용 관리](../articles/cost-management-billing/manage/cost-management-budget-scenario.md)
- [자습서: Azure 예산 만들기 및 관리](../articles/cost-management-billing/costs/tutorial-acm-create-budgets.md)

스토리지 사용량의 경우 Storage Analytics 도구를 사용하는 것이 좋습니다. 이를 통해 트랜잭션별 로깅을 사용할 수 있습니다. 로그는 자세히 설명되어 있지만 포괄적인 추적 및 디버깅을 직접 수행할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [스토리지 분석](../articles/storage/common/storage-analytics.md)
- [스토리지 분석 로그 형식](/rest/api/storageservices/Storage-Analytics-Log-Format)
- [Azure 스토리지 분석 로그(클래식) 사용 및 관리](../articles/storage/common/manage-storage-analytics-logs.md)

네트워크 관련 사용량의 경우 [네트워크 모니터](https://www.microsoft.com/download/details.aspx?id=4865) 또는 Fiddler와 같은 네트워크 캡처 도구를 사용할 수 있습니다.

Windows 운영 체제 이미지의 Virtual Machine과 관련된 문제의 경우 [Windows 이벤트 로그](/windows/win32/wes/windows-event-log)를 사용할 수 있습니다.

PaaS(Platform as a Service) 배포의 경우 애플리케이션에서 [Azure Diagnostics를 사용](../articles/cloud-services/cloud-services-dotnet-diagnostics.md)하도록 설정합니다.

IaaS(Infrastructure as a Service) 배포의 경우 [Windows Communication Foundation 추적](/dotnet/framework/wcf/diagnostics/tracing/configuring-tracing)을 사용하도록 설정합니다.

[IIS 8.5에 대한 향상된 로깅](/iis/get-started/whats-new-in-iis-85/enhanced-logging-for-iis85)을 사용하도록 설정합니다.

[Azure App Service에서 웹 앱에 대한 진단 로깅](../articles/app-service/troubleshoot-diagnostic-logs.md)을 사용하도록 설정합니다.

상황에 대한 자세한 내용과 조언은 Microsoft 고객 성공 계정 관리자에게 문의하여 클라우드 솔루션 설계자로부터 지원을 요청하세요.