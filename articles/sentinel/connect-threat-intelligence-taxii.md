---
title: Azure Sentinel을 STIX/TAXII 위협 인텔리전스 피드에 연결 | Microsoft Docs
description: Azure Sentinel을 업계 표준 위협 인텔리전스 피드에 연결하여 위협 지표를 가져오는 방법에 대해 알아봅니다.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: a3e0381654e8a4d80c9eccffd6e02207b997fad8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004362"
---
# <a name="connect-azure-sentinel-to-stixtaxii-threat-intelligence-feeds"></a>Azure Sentinel을 STIX/TAXII 위협 인텔리전스 피드에 연결

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**참고 항목**: [Azure Sentinel을 TIP(위협 인텔리전스 플랫폼)에 연결](connect-threat-intelligence-tip.md)

위협 인텔리전스 전송을 위한 가장 널리 채택된 업계 표준은 [STIX 데이터 형식과 TAXII 프로토콜의 조합](https://oasis-open.github.io/cti-documentation/)입니다. 조직이 현재 STIX/TAXII 버전(2.0 또는 2.1)을 지원하는 솔루션에서 위협 지표를 가져오는 경우 **위협 인텔리전스 - TAXII 데이터 커넥터** 를 사용하여 Azure Sentinel로 위협 지표를 가져올 수 있습니다. 이 커넥터를 사용하면 Azure Sentinel에서 기본 제공된 TAXII 클라이언트를 사용하여 TAXII 2.x 서버로부터 위협 인텔리전스를 가져올 수 있습니다.

:::image type="content" source="media/connect-threat-intelligence-taxii/threat-intel-taxii-import-path.png" alt-text="TAXII 가져오기 경로":::

Azure Sentinel에서 [위협 인텔리전스](understand-threat-intelligence.md), 특히 Azure Sentinel과 통합할 수 있는 [TAXII 위협 인텔리전스 피드](threat-intelligence-integration.md#taxii-threat-intelligence-feeds)에 대해 자세히 알아봅니다.

## <a name="prerequisites"></a>필수 조건  

- 위협 지표를 저장할 수 있는 Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.
- TAXII 2.0 또는 TAXII 2.1 **API 루트 URI** 및 **컬렉션 ID** 가 있어야 합니다.

## <a name="instructions"></a>Instructions

TAXII 서버에서 Azure Sentinel로 STIX 형식 위협 지표를 가져오려면 다음 단계를 수행합니다.

1. TAXII 서버 API 루트 및 컬렉션 ID 가져오기

1. Azure Sentinel에서 위협 인텔리전스 - TAXII 데이터 커넥터를 사용하도록 설정

### <a name="get-the-taxii-server-api-root-and-collection-id"></a>TAXII 서버 API 루트 및 컬렉션 ID 가져오기

TAXII 2.x 서버는 위협 인텔리전스의 컬렉션을 호스트하는 URL인 API 루트를 보급합니다. 일반적으로 TAXII 서버를 호스팅하는 위협 인텔리전스 공급자의 설명서 페이지에서 API 루트 및 컬렉션 ID를 찾을 수 있습니다. 

> [!NOTE]
> 경우에 따라 공급자는 검색 엔드포인트라는 URL만 보급합니다. [아래에서 설명된](#find-the-api-root) 것처럼 cURL 유틸리티를 사용하여 검색 엔드포인트를 찾아보고 API 루트를 요청할 수 있습니다.

### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>Azure Sentinel에서 위협 인텔리전스 - TAXII 데이터 커넥터를 사용하도록 설정

TAXII 서버에서 Azure Sentinel로 위협 지표를 가져오려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 **Azure Sentinel** 서비스로 이동합니다.

1. TAXII 서버에서 위협 지표를 가져올 **작업 영역** 을 선택합니다.

1. 메뉴에서 **데이터 커넥터** 를 선택하고 커넥터 갤러리에서 **위협 인텔리전스 - TAXII** 를 선택한 다음, **커넥터 페이지 열기** 단추를 선택합니다.

1. 이 TAXII 서버 컬렉션에 사용할 **친숙한 이름**, **API 루트 URL**, **컬렉션 ID**, **사용자 이름**(필요한 경우) 및 **암호**(필요한 경우)를 입력하고, 지표의 그룹 및 원하는 폴링 빈도를 선택합니다. **추가** 단추를 선택합니다.

    :::image type="content" source="media/connect-threat-intelligence-taxii/threat-intel-configure-taxii-servers.png" alt-text="TAXII 서버 구성":::
 
TAXII 서버에 대한 연결이 성공적으로 설정되었다는 확인 메시지가 표시되고, 하나 이상의 TAXII 서버에서 여러 컬렉션에 연결하기 위해 원하는 횟수만큼 위의 마지막 단계를 반복할 수 있습니다.

몇 분 후에 위협 지표가 이 Azure Sentinel 작업 영역으로 이동하기 시작합니다. Azure Sentinel 탐색 메뉴에서 액세스할 수 있는 **위협 인텔리전스** 블레이드에서 새 지표를 찾을 수 있습니다.

### <a name="find-the-api-root"></a>API 루트 찾기

Windows 및 대부분의 Linux 배포판에서 제공되는 [cURL](https://en.wikipedia.org/wiki/CURL) 명령줄 유틸리티를 사용하여 API 루트를 검색하고 검색 엔드포인트에서만 시작하는 TAXII 서버의 컬렉션을 검색하는 실제 예제를 살펴보겠습니다. [Anomali Limo](https://www.anomali.com/community/limo) ThreatStream TAXII 2.0 서버의 검색 엔드포인트를 사용하여 API 루트 URI 및 컬렉션을 요청할 수 있습니다.

1.  브라우저에서 https://limo.anomali.com/taxii 의 ThreatStream TAXII 2.0 서버 검색 엔드포인트로 이동하여 API 루트를 검색합니다. 사용자 이름 및 암호 `guest`를 사용하여 인증합니다.

    다음과 같은 응답이 표시됩니다.

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  cURL 유틸리티 및 API 루트(이전 응답의 https://limo.anomali.com/api/v1/taxii2/feeds/) )를 사용하여 API 루트에 “`collections/`”를 추가하여 API 루트에서 호스트되는 컬렉션 ID 목록을 찾습니다.

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    ```
    “guest” 암호를 사용하여 다시 인증한 후에는 다음 응답을 받게 됩니다.

    ```json
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

이제 Anomali Limo에서 제공하는 하나 이상의 TAXII 서버 컬렉션에 Azure Sentinel을 연결하는 데 필요한 정보가 모두 있습니다.

| **API 루트**(https://limo.anomali.com/api/v1/taxii2/feeds/) | 컬렉션 ID |
| ------------------------------------------------------------ | ------------: |
| **Phish Tank**                                               | 107           |
| **Abuse.ch Ransomware IPs**                                  | 135           |
| **Abuse.ch Ransomware Domains**                              | 136           |
| **DShield Scanning IPs**                                     | 150           |
| **Malware Domain List - Hotlist**                            | 200           |
| **Blutmagie TOR Nodes**                                      | 209           |
| **Emerging Threats C&C Server**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **CyberCrime**                                               |  41           |
| **Emerging Threats - Compromised**                           |  68           |
|

## <a name="next-steps"></a>다음 단계

이 문서에서는 TAXII 프로토콜을 사용하여 Azure Sentinel을 위협 인텔리전스 피드에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 잠재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./detect-threats-built-in.md)을 시작합니다.
