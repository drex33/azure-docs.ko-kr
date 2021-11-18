---
title: 일반적으로 사용되는 Microsoft Sentinel 통합 문서 | Microsoft Docs
description: 인기 있는 Microsoft Sentinel 리소스를 사용하기 위해 가장 일반적으로 사용되는 통합 문서에 대해 알아봅니다.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: a1cc4d9eca7f55dfbd7de9da9c71959d1bcfad70
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132711499"
---
# <a name="commonly-used-microsoft-sentinel-workbooks"></a>일반적으로 사용되는 Microsoft Sentinel 통합 문서

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

다음 표에서는 가장 일반적으로 사용되는 기본 제공 Microsoft Sentinel 통합 문서를 나열합니다.

왼쪽의 **위협 관리** 통합 문서에 있는 Microsoft Sentinel의  >  **통합 문서에** 액세스한 다음 사용할 통합 문서를 검색합니다. 자세한 내용은 [데이터 시각화 및 모니터링](monitor-your-data.md)을 참조하세요.

> [!TIP]
> 수집 중인 데이터와 연결된 통합 문서를 배포하는 것이 좋습니다. 통합 문서를 사용하면 수집된 데이터를 기반으로 광범위한 모니터링 및 조사를 할 수 있습니다.
>
> 자세한 내용은 [커넥트 데이터 원본](connect-data-sources.md) 및 [중앙 집중식으로 Microsoft Sentinel 콘텐츠 및 솔루션 검색 및 배포를 참조하세요.](sentinel-solutions-deploy.md)
>

