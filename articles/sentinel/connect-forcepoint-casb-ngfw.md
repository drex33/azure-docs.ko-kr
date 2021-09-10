---
title: Forcepoint 제품을 Azure Sentinel에 연결 | Microsoft Docs
description: Azure Sentinel에 Forcepoint 제품을 연결하는 방법을 알아봅니다.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: b0b734bcc540651debad35606be496fdc4069b3c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695116"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Forcepoint 제품을 Azure Sentinel에 연결

> [!IMPORTANT]
> Azure Sentinel의 Forcepoint 제품 데이터 커넥터는 현재 퍼블릭 미리 보기로 제공됩니다. 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

이 문서는 Forcepoint 제품을 Azure Sentinel에 연결하는 방법을 설명합니다. 

Forcepoint 데이터 커넥터를 사용하여 Forcepoint Cloud Access Security Broker 및 Forcepoint Next Generation Firewall 로그를 Azure Sentinel에 연결할 수 있습니다. 이러한 방식으로 사용자 정의 로그를 실시간으로 Azure Sentinel로 자동으로 내보낼 수 있습니다. 이 커넥터는 Forcepoint 제품에 기록된 사용자 활동을 보다 잘 볼 수 있도록 지원합니다. 또한 Azure 워크로드 및 다른 피드의 데이터와의 상관 관계를 설정하고 Azure Sentinel 내의 통합 문서로 모니터링 기능을 향상시킵니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Forcepoint 제품 로그를 Syslog 에이전트로 전달 

Syslog 에이전트를 통해 CEF 형식의 Syslog 메시지를 Azure 작업 영역으로 전달하도록 Forcepoint 제품을 구성합니다.

1. 다음 설치 가이드에 설명된 대로 Azure Sentinel로의 Forcepoint 제품 통합을 설정합니다.
 - [Forcepoint CASB 통합 가이드](https://frcpnt.com/casb-sentinel)
 - [Forcepoint NGFW 통합 가이드](https://frcpnt.com/ngfw-sentinel)

2. CommonSecurityLog를 검색하여 DeviceVendor 이름에 'Forcepoint'가 포함된 Log Analytics에서 관련 스키마를 사용합니다. 

3. [CEF 연결 유효성 검사](troubleshooting-cef-syslog.md#validate-cef-connectivity)로 계속 진행합니다.



## <a name="next-steps"></a>다음 단계

이 문서에서는 Forcepoint 제품을 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.

- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.

- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.