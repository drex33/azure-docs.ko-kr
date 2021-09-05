---
title: Azure Data Factory에 대한 Azure Private Link
description: Azure Data Factory에서 Azure Private Link가 작동하는 방법에 대해 알아봅니다.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/16/2021
ms.openlocfilehash: f4ae3d0653ce99be2017b1ef08ca0645c599ee10
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528898"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Data Factory에 대한 Azure Private Link

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure Private Link를 사용하면 프라이빗 엔드포인트를 통해 Azure의 다양한 PaaS(Platform as a Service) 배포에 연결할 수 있습니다. 프라이빗 엔드포인트는 특정 가상 네트워크 및 서브넷 내의 개인 IP 주소입니다. Private Link 기능을 지원하는 PaaS 배포 목록은 [Private Link 설명서](../private-link/index.yml)를 참조하세요. 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>고객 네트워크와 Azure Data Factory 간의 보안 통신 
Azure 가상 네트워크를 클라우드에서 네트워크의 논리적 표현으로 설정할 수 있습니다. 이렇게 하면 다음과 같은 이점이 있습니다.
* 공용 네트워크의 공격으로부터 Azure 리소스를 보호하는 데 도움이 됩니다.
* 네트워크와 Data Factory가 서로 안전하게 통신하도록 합니다. 

IPsec(인터넷 프로토콜 보안) VPN(사이트 간) 연결 또는 Azure ExpressRoute(개인 피어링) 연결을 설정하여 온-프레미스 네트워크를 가상 네트워크에 연결할 수도 있습니다. 

온-프레미스 컴퓨터 또는 가상 네트워크의 가상 머신에 자체 호스팅 통합 런타임을 설치할 수도 있습니다. 이렇게 하면 다음을 수행할 수 있습니다.
* 클라우드 데이터 저장소와 개인 네트워크의 데이터 저장소 간에 복사 활동을 실행합니다.
* 온-프레미스 네트워크 또는 Azure 가상 네트워크의 컴퓨팅 리소스에 대해 변환 활동을 디스패치할 수 있습니다. 

다음 표에 표시된 것처럼 Azure Data Factory와 고객 가상 네트워크 간에는 여러 가지 통신 채널이 필요합니다.

| 도메인 | 포트 | Description |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Data Factory 작성 및 모니터링에 필요한 컨트롤 플레인입니다. |
| `*.{region}.datafactory.azure.net` | 443 | 자체 호스팅 통합 런타임에서 Data Factory 서비스에 연결하는 데 필요합니다. |
| `*.servicebus.windows.net` | 443 | 대화형 작성을 위해 자체 호스팅 통합 런타임에 필요합니다. |
| `download.microsoft.com` | 443 | 업데이트를 다운로드하기 위해 자체 호스팅 통합 런타임에서 필요합니다. |

Azure Data Factory용 Private Link 지원을 통해 다음을 수행할 수 있습니다.
* 가상 네트워크에 프라이빗 엔드포인트를 만듭니다.
* 특정 데이터 팩터리 인스턴스에 대한 프라이빗 연결을 사용하도록 설정합니다. 

Azure Data Factory 서비스에 대한 통신은 Private Link를 통해 이동하여 안전한 프라이빗 연결을 제공합니다. 

:::image type="content" source="./media/data-factory-private-link/private-link-architecture.png" alt-text="Azure Data Factory 아키텍처에 대한 Private Link 다이어그램.":::

위의 각 통신 채널에 대해 Private Link 서비스를 사용하도록 설정하면 다음과 같은 기능을 제공합니다.
- **지원되는 사항**:
   - 모든 아웃바운드 통신을 차단하더라도 가상 네트워크의 데이터 팩터리를 작성하고 모니터링할 수 있습니다.
   - 자체 호스팅 통합 런타임과 Azure Data Factory 서비스 사이의 명령 통신은 프라이빗 네트워크 환경에서 안전하게 수행될 수 있습니다. 자체 호스팅 통합 런타임과 Azure Data Factory 서비스 사이의 트래픽은 Private Link를 통과합니다. 