|통합 문서 이름  |Description  |
|---------|---------|
|**분석 효율성**     |  SOC 성능을 향상하는 데 도움이 되는 분석 규칙의 효율성에 대한 인사이트를 제공합니다. <br><br>자세한 내용은 [Data-Driven SOC용 도구 키트](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152)를 참조하세요.|
|**Azure 활동**     |     모든 사용자 작업 및 이벤트를 분석하고 상관 관계를 지정하여 조직의 Azure 활동에 대한 광범위한 인사이트를 제공합니다. <br><br>자세한 내용은 [Azure 활동 로그를 사용하여 감사](audit-sentinel-data.md#auditing-with-azure-activity-logs)를 참조하세요.    |
|**Azure AD 감사 로그**     |  Azure Active Directory 감사 로그를 사용하여 Azure AD 시나리오에 대한 인사이트를 제공합니다. <br><br>자세한 내용은 [빠른 시작: Microsoft Sentinel 시작을 참조하세요.](get-visibility.md)     |
|**Azure AD 감사, 활동 및 로그인 로그**     |   하나의 통합 문서를 사용하여 Azure Active Directory 감사, 활동 및 로그인 데이터에 대한 인사이트를 제공합니다. 위치별 로그인, 디바이스, 오류 원인, 사용자 작업 등의 활동을 표시합니다. <br><br> 이 통합 문서를 보안 관리자와 Azure 관리자가 모두 사용할 수 있습니다.   |
|**Azure AD 로그인 로그**     | Azure AD 로그인 로그를 사용하여 Azure AD 시나리오에 대한 인사이트를 제공합니다.        |
|**CMMC(사이버 보안 성숙 모델 인증)**     |   Microsoft 보안 제품, Office 365, Teams, Intune, Windows Virtual Desktop 등을 비롯한 Microsoft 포트폴리오 전체에서 CMMC 컨트롤에 맞게 조정된 로그 쿼리를 볼 수 있는 메커니즘을 제공합니다. <br><br>자세한 내용은 [공개 미리 보기의 CMMC(사이버 보안 완성 모델 인증) 통합 문서](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cybersecurity-maturity-model-certification-cmmc/ba-p/2111184)를 참조하세요.|
|**데이터 수집 상태 모니터링** / **사용량 모니터링**     |  수집 크기, 대기 시간 및 원본당 로그 수와 같은 작업 영역의 데이터 수집 상태에 대한 인사이트를 제공합니다. 작업 영역 데이터 수집 상태를 결정하는 데 도움이 되는 모니터를 보고 이상을 감지합니다. <br><br>자세한 내용은 [이 Microsoft Sentinel 통합 문서를 사용하여 데이터 커넥터의 상태 모니터링을 참조하세요.](monitor-data-connector-health.md)    |
|**이벤트 분석기**     |  보안, 애플리케이션, 시스템, 설정, 디렉터리 서비스, DNS 등과 같은 모든 이벤트 세부 정보 및 특성을 포함하여 Windows 이벤트 로그 분석을 살펴보고 감사하고 속도를 높일 수 있습니다.       |
|**Exchange Online**     |모든 Exchange 작업 및 사용자 활동을 추적하고 분석하여 Microsoft Exchange Online에 대한 인사이트를 제공합니다.         |
|**ID 및 액세스**     |   감사 및 로그인 로그를 포함하는 보안 로그를 통해 Microsoft 제품 사용의 ID 및 액세스 작업에 대한 인사이트를 제공합니다.     |
|**인시던트 개요**     |   일반 정보, 엔터티 데이터, 심사 시간, 완화 시간, 주석을 비롯해 인시던트에 대한 심층 정보를 제공하여 심사 및 조사를 지원하도록 설계되었습니다. <br><br>자세한 내용은 [Data-Driven SOC용 도구 키트](https://techcommunity.microsoft.com/t5/azure-sentinel/the-toolkit-for-data-driven-socs/ba-p/2143152)를 참조하세요.      |
|<a name="investigation-insights"></a>**조사 인사이트**     | 분석가에게 인시던트, 책갈피 및 엔터티 데이터에 대한 인사이트를 제공합니다. 일반적인 쿼리 및 자세한 시각화는 분석가가 의심스러운 활동을 조사하는 데 도움이 될 수 있습니다.     |
|**Cloud Apps용 Microsoft Defender - 검색 로그**     |   조직에서 사용되는 클라우드 앱에 대한 세부 정보를 제공하고, 특정 사용자 및 애플리케이션에 대한 사용량 추세 및 드릴다운 데이터에서 통찰력을 제공합니다.  <br><br>자세한 내용은 [Cloud Apps용 Microsoft Defender의 데이터 커넥트 참조하세요.](./data-connectors-reference.md#microsoft-defender-for-cloud-apps)|
|**MITRE ATT&CK 통합 문서**     |   Microsoft Sentinel에 대한 MITRE ATT&CK 검사에 대한 세부 정보를 제공합니다.      |
|**Office 365**     | 모든 작업 및 활동을 추적하고 분석하여 Office 365에 대한 인사이트를 제공합니다. SharePoint, OneDrive, Teams 및 Exchange 데이터로 드릴다운합니다.       |
|**보안 경고**     |  Microsoft Sentinel 환경의 경고에 대한 보안 경고 대시보드를 제공합니다. <br><br>자세한 내용은 [Microsoft 보안 경고에서 인시던트 자동 생성](create-incidents-from-alerts.md)을 참조하세요.      |
|**보안 운영 효율성**     |  SOC(security operations center) 관리자가 팀의 성능에 대한 전반적인 효율성 메트릭과 측정값을 볼 수 있도록 설계되었습니다. <br><br>자세한 내용은 [인시던트 메트릭을 사용하여 효율적인 SOC 관리](manage-soc-with-incident-metrics.md)를 참조하세요.  |
|**위협 인텔리전스**     | 위협의 유형 및 심각도, 시간 경과에 따른 위협 활동, Office 365 및 방화벽을 비롯한 다른 데이터 원본과의 상관관계 등 위협 지표에 대한 인사이트를 제공합니다.  <br><br>자세한 내용은 [Microsoft Sentinel의 위협 인텔리전스 이해를 참조하세요.](understand-threat-intelligence.md)      |
|**제로 트러스트(TIC3.0)**     |  [Trusted Internet Connections 프레임워크](https://www.cisa.gov/trusted-internet-connections)를 안내하는 제로 트러스트 원칙에 대한 자동화된 시각화를 제공합니다.   <br><br>자세한 내용은 [제로 트러스트(TIC 3.0) 통합 문서 알림 블로그](https://techcommunity.microsoft.com/t5/public-sector-blog/announcing-the-azure-sentinel-zero-trust-tic3-0-workbook/ba-p/2313761)를 참조하세요.  |
