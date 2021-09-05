---
title: Azure Firewall 데이터를 Azure Sentinel에 연결
description: Azure Firewall 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: f4683708f8fdd4eda2f483cc4112fe9fe9939ce6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536712"
---
# <a name="connect-data-from-azure-firewall"></a>Azure Firewall에서 데이터 연결

Azure Firewall은 Azure Virtual Network 리소스를 보호하는 관리되는 클라우드 기반 네트워크 보안 서비스입니다. 고가용성 및 무제한 클라우드 확장성이 내장되어 있는 서비스 형태의 완전한 상태 저장 방화벽입니다. 

Azure Firewall 로그를 Azure Sentinel에 연결하여 통합 문서에서 로그 데이터를 볼 수 있도록 설정하고, 이 데이터를 사용하여 사용자 지정 경고를 만들고, 이 경고를 통합하여 조사를 개선할 수 있습니다.

[Azure Firewall 로그 모니터링](../firewall/firewall-diagnostics.md)에 대해 자세히 알아봅니다.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>필수 구성 요소

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

## <a name="connect-to-azure-firewall"></a>Azure Firewall에 연결
    
1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 데이터 커넥터 갤러리에서 **Azure Firewall** 을 선택한 다음, 미리 보기 창에서 **커넥터 페이지 열기** 를 선택합니다.

1. 연결하려는 로그가 있는 모든 방화벽에서 **진단 로그** 를 사용하도록 설정합니다.

    1. **Azure Firewall 리소스 열기 >** 링크를 선택합니다.

    1. **방화벽** 탐색 메뉴에서 **진단 설정** 을 선택합니다.

    1. 목록의 맨 아래에 있는 **+ 진단 설정 추가** 를 선택합니다.

    1. **진단 설정** 화면에서 **진단 설정 이름** 필드에 이름을 입력합니다.
    
    1. **Log Analytics에 보내기** 확인란을 선택합니다. 두 개의 새 필드가 아래에 표시됩니다. 관련 **구독** 및 **Log Analytics 작업 영역** 을 선택합니다(Azure Sentinel이 있는 위치).

    1. 수집하려는 로그가 있는 규칙 유형의 확인란을 표시합니다. **AzureFirewallApplicationRule**, **AzureFirewallNetworkRule** 및 **AzureFirewallDNSProxy** 가 권장됩니다.

    1. 화면 위쪽에서 **저장** 을 선택합니다.

1. Azure Firewall 경고에 대한 Log Analytics에서 관련 스키마를 사용하려면 **AzureDiagnostics** 를 검색합니다.

> [!NOTE]
>
> 이 특정 데이터 커넥터를 사용하면 연결 상태 표시기(데이터 커넥터 갤러리의 색상 줄무늬 및 데이터 유형 이름 옆의 연결 아이콘)가 지난 2주 동안 어느 지점에서 데이터를 수집한 경우에만 *연결됨*(녹색)으로 표시됩니다. 2주 동안 데이터 수집이 없으면 커넥터는 연결 끊김으로 표시됩니다. 더 많은 데이터가 수신되는 순간 *연결됨* 상태가 반환됩니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Firewall 로그를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
