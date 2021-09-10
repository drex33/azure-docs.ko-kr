---
title: Azure Portal을 사용하여 프라이빗 엔드포인트에 대한 DNS 레코드 내보내기
titleSuffix: Azure Private Link
description: 이 자습서에서는 Azure Portal에서 프라이빗 엔드포인트에 대한 DNS 레코드를 내보내는 방법을 알아봅니다.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: how-to
ms.date: 07/25/2021
ms.custom: how-to
ms.openlocfilehash: d4ad1fd995bfd4f1565215491037e6450bd65bf2
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713125"
---
# <a name="export-dns-records-for-a-private-endpoint-using-the-azure-portal"></a>Azure Portal을 사용하여 프라이빗 엔드포인트에 대한 DNS 레코드 내보내기

Azure의 프라이빗 엔드포인트는 엔드포인트의 이름 확인을 위해 DNS 레코드를 요구합니다. DNS 레코드는 구성된 리소스에 대한 엔드포인트의 개인 IP 주소를 확인합니다. 엔드포인트의 DNS 레코드를 내보내려면 Portal에서 Private Link 센터를 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 구독에서 구성된 프라이빗 엔드포인트입니다. 이 문서의 예제에서는 Azure 웹앱에 대한 프라이빗 엔드포인트를 사용합니다. 웹앱에 대한 프라이빗 엔드포인트를 만드는 방법에 대한 자세한 내용은 [자습서: Azure 프라이빗 엔드포인트를 사용하여 웹앱에 연결](tutorial-private-endpoint-webapp-portal.md)을 참조하세요.

## <a name="export-endpoint-dns-records"></a>엔드포인트 DNS 레코드 내보내기

이 섹션에서는 Azure Portal에 로그인하고 Private Link 센터를 검색합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Portal 맨 위에 있는 검색 상자에 **퍼블릭 링크** 를 입력합니다.

3. **Private link** 를 선택합니다.

4. Private Link 센터에서 **프라이빗 엔드포인트** 를 선택합니다.

    :::image type="content" source="./media/private-endpoint-export-dns/private-link-center.png" alt-text="Private Link 센터에서 프라이빗 엔드포인트 선택":::

5. **프라이빗 엔드포인트** 에서 DNS 레코드를 내보낼 대상 엔드포인트를 선택합니다. 호스트 파일 형식으로 엔드포인트 DNS 레코드를 다운로드하려면 **호스트 파일 다운로드** 를 선택합니다.
    
    :::image type="content" source="./media/private-endpoint-export-dns/download-host-file.png" alt-text="엔드포인트 DNS 레코드 다운로드":::

6. 다운로드된 호스트 파일 레코드는 다음과 같이 표시됩니다.

    ```text
    # Exported from the Azure portal "2021-07-26 11:26:03Z"
    # Private IP    FQDN    Private Endpoint Id
    10.1.0.4    mywebapp8675.scm.azurewebsites.net    #/subscriptions/7cc654c6-760b-442f-bd02-1a8a64b17413/resourceGroups/myResourceGroup/providers/Microsoft.Network/privateEndpoints/mywebappendpoint
    10.1.0.4    mywebapp8675.azurewebsites.net    #/subscriptions/7cc654c6-760b-442f-bd02-1a8a64b17413/resourceGroups/myResourceGroup/providers/Microsoft.Network/privateEndpoints/mywebappendpoint
    ```

## <a name="next-steps"></a>다음 단계

Azure Private Link 및 DNS에 대한 자세한 내용은 [Azure 프라이빗 엔드포인트 DNS 구성](private-endpoint-dns.md)을 참조하세요.

Azure Private Link에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Private Link란?](private-link-overview.md)
* [Azure Private Link 서비스는 무엇입니까?](private-link-service-overview.md)
* [Azure Private Link FAQ(질문과 대답)](private-link-faq.yml)