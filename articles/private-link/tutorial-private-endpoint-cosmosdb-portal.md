---
title: '자습서: Azure Private 엔드포인트를 사용하여 Azure Cosmos 계정에 연결'
titleSuffix: Azure Private Link
description: Azure Private 엔드포인트를 사용하여 Azure Cosmos 계정에 개인적으로 연결하는 이 자습서를 시작하세요.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: tutorial
ms.date: 9/25/2020
ms.openlocfilehash: c7c9caf5bbfd842c635b0cb887540457f15c081f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "114297040"
---
# <a name="tutorial-connect-to-an-azure-cosmos-account-using-an-azure-private-endpoint"></a>자습서: Azure Private 엔드포인트를 사용하여 Azure Cosmos 계정에 연결

Azure Private 엔드포인트는 Azure에서 Private Link를 만드는 데 사용되는 기본 구성 요소입니다. 이를 사용하면 VM(가상 머신)과 같은 Azure 리소스에서 Private Link 리소스와 비공개로 통신할 수 있습니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 가상 네트워크 및 베스천 호스트를 만듭니다.
> * 가상 머신을 만듭니다.
> * 프라이빗 엔드포인트를 사용하여 Cosmos DB 계정을 만듭니다.
> * Cosmos DB 계정 프라이빗 엔드포인트에 대한 연결을 테스트합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-virtual-network-and-bastion-host"></a>가상 네트워크 및 베스천 호스트 만들기

이 섹션에서는 가상 네트워크, 서브넷 및 베스천 호스트를 만듭니다. 

베스천 호스트는 가상 머신에 안전하게 연결하여 프라이빗 엔드포인트를 테스트하는 데 사용됩니다.

1. 화면의 왼쪽 위에서 **리소스 만들기 > 네트워킹 > 가상 네트워크** 를 차례로 선택하거나, 검색 상자에서 **가상 네트워크** 를 검색합니다.

2. **가상 네트워크 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | **설정**          | **값**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **프로젝트 세부 정보**  |                                                                 |
    | Subscription     | Azure 구독 선택                                  |
    | 리소스 그룹   | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |                                                                 |
    | 속성             | **myVNet** 입력                                    |
    | 지역           | **미국 동부** 를 선택합니다. |

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
    | 리소스 그룹 | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | **myVM** 을 입력합니다. |
    | 지역 | **미국 동부** 를 선택합니다. |
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

## <a name="create-a-cosmos-db-account-with-a-private-endpoint"></a>프라이빗 엔드포인트를 사용하여 Cosmos DB 계정 만들기

이 섹션에서는 Cosmos DB 계정을 만들고 프라이빗 엔드포인트를 구성합니다.

1. 왼쪽 메뉴에서 **리소스 만들기** > **데이터베이스** > **Cosmos DB 계정** 을 차례로 선택하거나, 검색 상자에서 **Cosmos DB 계정** 을 검색합니다.

2. **Cosmos DB 계정 만들기** 의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값                                          |
    |-----------------------|----------------------------------|
    | **프로젝트 세부 정보** |  |
    | Subscription | Azure 구독을 선택합니다. |
    | 리소스 그룹 | **myResourceGroup** 을 선택합니다. |
    | **인스턴스 세부 정보** |  |
    | 계정 이름 | **mycosmosdb** 를 입력합니다. 이름을 사용할 수 없는 경우 고유한 이름을 입력합니다. |
    | API | **코어(SQL)** 를 선택합니다. |
    | 위치 | **미국 동부** 를 선택합니다. |
    | 용량 모드 | 기본 **프로비저닝된 처리량** 을 그대로 둡니다. |
    | 체험 계층 할인 적용 | **적용 안 함**(기본값)을 그대로 둡니다. |
    | 지역 중복 | **사용 안 함**(기본값)을 그대로 둡니다. |
    | 다중 지역 쓰기 | **사용 안 함**(기본값)을 그대로 둡니다. |
   
3. **네트워킹** 탭을 선택하거나 **다음: 네트워킹** 단추를 선택합니다.

4. **네트워킹** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **네트워크 연결** | |
    | 연결 방법 | **프라이빗 엔드포인트** 를 선택합니다. |
    | **방화벽 구성** | |
    | Azure Portal에서 액세스 허용 | **허용**(기본값)을 그대로 둡니다 |
    | 내 IP에서 액세스 허용 | **거부**(기본값)를 그대로 둡니다 |

5. **프라이빗 엔드포인트** 에서 **+추가** 를 선택합니다.

6. **프라이빗 엔드포인트 만들기** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값                                          |
    |-----------------------|----------------------------------|
    | Subscription | Azure 구독 선택 |
    | 리소스 그룹 | **myResourceGroup** 을 선택합니다. |
    | 위치 | **미국 동부** 를 선택합니다. |
    | Name | **myPrivateEndpoint** 를 입력합니다. |
    | 대상 하위 리소스 | **코어(SQL)** (기본값)를 그대로 둡니다 |
    | **네트워킹** |  |
    | 가상 네트워크 | **myVNet** 을 선택합니다. |
    | 서브넷 | **mySubnet** 을 선택합니다. |
    | **프라이빗 DNS 통합** |
    | 프라이빗 DNS 영역과 통합 | 기본값인 **예** 를 유지합니다. |
    | 프라이빗 DNS 영역 | privatelink.documents.azure.com(기본값, 새 항목)을 그대로 둡니다 |

