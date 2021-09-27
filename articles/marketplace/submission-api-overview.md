---
title: 파트너 센터 상업용 Marketplace 제출 API 개요
description: 상업용 Marketplace 제출 API에 대한 개요를 얻습니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 09/22/2021
ms.openlocfilehash: 246f57b09e075baaa17296e0c81c2f756e0f42ac
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057084"
---
# <a name="commercial-marketplace-submission-api-overview"></a>상업용 Marketplace 제출 API 개요

API를 사용하여 프로그래밍 방식으로 제안을 쿼리하고, 제출을 만들고, 게시합니다. API는 계정이 많은 제안을 관리하고 이러한 제안에 대한 제출 프로세스를 자동화하고 최적화하려는 경우에 유용합니다.

## <a name="types-of-apis"></a>API 유형

다음 두 가지 제출 API 집합을 사용할 수 있습니다.

- **파트너 센터 제출 API** – 소비자 및 상용 제품에서 작동하여 파트너 센터 통해 게시하는 일반적인 API 집합입니다. 새 기능은 이 API 집합에 지속적으로 추가됩니다. 이 API와 통합하는 방법에 대한 자세한 내용은 [파트너 센터 제출 API 온보딩을 참조하세요.](submission-api-onboard.md) 
- **레거시 Cloud 파트너 포털 API** – 사용되지 Cloud 파트너 포털 API 와 통합되고 파트너 센터 계속 작동합니다. 이 API 집합은 유지 관리 모드에만 있습니다. 파트너 센터 도입된 새로운 기능은 지원되지 않을 수 있으며, 파트너 센터 전환하기 전에 이미 통합된 기존 제품에만 사용해야 합니다. Cloud 파트너 포털 API를 계속 사용하는 방법에 대한 자세한 내용은 [Cloud 파트너 포털 API 참조를 참조하세요.](cloud-partner-portal-api-overview.md)

각 제안 유형에 대해 지원되는 제출 API는 다음 표를 참조합니다.

| 제품 유형 | 레거시 Cloud 파트너 포털 API 지원 | 파트너 센터 제출 API 지원 |
| --- | :---: | :---: |
| Azure 애플리케이션 |  | &#x2714; |
| Azure Container | &#x2714; |  |
| Azure Virtual Machine | &#x2714; |  |
| 컨설팅 서비스 | &#x2714; |  |
| Dynamics 365 |  | &#x2714; |
| IoT Edge 모듈 | &#x2714; |  |
| 관리형 서비스 | &#x2714; |  |
| Power BI App | &#x2714; |  |
| 서비스 제공 소프트웨어(Software as a Service) |  | &#x2714; |
|

Microsoft 365 Office 추가 기능, Microsoft 365 SharePoint 솔루션, Microsoft 365 Teams 앱 및 Power BI Visuals에는 제출 API 지원이 없습니다.

## <a name="next-steps"></a>다음 단계

- 필요에 따라 제안 유형에 적합한 API 링크를 방문하세요.
