---
title: Azure Sentinel에 Cisco Umbrella 연결| Microsoft Docs
description: Cisco Umbrella 데이터 커넥터를 사용하여 Azure Sentinel로 Umbrella 데이터를 끌어오는 방법에 대해 알아봅니다. Umbrella 데이터를 통합 문서에서 보고, 경고를 만들고, 조사를 개선합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a6a32f33eb88ac82793445badef25227f93fd809
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530438"
---
# <a name="connect-your-cisco-umbrella-to-azure-sentinel"></a>Azure Sentinel에 Cisco Umbrella 연결

> [!IMPORTANT]
> Cisco Umbrella 커넥터는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Cisco Umbrella 커넥터를 사용하면 S3 API를 통해 Amazon Web Services S3 버킷에 연결하여 Cisco Umbrella 보안 솔루션 로그를 Azure Sentinel에 수집할 수 있습니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-cisco-umbrella"></a>Cisco Umbrella 구성 및 연결

Cisco Umbrella는 Amazon S3 API를 통해 Azure Sentinel에 로그를 통합하고 내보낼 수 있습니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. **데이터 커넥터** 갤러리에서 **Cisco Umbrella(미리 보기)** 를 선택한 다음, **커넥터 페이지를 엽니다**.

1. 커넥터 페이지의 **구성** 섹션에 설명된 단계를 따릅니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 데이터는 **로그** 에서 **CustomLogs** 섹션의 다음 표 중 하나 이상에 표시됩니다.
- `Cisco_Umbrella_dns_CL`
- `Cisco_Umbrella_proxy_CL`
- `Cisco_Umbrella_ip_CL`
- `Cisco_Umbrella_cloudfirewall_CL`

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Cisco Umbrella 데이터를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.