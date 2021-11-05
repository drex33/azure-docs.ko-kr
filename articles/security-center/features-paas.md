---
title: 지원되는 Azure PaaS 리소스에 대한 Microsoft Defender for Cloud 기능.
description: 이 페이지에서는 지원되는 Azure PaaS 리소스에 대한 Microsoft Defender for Cloud 기능의 가용성을 보여줍니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/25/2021
ms.author: memildin
ms.openlocfilehash: c4ed4868cdaa8363419d1f4ca5e9267509df5ed4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437983"
---
# <a name="feature-coverage-for-azure-paas-services"></a>Azure PaaS 서비스의 기능 적용 범위 <a name="paas-services"></a>

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

아래 표에서는 지원되는 Azure PaaS 리소스에 대한 Microsoft Defender for Cloud 기능의 가용성을 보여줍니다.

|서비스|권장 사항(무료)|보안 경고 |취약점 평가|
|:----|:----:|:----:|:----:|
|Azure App Service|✔|✔|-|
|Azure Automation 계정|✔|-|-|
|Azure Batch 계정|✔|-|-|
|Azure Blob Storage|✔|✔|-|
|Azure Cache for Redis|✔|-|-|
|Azure Cloud Services|✔|-|-|
|Azure Cognitive Search|✔|-|-|
|Azure Container Registry|✔|✔|✔|
|Azure Cosmos DB*|✔|✔|-|
|Azure 데이터 레이크 분석|✔|-|-|
|Azure Data Lake Storage|✔|✔|-|
|Azure Database for MySQL*|-|✔|-|
|Azure Database for PostgreSQL*|-|✔|-|
|Azure Event Hubs 네임스페이스|✔|-|-|
|Azure Functions 앱|✔|-|-|
|Azure Key Vault|✔|✔|-|
|Azure Kubernetes Service|✔|✔|-|
|Azure Load Balancer|✔|-|-|
|Azure Logic Apps|✔|-|-|
|Azure SQL Database|✔|✔|✔|
|Azure SQL Managed Instance|✔|✔|✔|
|Azure Service Bus 네임스페이스|✔|-|-|
|Azure Service Fabric 계정|✔|-|-|
|Azure Storage 계정|✔|✔|-|
|Azure Stream Analytics|✔|-|-|
|Azure 구독|✔ **|✔|-|
|Azure Virtual Network</br> (서브넷, NIC 및 네트워크 보안 그룹 포함)|✔|-|-|

\* 이러한 기능은 현재 미리 보기로 지원됩니다.

\*\* Azure AD(Azure Active Directory) 권장 사항은 [향상된 보안 기능이 활성화](enable-enhanced-security.md)된 구독에만 사용할 수 있습니다.