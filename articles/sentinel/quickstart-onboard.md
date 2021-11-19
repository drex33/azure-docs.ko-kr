---
title: '빠른 시작: Microsoft Sentinel의 온보딩'
description: 이 빠른 시작에서는 먼저 Microsoft Sentinel을 사용하도록 설정한 다음, 데이터 원본을 연결하여 온보딩하는 방법을 알아봅니다.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: quickstart
ms.date: 11/09/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 46ac4308035491dc2d6b75e34bc0c58a58f93535
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520810"
---
# <a name="quickstart-on-board-microsoft-sentinel"></a>빠른 시작: Microsoft Sentinel 온보딩

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 빠른 시작에서는 Microsoft Sentinel을 온보딩하는 방법을 알아봅니다. Microsoft Sentinel을 온보딩하려면 먼저 Microsoft Sentinel을 사용하도록 설정한 다음, 데이터 원본을 연결해야 합니다.

Microsoft Sentinel에는 Microsoft 365 Defender(이전에는 Microsoft Threat Protection) 솔루션, Microsoft 365 원본(Office 365 포함), Azure AD, Microsoft Defender for Identity(이전에는 Azure ATP), Microsoft Defender for Cloud Apps, Microsoft Defender for Cloud의 보안 경고를 포함하여 즉시 사용 가능하고 실시간 통합을 제공하는 다양한 Microsoft 솔루션용 커넥터가 포함되어 있습니다. 또한 타사 솔루션에 대한 광범위한 보안 에코시스템에 기본 제공 커넥터도 제공됩니다. CEF(Common Event Format), Syslog 또는 REST API를 사용하여 Microsoft Sentinel에 데이터 원본을 연결할 수도 있습니다.

데이터 원본을 연결한 후 데이터에 따른 인사이트를 제공하는 전문가가 만든 통합 문서 갤러리 중에서 선택합니다. 이러한 통합 문서는 사용자의 요구에 맞게 쉽게 사용자 지정할 수 있습니다.

