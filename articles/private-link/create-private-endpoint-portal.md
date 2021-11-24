---
title: 빠른 시작 - Azure Portal을 사용하여 Private Endpoint 만들기
description: 이 빠른 시작을 사용하여 Azure Portal에서 Private Endpoint를 만드는 방법을 알아봅니다.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: allensu
ms.custom: mode-other
ms.openlocfilehash: eb9aff63425b4edcf46399c8e3b3f40d229fee09
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133047788"
---
# <a name="quickstart-create-a-private-endpoint-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Private Endpoint 만들기

Private Endpoint를 통해 Azure 웹앱에 안전하게 연결하여 Azure Private Link를 시작합니다.

이 빠른 시작에서는 Azure 웹앱에 대한 프라이빗 엔드포인트를 만들고, 가상 머신을 배포하여 프라이빗 연결을 테스트합니다.  

Azure SQL 및 Azure Storage와 같은 다양한 종류의 Azure 서비스에 대한 프라이빗 엔드포인트를 만들 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* **PremiumV2 계층** 이상의 App Service 요금제가 Azure 구독에 배포된 Azure 웹앱  
    * 자세한 내용 및 예제는 [빠른 시작: Azure에서 ASP.NET Core 웹앱 만들기](../app-service/quickstart-dotnetcore.md)를 참조하세요. 
    * 웹앱 및 엔드포인트를 만드는 방법에 대한 자세한 자습서는 [자습서: Azure Private Endpoint를 사용하여 웹앱에 연결](tutorial-private-endpoint-webapp-portal.md)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-virtual-network-and-bastion-host"></a>가상 네트워크 및 베스천 호스트 만들기

이 섹션에서는 가상 네트워크, 서브넷 및 베스천 호스트를 만듭니다. 

베스천 호스트는 가상 머신에 안전하게 연결하여 프라이빗 엔드포인트를 테스트하는 데 사용됩니다.

1. 화면의 왼쪽 위에서 **리소스 만들기 > 네트워킹 > 가상 네트워크** 를 차례로 선택하거나, 검색 상자에서 **가상 네트워크** 를 검색합니다.

2. **가상 네트워크 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | **설정**          | **값**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **프로젝트 세부 정보**  |                                                                 |
    | Subscription     | Azure 구독 선택                                  |
    | 리소스 그룹   | **CreatePrivateEndpointQS-rg** 를 선택합니다. |
    | **인스턴스 세부 정보** |                                                                 |
    | 속성             | **myVNet** 입력                                    |
    | 지역           | **서유럽** 를 선택합니다.|

3. **IP 주소** 탭을 선택하거나 페이지 하단의 **다음: IP 주소** 단추를 선택합니다.

4. **IP 주소** 탭에서 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | IPv4 주소 공간 | **10.1.0.0/16** 입력 |

5. **서브넷 이름** 아래에서 **기본값** 이라는 단어를 선택합니다.

6. **서브넷 편집** 에서 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | 서브넷 이름 | **mySubnet** 을 입력합니다. |
    | 서브넷 주소 범위 | **10.1.0.0/24** 입력 |

7. **저장** 을 선택합니다.

8. **보안** 탭을 선택합니다.

9. **BastionHost** 에서 **사용** 을 선택합니다. 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | Bastion 이름 | **myBastionHost** 입력 |
    | AzureBastionSubnet 주소 공간 | **10.1.1.0/24** 입력 |
    | 공용 IP 주소 | **새로 만들기** 를 선택합니다. </br> **이름** 에 대해 **myBastionIP** 를 입력합니다. </br> **확인** 을 선택합니다. |


8. **검토 + 만들기** 탭을 선택하거나 **검토 + 만들기** 단추를 선택합니다.

9. **만들기** 를 선택합니다.

## <a name="create-a-virtual-machine"></a>가상 머신 만들기

이 섹션에서는 프라이빗 엔드포인트를 테스트하는 데 사용할 가상 머신을 만듭니다.

1. 포털의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **가상 머신** 을 선택하거나 검색 상자에 **가상 머신** 을 검색합니다.
   
2. **가상 머신 만들기** 의 **기본** 탭에서 값을 입력하거나 선택합니다.

    | 설정 | 값                                          |
    |-----------------------|----------------------------------|
    | **프로젝트 세부 정보** |  |
    | Subscription | Azure 구독 선택 |
    | 리소스 그룹 | **CreatePrivateEndpointQS-rg** 를 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | **myVM** 을 입력합니다. |
    | 지역 | **서유럽** 를 선택합니다. |
    | 가용성 옵션 | **인프라 중복이 필요하지 않습니다** 를 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter - Gen1** 을 선택합니다. |
    | Azure Spot 인스턴스 | **아니요** 를 선택합니다. |
    | 크기 | VM 크기를 선택하거나 기본 설정을 사용합니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 사용자 이름을 입력합니다. |
    | 암호 | 암호를 입력합니다. |
    | 암호 확인 | 암호를 다시 입력합니다. |

3. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹** 을 차례로 선택합니다.
  
4. [네트워킹] 탭에서 다음을 선택하거나 입력합니다.

    | 설정 | 값 |
    |-|-|
    | **네트워크 인터페이스** |  |
    | 가상 네트워크 | **myVNet** |
    | 서브넷 | **mySubnet** |
    | 공용 IP | **없음** 을 선택합니다. |
    | NIC 네트워크 보안 그룹 추가 | **기본**|
    | 공용 인바운드 포트 | **없음** 을 선택합니다. |
   
