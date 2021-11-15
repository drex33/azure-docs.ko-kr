---
title: 포함 파일
description: 포함 파일
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 04/24/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 282fee2bdb9fa6cd063d2c041b57bb1ee5513926
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132353974"
---
이러한 샘플은 교차 테넌트 관리 시나리오에서 수행할 수 있는 다양한 작업을 보여 줍니다.

| **템플릿** | **설명** |
|---------|---------|
| [`create-keyvault-secret`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret) | 고객의 테넌트에 Key Vault를 만들고 액세스 정책을 만듭니다.
| [`cross-rg-deployment`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-rg-deployment) | 스토리지 계정을 두 개의 서로 다른 리소스 그룹에 배포합니다.|
| [`deploy-azure-mgmt-services`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-mgmt-services) | Azure 관리 서비스를 만들고, 함께 연결하고, 추가 솔루션을 배포합니다. 엔드투엔드 배포의 경우 **rgWithAzureMgmt.json** 템플릿을 사용합니다. |
| [`deploy-azure-security-center`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-security-center) | 대상 Azure 구독 내에서 Microsoft Defender for Cloud를 사용하도록 설정하고 구성합니다. |
| [`deploy-azure-sentinel`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-azure-sentinel) | 위임된 구독의 기존 Log Analytics 작업 영역에서 Microsoft Sentinel을 배포하고 사용하도록 설정합니다. |
| [`deploy-log-analytics-vm-extensions`](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/deploy-log-analytics-vm-extensions) | 이러한 템플릿을 사용하여 Windows 및 Linux VM에 Log Analytics VM 확장을 배포하여 지정된 Log Analytics 작업 영역에 연결할 수 있습니다. |