>[!IMPORTANT]
> Microsoft Sentinel을 사용할 때 발생하는 요금에 대한 자세한 내용은 [Microsoft Sentinel 가격](https://azure.microsoft.com/pricing/details/azure-sentinel/) 및 [Microsoft Sentinel 비용 및 청구](azure-sentinel-billing.md)를 참조하세요.

## <a name="global-prerequisites"></a>전역 전제 조건

- 활성 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- Log Analytics 작업 영역. [Log Analytics 작업 영역을 만드는 방법](../azure-monitor/logs/quick-create-workspace.md)을 알아봅니다. Log Analytics 작업 영역에 대한 자세한 내용은 [Azure Monitor 로그 배포 디자인](../azure-monitor/logs/design-logs-deployment.md)을 참조하세요.

- Microsoft Sentinel을 사용하도록 설정하려면 Microsoft Sentinel 작업 영역이 있는 구독에 대한 기여자 권한이 필요합니다. 
- Microsoft Sentinel을 사용하려면 작업 영역이 속한 리소스 그룹에 대해 기여자 또는 읽기 권한자 권한이 있어야 합니다.
- 특정 데이터 원본을 연결하려면 추가 권한이 필요할 수 있습니다.
- Microsoft Sentinel은 유료 서비스입니다. 자세한 내용은 [Microsoft Sentinel 정보](https://go.microsoft.com/fwlink/?linkid=2104058)를 참조하세요.

자세한 내용은 [Microsoft Sentinel 배포를 위한 사전 배포 활동 및 필수 조건](prerequisites.md)을 참조하세요.

### <a name="geographical-availability-and-data-residency"></a>지리적 가용성 및 데이터 상주

- Microsoft Sentinel은 중국과 독일(소버린) 지역을 제외한 대부분의 [Log Analytics의 GA 지역](https://azure.microsoft.com/global-infrastructure/services/?products=monitor)의 작업 영역에서 실행할 수 있습니다. 새 Log Analytics 지역은 Microsoft Sentinel 서비스를 온보딩하는 데 시간이 걸릴 수 있습니다. 

- 인시던트, 책갈피 및 분석 규칙과 같이 Microsoft Sentinel에서 생성된 데이터에는 고객의 Log Analytics 작업 영역에서 소싱된 일부 고객 데이터가 포함될 수 있습니다. 이 Microsoft Sentinel에서 생성된 데이터는 작업 영역이 있는 지리에 따라 다음 표에 나열된 지리에 저장됩니다.

    | 작업 영역 지리 | Microsoft Sentinel 생성 데이터 지리 |
    | --- | --- |
    | 미국<br>인도 | 미국 |
    | 유럽<br>프랑스 | 유럽 |
    | 오스트레일리아 | 오스트레일리아 |
    | United Kingdom | United Kingdom |
    | Canada | Canada |
    | 일본 | 일본 |
    | 아시아 태평양 | 아시아 태평양 * |
    | 브라질 | 브라질 * |
    | 노르웨이 | 노르웨이 |
    | 아프리카 | 아프리카 |
    | 한국 | 한국 |
    | 독일 | 독일 |
    | 아랍에미리트연합국 | 아랍에미리트연합국 |
    | 스위스 | 스위스 |
    |

    \* 단일 지역 데이터 상주는 현재 아시아 태평양 지역의 동남아시아 지역(싱가포르)과 브라질 지역의 브라질 남부(상파울루 주) 지역에서만 제공됩니다. 다른 모든 지역의 경우 고객 데이터는 Sentinel이 온보딩된 작업 영역의 지리적 위치에 저장할 수 있습니다.

    > [!IMPORTANT]
    > - ML(기계 학습) 엔진을 사용하는 특정 규칙을 사용하도록 설정하여 **Microsoft Sentinel 작업 영역 지리 외부에서 수집된 관련 데이터를 복사하는 권한을 Microsoft에 제공** 합니다. 이 권한은 기계 학습 엔진에서 이러한 규칙을 처리하는 데 필요할 수 있습니다.

## <a name="enable-microsoft-sentinel"></a>Microsoft Sentinel 사용 <a name="enable"></a>

1. Azure Portal에 로그인합니다. Microsoft Sentinel을 이 만들어지는 구독이 선택되었는지 확인합니다.

1. **Microsoft Sentinel** 을 검색하여 선택합니다.

   ![서비스 검색](./media/quickstart-onboard/search-product.png)

1. **추가** 를 선택합니다.

1. 사용하려는 작업 영역을 선택하거나 새 작업 영역을 만듭니다. 둘 이상의 작업 영역에서 Microsoft Sentinel을 실행할 수 있지만 데이터는 단일 작업 영역으로 격리됩니다.

   ![작업 영역 선택](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Microsoft Defender for Cloud에서 만든 기본 작업 영역은 목록에 표시되지 않습니다. Microsoft Sentinel을 설치할 수 없습니다.
   >

   >[!IMPORTANT]
   >
   > - 작업 영역에 배포되면 Microsoft Sentinel은 해당 작업 영역을 다른 리소스 그룹 또는 구독으로 이동하는 것을 **현재 지원하지 않습니다**. 
   >
   >   작업 영역을 이미 이동한 경우 **Analytics** 에서 모든 활성 규칙을 사용하지 않도록 설정하고 5분 후에 다시 사용하도록 설정합니다. 대부분의 경우에 이를 효과적으로 수행해야 하지만, 이는 지원되지 않으며 사용자의 책임하에 수행됩니다.

1. **Microsoft Sentinel 추가** 를 선택합니다.

## <a name="connect-data-sources"></a>데이터 원본 연결

Microsoft Sentinel은 서비스에 연결하고 이벤트 및 로그를 Microsoft Sentinel에 전달하여 서비스 및 앱에서 데이터를 수집합니다. 실제 및 가상 머신의 경우 로그를 수집하고 Microsoft Sentinel에 전달하는 Log Analytics 에이전트를 설치할 수 있습니다. 방화벽 및 프록시의 경우 Microsoft Sentinel은 Linux Syslog 서버에 Log Analytics 에이전트를 설치합니다. 여기서 에이전트는 로그 파일을 수집하여 Microsoft Sentinel에 전달합니다. 
 
1. 주 메뉴에서 **데이터 커넥터** 를 선택합니다. 그러면 데이터 커넥터 갤러리가 열립니다.

1. 갤러리는 연결할 수 있는 모든 데이터 원본의 목록입니다. 데이터 원본을 선택한 다음, **커넥터 페이지 열기** 단추를 선택합니다.

1. 커넥터 페이지에는 커넥터 구성 지침과 필요한 추가 지침이 표시됩니다.

    예를 들어 Azure AD에서 Microsoft Sentinel로 로그를 스트림할 수 있는 **Azure Active Directory** 데이터 원본을 선택하는 경우 가져올 로그 유형(로그인 로그 및/또는 감사 로그)을 선택할 수 있습니다. <br> 자세한 내용은 설치 지침을 따르거나 [관련 연결 가이드](data-connectors-reference.md)를 참조하세요. 데이터 커넥터에 대한 자세한 내용은 [Microsoft Sentinel 데이터 커넥터](connect-data-sources.md)를 참조하세요.

1. 커넥터 페이지의 **다음 단계** 탭에는 데이터 커넥터와 함께 제공되는 관련 기본 제공 통합 문서, 샘플 쿼리 및 분석 규칙 템플릿이 표시됩니다. 이를 그대로 사용하거나 수정할 수 있습니다. 두 가지 방법 모두 데이터에 대한 흥미로운 인사이트를 즉시 가져올 수 있습니다.

데이터 원본이 연결된 후 데이터는 Microsoft Sentinel로 스트림되기 시작하고 작업을 시작할 준비가 됩니다. [기본 제공 통합 문서](get-visibility.md)에서 로그를 확인하고 Log Analytics에서 쿼리를 작성하여 [데이터를 조사](investigate-cases.md)할 수 있습니다.

자세한 내용은 [데이터 수집 모범 사례](best-practices-data.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- **대체 배포 옵션**:

    - [API를 통해 Microsoft Sentinel 배포](/rest/api/securityinsights/)
    - [PowerShell을 통해 Microsoft Sentinel 배포](https://www.powershellgallery.com/packages/Az.SecurityInsights/0.1.0)
    - [ARM 템플릿을 통해 Microsoft Sentinel 배포](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-all-in-one-accelerator/ba-p/1807933)

- **시작**:
    - [Microsoft Sentinel 시작](get-visibility.md)
    - [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)
    - [Common Event Format을 사용하여 외부 솔루션 연결](connect-common-event-format.md)