5. **검토 + 만들기** 를 선택합니다. 
  
6. 설정을 검토한 다음, **만들기** 를 선택합니다.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-a-private-endpoint"></a>Private Endpoint 만들기

이 섹션에서는 사전 요구 사항 섹션에서 만든 웹앱에 대한 Private Endpoint를 만듭니다.

1. 포털 화면의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **Private Link** 를 차례로 선택하거나 검색 상자에서 **Private Link** 를 입력합니다.

2. **만들기** 를 선택합니다.

3. **Private Link 센터** 의 왼쪽 메뉴에서 **프라이빗 엔드포인트** 를 선택합니다.

4. **프라이빗 엔드포인트** 에서 **+ 추가** 를 선택합니다.

5. **프라이빗 엔드포인트 만들기** 의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | Resource group | **CreatePrivateEndpointQS-rg** 를 선택합니다. 이전 섹션에서 이 리소스 그룹을 만들었습니다.|
    | **인스턴스 세부 정보** |  |
    | 속성  | **myPrivateEndpoint** 를 입력합니다. |
    | 지역 | **서유럽** 를 선택합니다. |

6. 페이지 아래쪽에서 **리소스** 탭 또는 **다음: 리소스** 단추를 선택합니다.
    
7. **리소스** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 연결 방법 | **내 디렉터리의 Azure 리소스에 연결** 을 선택합니다. |
    | Subscription | 구독을 선택합니다. |
    | 리소스 유형 | **Microsoft.Web/sites** 를 선택합니다. |
    | 리소스 | **\<your-web-app-name>** 을 선택합니다. </br> 사전 요구 사항에서 만든 웹앱의 이름을 선택합니다. |
    | 대상 하위 리소스 | **사이트** 를 선택합니다. |

8. 화면 아래쪽에서 **구성** 탭 또는 **다음: 구성** 단추를 선택합니다.

9. **구성** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **네트워킹** |  |
    | 가상 네트워크 | **myVNet** 을 선택합니다. |
    | 서브넷 | **mySubnet** 을 선택합니다. |
    | **프라이빗 DNS 통합** |  |
    | 프라이빗 DNS 영역과 통합 | **예**(기본값)를 그대로 둡니다. |
    | Subscription | 구독을 선택합니다. |
    | 프라이빗 DNS 영역 | **(새 항목) privatelink.azurewebsites.net**(기본값)을 그대로 둡니다.
    

13. **검토 + 만들기** 를 선택합니다.

14. **만들기** 를 선택합니다.

## <a name="test-connectivity-to-private-endpoint"></a>프라이빗 엔드포인트에 연결 테스트

이 섹션에서는 이전 단계에서 만든 가상 머신을 사용하여 프라이빗 엔드포인트에서 웹앱에 연결합니다.

1. 왼쪽 탐색 창에서 **리소스 그룹** 을 선택합니다.

2. **CreatePrivateEndpointQS-rg** 를 선택합니다.

3. **myVM** 을 선택합니다.

4. **myVM** 에 대한 개요 페이지에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

5. 파란색 **Bastion 사용** 단추를 선택합니다.

6. 가상 머신 만들기에서 입력한 사용자 이름과 암호를 입력합니다.

7. 연결한 후 서버에서 Windows PowerShell을 엽니다.

8. `nslookup <your-webapp-name>.azurewebsites.net`를 입력합니다. **\<your-webapp-name>** 을 이전 단계에서 만든 웹앱의 이름으로 바꿉니다.  아래 표시된 것과 유사한 메시지가 표시됩니다.

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.1.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    웹앱 이름에 대해 **10.1.0.5** 의 개인 IP 주소가 반환됩니다.  이 주소는 이전에 만든 가상 네트워크의 서브넷에 있습니다.

11. **myVM** 에 대한 베스천 연결에서 Internet Explorer를 엽니다.

12. 웹앱의 URL(**https://\<your-webapp-name>.azurewebsites.net**)을 입력합니다.

13. 애플리케이션이 배포되지 않은 경우 기본 웹앱 페이지가 표시됩니다.

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="기본 웹앱 페이지" border="true":::

18. **myVM** 에 대한 연결을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 가상 네트워크, 가상 머신 및 웹앱을 삭제합니다.

1. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.

2. **CreatePrivateEndpointQS-rg** 를 선택합니다.

3. **리소스 그룹 삭제** 를 선택합니다.

4. **리소스 그룹 이름 입력** 에서 **CreatePrivateEndpointQS-rg** 를 입력합니다.

5. **삭제** 를 선택합니다.


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 만들었습니다.

* 가상 네트워크 및 베스천 호스트
* 가상 머신
* Azure Web App에 대한 프라이빗 엔드포인트

가상 머신을 사용하여 프라이빗 엔드포인트에서 웹앱에 대한 연결을 안전하게 테스트했습니다.



프라이빗 엔드포인트를 지원하는 서비스에 대한 자세한 내용은 다음을 참조하세요.
> [!div class="nextstepaction"]
> [Private Link 가용성](private-link-overview.md#availability)