- **현재 지원되지 않는 사항**:
   - 연결 테스트, 폴더 목록 및 테이블 목록 찾아보기, 스키마 가져오기, 데이터 미리 보기 등 자체 호스팅 통합 런타임을 사용하는 대화형 작성은 Private Link를 통과합니다.
   - 자동 업데이트를 사용하도록 설정하면 Microsoft 다운로드 센터에서 자동으로 다운로드할 수 있는 자체 호스팅 통합 런타임의 새 버전은 현재 지원되지 않습니다.

   > [!NOTE]
   > 현재 지원되지 않는 기능을 사용하려면 가상 네트워크 또는 회사 방화벽에서 이전에 언급한 도메인 및 포트를 구성해야 합니다. 

   > [!NOTE]
   > 프라이빗 엔드포인트를 통해 Azure Data Factory에 연결하는 것은 데이터 팩터리의 자체 호스팅 통합 런타임에만 적용됩니다. Azure Synapse에 대해서는 지원되지 않습니다.

> [!WARNING]
> Azure Data Factory에서 프라이빗 링크를 사용하도록 설정하는 동시에 퍼블릭 액세스를 차단하는 경우, 연결된 서비스를 만들 때 자격 증명이 Azure Key Vault에 저장되어 있는지 확인합니다. 그러지 않으면 자격 증명이 작동하지 않습니다.

## <a name="dns-changes-for-private-endpoints"></a>프라이빗 엔드포인트에 대한 DNS 변경 내용
프라이빗 엔드포인트를 생성하면 Data Factory에 대한 DNS CNAME 리소스 레코드가 접두사 'private link'를 포함하는 하위 도메인의 별칭으로 업데이트됩니다. 또한 기본적으로 프라이빗 엔드포인트에 대한 DNS A 리소스 레코드를 사용하여 'privatelink' 하위 도메인에 해당하는 [프라이빗 DNS 영역](../dns/private-dns-overview.md)을 만듭니다.

프라이빗 엔드포인트를 사용하여 VNet 외부에서 데이터 팩터리 엔드포인트 URL을 확인하면 데이터 팩터리 서비스의 퍼블릭 엔드포인트로 확인됩니다. 프라이빗 엔드포인트를 호스트하는 VNet에서 확인되면 스토리지 엔드포인트 URL은 프라이빗 엔드포인트의 IP 주소로 확인됩니다.

위의 예에서 프라이빗 엔드포인트를 호스팅하는 VNet 외부에서 해결되면 Data Factory 'DataFactory A'에 대한 DNS 리소스 레코드는 다음과 같습니다.

| Name | 유형 | 값 |
| ---------- | -------- | --------------- |
| DataFactoryA.{지역}.datafactory.azure.net | CNAME   | DataFactoryA.{지역}.privatelink.datafactory.azure.net |
| DataFactoryA.{지역}.privatelink.datafactory.azure.net | CNAME   | < 데이터 팩터리 서비스 퍼블릭 엔드포인트 > |
| < 데이터 팩터리 서비스 퍼블릭 엔드포인트 >  | A | < 데이터 팩터리 서비스 퍼블릭 IP 주소 > |

프라이빗 엔드포인트를 호스팅하는 VNet에서 확인되면 DataFactoryA에 대한 DNS 리소스 레코드는 다음과 같습니다.

| Name | 유형 | 값 |
| ---------- | -------- | --------------- |
| DataFactoryA.{지역}.datafactory.azure.net | CNAME   | DataFactoryA.{지역}.privatelink.datafactory.azure.net |
| DataFactoryA.{지역}.privatelink.datafactory.azure.net   | A | < 프라이빗 엔드포인트 IP 주소 > |

네트워크에서 사용자 지정 DNS 서버를 사용하는 경우 클라이언트는 Data Factory 엔드포인트의 FQDN을 프라이빗 엔드포인트 IP 주소로 확인할 수 있어야 합니다. 프라이빗 링크 하위 도메인을 VNet의 프라이빗 DNS 영역에 위임하도록 DNS 서버를 구성하거나 프라이빗 엔드포인트 IP 주소를 사용하여 'DataFactoryA.{지역}.privatelink.datafactory.azure.net'에 대한 A 레코드를 구성해야 합니다.