7. **확인** 을 선택합니다.

8. **검토 + 만들기** 를 선택합니다.

9. **만들기** 를 선택합니다.

### <a name="add-a-database-and-a-container"></a>데이터베이스 및 컨테이너 추가

1. **리소스로 이동** 을 선택하거나 Azure Portal의 왼쪽 메뉴에서 **모든 리소스** > **mycosmosdb** 를 차례로 선택합니다.

2. 왼쪽 메뉴에서 **Data Explorer** 를 선택합니다.

3. **Data Explorer** 창에서 **새 컨테이너** 를 선택합니다.

4. **컨테이너 추가** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 데이터베이스 ID | **새로 만들기**(기본값)를 그대로 둡니다. </br> 텍스트 상자에서 **mydatabaseid** 를 입력합니다. |
    | 처리량(400~100,000RU/s) | **수동**(기본값)을 그대로 둡니다 </br> 텍스트 상자에서 **400** 을 입력합니다. |
    | 컨테이너 ID | **mycontainerid** 를 입력합니다. |
    | 파티션 키 | **/mykey** 를 입력합니다. |

5. **확인** 을 선택합니다.

6. CosmosDB 계정의 **설정** 섹션에서 **키** 를 선택합니다.

7. **기본 연결 문자열** 에서 복사를 선택합니다.

## <a name="test-connectivity-to-private-endpoint"></a>프라이빗 엔드포인트에 연결 테스트

이 섹션에서는 이전 단계에서 만든 가상 머신을 사용하여 프라이빗 엔드포인트에서 Cosmos DB 계정에 연결합니다.

1. 왼쪽 탐색 창에서 **리소스 그룹** 을 선택합니다.

1. **myResourceGroup** 을 선택합니다.

1. **myVM** 을 선택합니다.

1. **myVM** 에 대한 개요 페이지에서 **연결** 을 선택한 다음, **Bastion** 을 선택합니다.

1. 파란색 **Bastion 사용** 단추를 선택합니다.

1. 가상 머신 만들기에서 입력한 사용자 이름과 암호를 입력합니다.

1. 연결한 후 서버에서 Windows PowerShell을 엽니다.

1. `nslookup <cosmosdb-account-name>.documents.azure.com`을 입력하고 이름 확인의 유효성을 검사합니다. **\<cosmosdb-account-name>** 를 이전 단계에서 만든 Cosmos DB 계정의 이름으로 바꿉니다. 

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mycosmosdb8675.privatelink.documents.azure.com
    Address:  10.1.0.5
    Aliases:  mycosmosdb8675.documents.azure.com
    ```
    Cosmos DB 계정 이름에 대해 **10.1.0.5** 의 개인 IP 주소가 반환됩니다.  이 주소는 이전에 만든 가상 네트워크의 서브넷에 있습니다.
    
1. 포털에서 Azure Cosmos DB 기본 연결 문자열을 가져옵니다. 유효한 연결 문자열은 다음 형식입니다.
   
   SQL API 계정의 경우: `https://<accountName>.documents.azure.com:443/;AccountKey=<accountKey>;` Azure Cosmos DB API for MongoDB의 경우: `mongodb://<accountName>:<accountKey>@cdbmongo36.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false`

1. 가상 머신에 [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 설치합니다.

1. **Microsoft Azure Storage Explorer** 가 설치된 후 **마침** 을 선택합니다.  확인란을 선택된 상태로 두고 애플리케이션을 엽니다.

1. **Azure Storage에 연결** 화면에서 **취소** 를 선택합니다.

1. Storage Explorer에서 **Cosmos DB 계정** 을 마우스 오른쪽 단추로 선택하고 **Cosmos DB에 연결** 을 선택합니다.

1. **API 선택** 아래에서 **SQL**(기본값)을 그대로 둡니다.

1. 이전 단계에서 복사한 Cosmos DB 계정의 연결 문자열을 **연결 문자열** 아래의 상자에 붙여넣습니다.

1. **다음** 을 선택합니다.

1. **연결 요약** 에서 설정이 올바른지 확인합니다.  

1. **연결** 을 선택합니다.

1. **myVM** 에 대한 연결을 닫습니다.


## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용하지 않으려면 다음 단계에 따라 가상 네트워크, 가상 머신 및 Cosmos DB 계정을 삭제합니다.

1. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.

2. **myResourceGroup** 을 선택합니다.

3. **리소스 그룹 삭제** 를 선택합니다.

4. **리소스 그룹 이름 입력** 에서 **myResourceGroup** 을 입력합니다.

5. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 만들었습니다.

* 가상 네트워크 및 베스천 호스트
* Virtual Machine.
* Cosmos DB 계정.

Private Link 서비스를 만드는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [Private Link 서비스 만들기](create-private-link-service-portal.md)