프라이빗 엔드포인트 지원 목적으로 자체 DNS 서버를 구성하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
- [Azure 가상 네트워크의 리소스 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [프라이빗 엔드포인트에 대한 DNS 구성](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-a-private-endpoint-link-for-azure-data-factory"></a>Azure Data Factory에 대한 프라이빗 엔드포인트 링크 설정

이 섹션에서는 Azure Data Factory에 대한 프라이빗 엔드포인트 링크를 설정합니다.

다음과 같이 데이터 팩터리 만들기 단계에서 SHIR(자체 호스팅 통합 런타임)을 Azure Data Factory에 연결할 때 퍼블릭 엔드포인트를 사용할지 또는 프라이빗 엔드포인트를 사용할지 선택할 수 있습니다. 

:::image type="content" source="./media/data-factory-private-link/disable-public-access-shir.png" alt-text="자체 호스팅 통합 런타임에 대한 공용 액세스를 차단하는 스크린샷.":::

만든 후에 언제든지 네트워킹 블레이드의 데이터 팩터리 포털 페이지에서 선택을 변경할 수 있습니다.  프라이빗 엔드포인트를 사용하도록 설정한 후에는 데이터 팩터리에 프라이빗 엔드포인트도 추가해야 합니다.

프라이빗 엔드포인트에는 링크의 가상 네트워크와 서브넷이 필요할 뿐 아니라, 프라이빗 엔드포인트 링크를 통해 연결하는 SHIR(자체 호스팅 통합 런타임)을 실행하는 데 사용되는 서브넷 내의 가상 머신이 필요합니다.

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기
프라이빗 엔드포인트 링크에서 사용할 기존 가상 네트워크가 없는 경우에는 가상 네트워크를 만들고 서브넷을 할당해야 합니다.  

1. https://portal.azure.com에서 Azure Portal에 로그인합니다.
2. 화면의 왼쪽 위에서 **리소스 만들기 > 네트워킹 > 가상 네트워크** 를 차례로 선택하거나, 검색 상자에서 **가상 네트워크** 를 검색합니다.

3. **가상 네트워크 만들기** 의 **기본** 탭에서 다음 정보를 입력하거나 선택합니다.

    | **설정**          | **값**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **프로젝트 세부 정보**  |                                                                 |
    | 구독     | Azure 구독 선택                                  |
    | 리소스 그룹   | 가상 네트워크의 리소스 그룹을 선택합니다. |
    | **인스턴스 세부 정보** |                                                                 |
    | 속성             | 가상 네트워크의 이름을 입력합니다. |
    | 지역           | 중요: 프라이빗 엔드포인트에서 사용할 지역과 동일한 지역을 선택합니다. |

4. **IP 주소** 탭을 선택하거나 페이지 하단의 **다음: IP 주소** 단추를 선택합니다.

5. **IP 주소** 탭에서 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | IPv4 주소 공간 | **10.1.0.0/16** 입력 |

6. **서브넷 이름** 아래에서 **기본값** 이라는 단어를 선택합니다.

7. **서브넷 편집** 에서 다음 정보를 입력합니다.

    | 설정            | 값                      |
    |--------------------|----------------------------|
    | 서브넷 이름 | 서브넷의 이름을 입력합니다. |
    | 서브넷 주소 범위 | **10.1.0.0/24** 입력 |

8. **저장** 을 선택합니다.

9. **검토 + 만들기** 탭을 선택하거나 **검토 + 만들기** 단추를 선택합니다.

10. **만들기** 를 선택합니다.

### <a name="create-a-virtual-machine-for-the-self-hosted-integration-runtime-shir"></a>SHIR(자체 호스팅 통합 런타임)의 가상 머신 만들기
위에서 만든 새 서브넷의 자체 호스팅 통합 런타임을 실행할 기존 가상 머신도 할당하거나 새로 만들어야 합니다.

1. 포털의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **가상 머신** 을 선택하거나 검색 상자에 **가상 머신** 을 검색합니다.
   
2. **가상 머신 만들기** 의 **기본** 탭에서 값을 입력하거나 선택합니다.

    | 설정 | 값                                          |
    |-----------------------|----------------------------------|
    | **프로젝트 세부 정보** |  |
    | 구독 | Azure 구독 선택 |
    | 리소스 그룹 | 리소스 그룹 선택 |
    | **인스턴스 세부 정보** |  |
    | 가상 머신 이름 | 가상 머신의 이름을 입력합니다. |
    | 지역 | 위에서 가상 네트워크에 사용된 지역을 선택합니다. |
    | 가용성 옵션 | **인프라 중복이 필요하지 않습니다** 를 선택합니다. |
    | 이미지 | **Windows Server 2019 Datacenter - Gen1**(또는 자체 호스팅 통합 런타임을 지원하는 다른 Windows 이미지)을 선택합니다. |
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
    | 가상 네트워크 | 위에서 만든 가상 네트워크를 선택합니다. |
    | 서브넷 | 위에서 만든 서브넷을 선택합니다. |
    | 공용 IP | **없음** 을 선택합니다. |
    | NIC 네트워크 보안 그룹 추가 | **기본**|
    | 공용 인바운드 포트 | **없음** 을 선택합니다. |
   
5. **검토 + 만들기** 를 선택합니다. 
  
6. 설정을 검토한 다음, **만들기** 를 선택합니다.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

### <a name="create-the-private-endpoint"></a>프라이빗 엔드포인트 만들기 
마지막으로, 데이터 팩터리에서 프라이빗 엔드포인트를 만들어야 합니다.

1. 데이터 팩터리에 대한 Azure Portal 페이지에서 **네트워킹** 블레이드와 **프라이빗 엔드포인트 연결** 탭을 선택한 다음, **+ 프라이빗 엔드포인트** 를 선택합니다. 

:::image type="content" source="./media/data-factory-private-link/create-private-endpoint.png" alt-text="프라이빗 엔드포인트를 만들기 위한 프라이빗 엔드포인트 연결 창 스크린샷":::

2. **프라이빗 엔드포인트 만들기** 의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | 구독 | 구독 선택 |
    | Resource group | 리소스 그룹 선택 |
    | **인스턴스 세부 정보** |  |
    | 속성  | 엔드포인트의 이름을 입력합니다. |
    | 지역 | 위에서 만든 가상 네트워크의 지역을 선택합니다. |

3. 페이지 아래쪽에서 **리소스** 탭 또는 **다음: 리소스** 단추를 선택합니다.
    
4. **리소스** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 연결 방법 | **내 디렉터리의 Azure 리소스에 연결** 을 선택합니다. |
    | Subscription | 구독 선택 |
    | 리소스 종류 | **Microsoft.Datafactory/factories** 를 선택합니다. |
    | 리소스 | 데이터 팩터리 선택 |
    | 대상 하위 리소스 | 자체 호스팅 통합 런타임과 Azure Data Factory 서비스 간 명령 통신을 위해 프라이빗 엔드포인트를 사용하려는 경우 **대상 하위 리소스** 로 **datafactory** 를 선택합니다. 가상 네트워크에서 데이터 팩터리를 작성하고 모니터링하기 위해 프라이빗 엔드포인트를 사용하려는 경우 **대상 하위 리소스** 로 **portal** 을 선택합니다.|

5. 화면 아래쪽에서 **구성** 탭 또는 **다음: 구성** 단추를 선택합니다.

6. **구성** 에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **네트워킹** |  |
    | 가상 네트워크 | 위에서 만든 가상 네트워크를 선택합니다. |
    | 서브넷 | 위에서 만든 서브넷을 선택합니다. |
    | **프라이빗 DNS 통합** |  |
    | 프라이빗 DNS 영역과 통합 | **예**(기본값)를 그대로 둡니다. |
    | Subscription | 구독을 선택합니다. |
    | 프라이빗 DNS 영역 | **(새 항목) privatelink.azurewebsites.net**(기본값)을 그대로 둡니다.
    

7. **검토 + 만들기** 를 선택합니다.

8. **만들기** 를 선택합니다.

> [!NOTE]
> 공용 네트워크 액세스 비활성화는 Azure Integration Runtime 및 SSIS(SQL Server Integration Services) Integration Runtime이 아닌 자체 호스팅 통합 런타임에만 적용할 수 있습니다.

> [!NOTE]
> 포털에 대한 프라이빗 엔드포인트를 만든 후에도 공용 네트워크를 통해 Azure Data Factory 포털에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory UI를 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-portal.md)
- [Azure Data Factory 소개](introduction.md)
- [Azure Data Factory에서 시각적 작성](author-visually.md)
