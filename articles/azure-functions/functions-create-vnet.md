---
title: 프라이빗 엔드포인트를 사용하여 가상 네트워크와 Azure Functions 통합
description: 이 자습서에서는 Azure Virtual Network에 함수를 연결하고 프라이빗 엔드포인트를 사용하여 잠그는 방법을 보여줍니다.
ms.topic: article
ms.date: 2/22/2021
ms.openlocfilehash: 8eaf46cdab44fcf4ebdb69564cd05f07f8f88f1b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850036"
---
# <a name="tutorial-integrate-azure-functions-with-an-azure-virtual-network-by-using-private-endpoints"></a>자습서: 프라이빗 엔드포인트를 사용하여 Azure 가상 네트워크와 Azure Functions 통합

이 자습서에서는 Azure Functions를 사용하여 프라이빗 엔드포인트를 통해 Azure Virtual Network의 리소스에 연결하는 방법을 보여줍니다. 가상 네트워크 뒤에 잠긴 스토리지 계정을 사용하여 함수를 만듭니다. 가상 네트워크에는 Service Bus 큐 트리거가 사용됩니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 프리미엄 계획에서 함수 앱을 만듭니다.
> * Service bus, 스토리지 계정 및 가상 네트워크와 같은 Azure 리소스를 만듭니다.
> * 프라이빗 엔드포인트 뒤에서 스토리지 계정을 잠급니다.
> * 프라이빗 엔드포인트 뒤에서 Service Bus를 잠급니다.
> * Service bus 및 HTTP 트리거를 모두 사용하는 함수 앱을 배포합니다.
> * 프라이빗 엔드포인트 뒤에서 함수 앱을 잠급니다.
> * 함수 앱이 가상 네트워크 내에서 안전하게 보호되는지 테스트합니다.
> * 리소스를 정리합니다.

## <a name="create-a-function-app-in-a-premium-plan"></a>프리미엄 플랜에서 함수 앱 만들기

이 자습서에서는 C#을 사용하기 때문에 프리미엄 계획에서 .NET 함수 앱을 만듭니다. 다른 언어도 Windows에서 지원됩니다. 프리미엄 계획은 가상 네트워크 통합을 지원하는 동시에 서버리스 크기 조정을 제공합니다.

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

1. **새로 만들기** 페이지에서 **컴퓨팅** > **함수 앱** 을 선택합니다.

1. **기본 사항** 페이지에서 다음 표를 사용하여 함수 앱 설정을 구성합니다.

    | 설정      | 제안 값  | Description |
    | ------------ | ---------------- | ----------- |
    | **구독** | 사용자의 구독 | 이 새 함수 앱이 만들어질 구독입니다. |
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)** |  myResourceGroup | 함수 앱을 만들 새 리소스 그룹의 이름입니다. |
    | **함수 앱 이름** | 전역적으로 고유한 이름 | 새 함수 앱을 식별하는 이름입니다. 유효한 문자는 `a-z`(대/소문자 구분 안 함), `0-9`및 `-`입니다.  |
    |**게시**| 코드 | 코드 파일 또는 Docker 컨테이너를 게시하도록 선택합니다. |
    | **런타임 스택** | .NET | 이 자습서에서는 .NET을 사용합니다. |
    |**지역**| 기본 지역 | 사용자 근처 또는 함수가 액세스할 기타 서비스에 가까운 [지역](https://azure.microsoft.com/regions/)을 선택합니다. |

1. 완료되면 **다음: 호스팅** 을 선택합니다. **호스팅** 페이지에서 다음 설정을 입력합니다.

    | 설정      | 제안 값  | Description |
    | ------------ | ---------------- | ----------- |
    | **[Storage 계정](../storage/common/storage-account-create.md)** |  전역적으로 고유한 이름 |  함수 앱에서 사용하는 스토리지 계정을 만듭니다. 스토리지 계정 이름은 3~24자여야 합니다. 이름은 숫자와 소문자만 포함할 수 있습니다. 기존 계정을 사용할 수도 있습니다. 여기서는 [스토리지 계정 요구 사항](./storage-considerations.md#storage-account-requirements)을 충족해야 합니다. |
    |**운영 체제**| Windows | 이 자습서에서는 Windows를 사용합니다. |
    | **[계획](./functions-scale.md)** | Premium | 함수 앱에 리소스가 할당되는 방법을 정의하는 호스팅 계획입니다. 기본적으로 **프리미엄** 을 선택하면 새 App Service 요금제가 만들어집니다. 기본 **SKU 및 크기** 는 **EP1** 입니다. 여기서 *EP* 는 _탄력적 프리미엄_ 을 의미합니다. 자세한 내용은 [프리미엄 SKU](./functions-premium-plan.md#available-instance-skus) 목록을 참조하세요.<br/><br/>프리미엄 계획에서 JavaScript 함수를 실행 중인 경우 vCPU 수가 더 작은 인스턴스를 선택합니다. 자세한 내용은 [단일 코어 프리미엄 계획 선택](./functions-reference-node.md#considerations-for-javascript-functions)을 참조하세요.  |

1. 완료되면 **다음: 모니터링** 을 선택합니다. **모니터링** 페이지에서 다음 설정을 입력합니다.

    | 설정      | 제안 값  | Description |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](./functions-monitoring.md)** | 기본값 | 가장 가까운 지원 영역에 동일한 앱 이름의 Application Insight 리소스를 만듭니다. **새 리소스 이름을** 변경하거나 [Azure Geography](https://azure.microsoft.com/global-infrastructure/geographies/)의 다른 **위치** 에 데이터를 저장해야 하는 경우 이 설정을 확장합니다. |

1. **검토 + 만들기** 를 선택하여 앱 구성 선택을 검토합니다.

1. **검토 + 만들기** 페이지에서 설정을 검토합니다. 그런 다음, **만들기** 를 선택하여 함수 앱을 프로비전하고 배포합니다.

1. 포털의 오른쪽 상단에 있는 **알림** 아이콘을 선택하고 **배포 성공** 메시지를 확인합니다.

1. **리소스로 이동** 을 선택하여 함수 앱을 봅니다. **대시보드에 고정** 을 선택할 수도 있습니다. 고정하면 대시보드에서 이 함수 앱 리소스로 쉽게 돌아올 수 있습니다.

축하합니다! 프리미엄 함수 앱을 만들었습니다.

## <a name="create-azure-resources"></a>Azure 리소스 만들기

그런 다음, 스토리지 계정, Service Bus 및 가상 네트워크를 만듭니다. 
### <a name="create-a-storage-account"></a>스토리지 계정 만들기

가상 네트워크에는 함수 앱을 사용하여 만든 것과는 다른 스토리지 계정이 필요합니다.

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

1. **새로 만들기** 페이지에서 *스토리지 계정* 을 검색합니다. 그런 다음 **생성** 를 선택합니다.

1. **기본 사항** 탭에서 다음 표를 사용하여 스토리지 계정 설정을 구성합니다. 다른 모든 설정에는 기본값을 사용할 수 있습니다.

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용하여 만든 리소스 그룹입니다. |
    | **이름** | mysecurestorage| 프라이빗 엔드포인트가 적용될 스토리지 계정의 이름입니다. |
    | **[지역](https://azure.microsoft.com/regions/)** | myFunctionRegion | 함수 앱을 만든 지역입니다. |

1. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다.

### <a name="create-a-service-bus"></a>Service Bus 만들기

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

1. **새로 만들기** 페이지에서 *Service Bus* 를 검색합니다. 그런 다음 **생성** 를 선택합니다.

1. **기본 정보** 탭에서 다음 표를 사용하여 Service Bus 설정을 구성합니다. 다른 모든 설정에는 기본값을 사용할 수 있습니다.

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. |
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용하여 만든 리소스 그룹입니다. |
    | **네임스페이스 이름** | myServiceBus| 프라이빗 엔드포인트가 적용될 Service Bus의 이름입니다. |
    | **[위치](https://azure.microsoft.com/regions/)** | myFunctionRegion | 함수 앱을 만든 지역입니다. |
    | **가격 책정 계층** | Premium | Azure Service Bus에서 프라이빗 엔드포인트를 사용하려면 이 계층을 선택합니다. |

1. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다.

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

이 자습서의 Azure 리소스는 가상 네트워크와 통합되거나 가상 네트워크 내에 배치됩니다. 가상 네트워크 내에 네트워크 트래픽을 포함하기 위해 프라이빗 엔드포인트를 사용합니다.

이 자습서에서는 두 개의 서브넷을 만듭니다.
- **기본값**: 프라이빗 엔드포인트의 서브넷입니다. 개인 IP 주소는 이 서브넷에서 제공됩니다.
- **함수**: Azure Functions 가상 네트워크 통합의 서브넷입니다. 이 서브넷은 함수 앱에 위임됩니다.

함수 앱이 통합되는 가상 네트워크를 만듭니다.

1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

1. **새로 만들기** 페이지에서 *가상 네트워크* 를 검색합니다. 그런 다음 **생성** 를 선택합니다.

1. **기본 사항** 탭에서 다음 표를 사용하여 가상 네트워크 설정을 구성합니다.

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용하여 만든 리소스 그룹입니다. |
    | **이름** | myVirtualNet| 함수 앱이 연결되는 가상 네트워크의 이름입니다. |
    | **[지역](https://azure.microsoft.com/regions/)** | myFunctionRegion | 함수 앱을 만든 지역입니다. |

1. **IP 주소** 탭에서 **서브넷 추가** 를 선택합니다. 다음 표를 사용하여 서브넷 설정을 구성합니다.

    :::image type="content" source="./media/functions-create-vnet/1-create-vnet-ip-address.png" alt-text="가상 네트워크 구성 뷰의 스크린샷입니다.":::

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **서브넷 이름** | functions | 함수 앱이 연결될 서브넷의 이름입니다. | 
    | **서브넷 주소 범위** | 10.0.1.0/24 | 서브넷 주소 범위입니다. 위의 이미지에서 IPv4 주소 공간은 10.0.0.0/16입니다. 값이 10.1.0.0/16인 경우 권장 서브넷 주소 범위는 10.1.1.0/24입니다. |

1. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다.

## <a name="lock-down-your-storage-account"></a>스토리지 계정 잠금

Azure 프라이빗 엔드포인트는 개인 IP 주소를 사용하여 특정 Azure 리소스에 연결하는 데 사용됩니다. 이렇게 연결하면 선택한 가상 네트워크 내에서 네트워크 트래픽이 유지되고 특정 리소스에 대해서만 액세스할 수 있습니다. 

스토리지 계정을 사용하여 Azure Files Storage, Azure Blob Storage 및 Azure Table Storage에 대한 프라이빗 엔드포인트를 만듭니다.

1. 새 스토리지 계정의 왼쪽에 있는 메뉴에서 **네트워킹** 을 선택합니다.

1. **프라이빗 엔드포인트 연결** 탭에서 **프라이빗 엔드포인트** 를 선택합니다.

    :::image type="content" source="./media/functions-create-vnet/2-navigate-private-endpoint-store.png" alt-text="스토리지 계정에 대한 프라이빗 엔드포인트를 만드는 방법을 보여주는 스크린샷입니다.":::

1. **기본 사항** 탭에서 다음 표에 나와 있는 프라이빗 엔드포인트 설정을 사용합니다.

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용하여 만든 리소스 그룹을 선택합니다. |
    | **이름** | file-endpoint | 스토리지 계정의 파일에 대한 프라이빗 엔드포인트의 이름입니다. |
    | **[지역](https://azure.microsoft.com/regions/)** | myFunctionRegion | 스토리지 계정을 만든 지역을 선택합니다. |

1. **리소스** 탭에서 다음 표에 나와 있는 프라이빗 엔드포인트 설정을 사용합니다.

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **리소스 종류**  | Microsoft.Storage/storageAccounts | 스토리지 계정에 대한 리소스 종류입니다. |
    | **리소스** | mysecurestorage | 만든 스토리지 계정입니다. |
    | **대상 하위 리소스** | 파일 | 스토리지 계정의 파일에 사용될 프라이빗 엔드포인트입니다. |

1. **구성** 탭의 **서브넷** 설정에서 **기본값** 을 선택합니다.

1. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다. 이제 가상 네트워크의 리소스를 스토리지 파일과 통신할 수 있습니다.

1. Blob에 대한 다른 프라이빗 엔드포인트를 만듭니다. **리소스** 탭에서 다음 표에 나와 있는 설정을 사용합니다. 다른 모든 설정의 경우에는 파일에 대한 프라이빗 엔드포인트를 만드는 데 사용한 것과 동일한 값을 사용합니다.

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **리소스 종류**  | Microsoft.Storage/storageAccounts | 스토리지 계정에 대한 리소스 종류입니다. |
    | **이름** | blob-endpoint | 스토리지 계정의 blob에 대한 프라이빗 엔드포인트의 이름입니다. |
    | **리소스** | mysecurestorage | 만든 스토리지 계정입니다. |
    | **대상 하위 리소스** | blob | 스토리지 계정의 Blob에 사용될 프라이빗 엔드포인트입니다. |
1. 테이블에 대한 다른 프라이빗 엔드포인트를 만듭니다. **리소스** 탭에서 다음 표에 나와 있는 설정을 사용합니다. 다른 모든 설정의 경우에는 파일에 대한 프라이빗 엔드포인트를 만드는 데 사용한 것과 동일한 값을 사용합니다.

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **리소스 종류**  | Microsoft.Storage/storageAccounts | 스토리지 계정에 대한 리소스 종류입니다. |
    | **이름** | table-endpoint | 스토리지 계정의 blob에 대한 프라이빗 엔드포인트의 이름입니다. |
    | **리소스** | mysecurestorage | 만든 스토리지 계정입니다. |
    | **대상 하위 리소스** | 테이블 | 스토리지 계정의 테이블에 사용될 프라이빗 엔드포인트입니다. |
1. 프라이빗 엔드포인트를 만든 후 스토리지 계정의 **방화벽 및 가상 네트워크** 섹션으로 돌아갑니다.  
1. **선택한 네트워크** 가 선택되었는지 확인합니다.  기존 가상 네트워크를 추가할 필요는 없습니다.

이제 가상 네트워크의 리소스가 프라이빗 엔드포인트를 사용하여 스토리지 계정과 통신할 수 있습니다.
## <a name="lock-down-your-service-bus"></a>Service Bus 잠금

다음과 같이 Service Bus를 잠글 프라이빗 엔드포인트를 만듭니다.

1. 새 Service Bus의 왼쪽에 있는 메뉴에서 **네트워킹** 을 선택합니다.

1. **프라이빗 엔드포인트 연결** 탭에서 **프라이빗 엔드포인트** 를 선택합니다.

    :::image type="content" source="./media/functions-create-vnet/3-navigate-private-endpoint-service-bus.png" alt-text="Service Bus에 대한 프라이빗 엔드포인트로 이동하는 방법을 보여주는 스크린샷입니다.":::

1. **기본 사항** 탭에서 다음 표에 나와 있는 프라이빗 엔드포인트 설정을 사용합니다.

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)**  | myResourceGroup | 함수 앱을 사용하여 만든 리소스 그룹입니다. |
    | **이름** | sb-endpoint | 스토리지 계정의 파일에 대한 프라이빗 엔드포인트의 이름입니다. |
    | **[지역](https://azure.microsoft.com/regions/)** | myFunctionRegion | 스토리지 계정을 만든 지역입니다. |

1. **리소스** 탭에서 다음 표에 나와 있는 프라이빗 엔드포인트 설정을 사용합니다.

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. | 
    | **리소스 종류**  | Microsoft.ServiceBus/namespaces | Service Bus에 대한 리소스 종류입니다. |
    | **리소스** | myServiceBus | 자습서의 앞부분에서 만든 Service Bus입니다. |
    | **대상 하위 리소스** | namespace | Service Bus의 네임스페이스에 사용될 프라이빗 엔드포인트입니다. |

1. **구성** 탭의 **서브넷** 설정에서 **기본값** 을 선택합니다.

1. **검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다. 
1. 프라이빗 엔드포인트를 만든 후 Service Bus 네임스페이스의 **방화벽 및 가상 네트워크** 섹션으로 돌아갑니다.
1. **선택한 네트워크** 가 선택되었는지 확인합니다.
1. **+ 기존 가상 네트워크 추가** 를 선택하여 최근에 만든 가상 네트워크를 추가합니다.
1. **네트워크 추가** 탭에서 다음 테이블의 네트워크 설정을 사용합니다.

    | 설정 | 제안 값 | Description|
    |---------|-----------------|------------|
    | **구독** | 사용자의 구독 | 리소스가 만들어지는 구독입니다. |
    | **가상 네트워크** | myVirtualNet | 함수 앱이 연결되는 가상 네트워크의 이름입니다. |
    | **서브넷** | functions | 함수 앱이 연결될 서브넷의 이름입니다. |

1. **클라이언트 IP 주소 추가** 를 선택하여 현재 클라이언트 IP에 네임스페이스에 대한 액세스 권한을 부여합니다.
    > [!NOTE]
    > Azure Portal이 [이 자습서의 뒷 부분에서 큐에 메시지를 게시](#test-your-locked-down-function-app)하도록 설정하기 위해서는 클라이언트 IP 주소를 허용해야 합니다.
1. 서비스 엔드포인트를 사용하도록 설정하려면 **사용** 을 선택합니다.
1. 선택한 가상 네트워크 및 서브넷을 Service Bus에 대한 방화벽 규칙에 추가하려면 **추가** 를 선택합니다.
1. 업데이트된 방화벽 규칙을 저장하려면 **저장** 을 선택합니다.

이제 가상 네트워크의 리소스가 프라이빗 엔드포인트를 사용하여 Service Bus와 통신할 수 있습니다.

## <a name="create-a-file-share"></a>파일 공유 만들기

1. 만든 스토리지 계정의 왼쪽 메뉴에서 **파일 공유** 를 선택합니다.

1. **+ 파일 공유** 를 선택합니다. 이 자습서의 용도에 따라 파일 이름을 공유 *파일* 로 지정합니다.

    :::image type="content" source="./media/functions-create-vnet/4-create-file-share.png" alt-text="스토리지 계정에서 파일 공유를 만드는 방법을 보여주는 스크린샷입니다.":::

1. **만들기** 를 선택합니다.

## <a name="get-the-storage-account-connection-string"></a>스토리지 계정 연결 문자열 가져오기

1. 만든 스토리지 계정의 왼쪽 메뉴에서 **액세스 키** 를 선택합니다.

1. **키 표시** 를 선택합니다. **Key1** 의 연결 문자열을 복사하고 저장합니다. 앱 설정을 구성할 때 이 연결 문자열이 필요합니다.

    :::image type="content" source="./media/functions-create-vnet/5-get-store-connection-string.png" alt-text="스토리지 계정 연결 문자열을 가져오는 방법을 보여주는 스크린샷입니다.":::

## <a name="create-a-queue"></a>큐 만들기

Azure Functions Service Bus 트리거에서 이벤트를 가져오는 큐를 만듭니다.

1. Service Bus의 왼쪽에 있는 메뉴에서 **큐** 를 선택합니다.

1. **큐** 를 선택합니다. 이 자습서에서는 새 큐의 이름으로 *queue* 이름을 제공합니다.

    :::image type="content" source="./media/functions-create-vnet/6-create-queue.png" alt-text="Service Bus 큐를 만드는 방법을 보여주는 스크린샷입니다.":::

1. **만들기** 를 선택합니다.

## <a name="get-a-service-bus-connection-string"></a>Service Bus 연결 문자열 가져오기

1. Service Bus의 왼쪽에 있는 메뉴에서 **공유 액세스 정책** 을 선택합니다.

1. **RootManageSharedAccessKey** 를 선택합니다. **기본 연결 문자열** 을 복사하고 저장합니다. 앱 설정을 구성할 때 이 연결 문자열이 필요합니다.

    :::image type="content" source="./media/functions-create-vnet/7-get-service-bus-connection-string.png" alt-text="Service Bus 연결 문자열을 가져오는 방법을 보여주는 스크린샷입니다.":::

## <a name="integrate-the-function-app"></a>함수 앱 통합

가상 네트워크에서 함수 앱을 사용하려면 서브넷에 조인해야 합니다. Azure Functions 가상 네트워크 통합에 대해 특정 서브넷을 사용합니다. 이 자습서에서 만드는 다른 프라이빗 엔드포인트에 대해서는 기본 서브넷을 사용합니다.

1. 함수 앱의 왼쪽에 있는 메뉴에서 **네트워킹** 을 선택합니다.

1. **VNet 통합** 에서 **구성하려면 클릭** 을 선택하세요.

    :::image type="content" source="./media/functions-create-vnet/8-connect-app-vnet.png" alt-text="가상 네트워크 통합으로 이동하는 방법을 보여주는 스크린샷입니다.":::

1. **VNet 추가** 를 선택합니다.

1. **Virtual Network** 에서 앞서 만든 가상 네트워크를 선택합니다.

1. 앞서 만든 **함수** 서브넷을 선택합니다. **확인** 을 선택합니다.  이제 함수 앱이 가상 네트워크와 통합되었습니다.

    가상 네트워크와 함수 앱이 서로 다른 구독에 있는 경우 먼저 가상 네트워크의 서비스 주체 **Microsoft Azure App Service** **대한 기여자** 액세스를 제공해야 합니다.

    :::image type="content" source="./media/functions-create-vnet/9-connect-app-subnet.png" alt-text="서브넷에 함수 앱을 연결하는 방법을 보여주는 스크린샷입니다.":::

## <a name="configure-your-function-app-settings"></a>함수 앱 설정 구성

1. 함수 앱의 왼쪽에 있는 메뉴에서 **구성** 을 선택합니다.

1. 가상 네트워크에서 함수 앱을 사용하려면 다음 표에 표시된 앱 설정을 업데이트합니다. 설정을 추가하거나 편집하려면 앱 설정 테이블의 맨 오른쪽 열에서 **+ 새 애플리케이션 설정** 또는 **편집** 아이콘을 선택합니다. 완료되면 **저장** 을 선택합니다.

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **AzureWebJobsStorage** | mysecurestorageConnectionString | 만든 스토리지 계정의 연결 문자열입니다. 이 스토리지 연결 문자열은 [스토리지 계정 연결 문자열 가져오기](#get-the-storage-account-connection-string) 섹션에서 가져온 것입니다. 이 설정을 통해 함수 앱은 런타임 시 정상 작업에 보안 스토리지 계정을 사용할 수 있습니다. | 
    | **WEBSITE_CONTENTAZUREFILECONNECTIONSTRING**  | mysecurestorageConnectionString | 만든 스토리지 계정의 연결 문자열입니다. 이 설정을 통해 함수 앱에서 배포 중에 사용되는 Azure Files에 대한 보안 스토리지 계정을 사용할 수 있습니다. |
    | **WEBSITE_CONTENTSHARE** | files | 스토리지 계정에서 만든 파일 공유의 이름입니다. WEBSITE_CONTENTAZUREFILECONNECTIONSTRING과 함께 이 설정을 사용합니다. |
    | **SERVICEBUS_CONNECTION** | myServiceBusConnectionString | Service Bus의 연결 문자열에 대해 이 앱 설정을 만듭니다. 이 스토리지 연결 문자열은 [Service Bus 연결 문자열 가져오기](#get-a-service-bus-connection-string) 섹션에서 가져온 것입니다.|
    | **WEBSITE_CONTENTOVERVNET** | 1 | 이 앱 설정을 만듭니다. 1 값을 사용하면 스토리지 계정이 가상 네트워크로 제한된 경우 함수 앱을 스케일링할 수 있습니다. |
    | **WEBSITE_VNET_ROUTE_ALL** | 1 | 이 앱 설정을 만듭니다. 앱이 가상 네트워크와 통합되는 경우 가상 네트워크와 동일한 DNS 서버를 사용합니다. Azure DNS Private Zone에서 사용할 수 있도록 함수 앱에 이 설정이 필요합니다. 프라이빗 엔드포인트를 사용하는 경우 필요합니다. |

1. **구성** 뷰에서 **함수 런타임 설정** 탭을 선택합니다.

1. **런타임 규모 모니터링** 을 **켜기** 로 설정합니다. 그런 다음 **저장** 을 선택합니다. 런타임 기반 크기 조정을 사용하면 가상 네트워크 내에서 실행되는 서비스에 HTTP가 아닌 트리거 함수를 연결할 수 있습니다.

    :::image type="content" source="./media/functions-create-vnet/11-enable-runtime-scaling.png" alt-text="Azure Functions에 대한 런타임 기반 크기 조정을 사용하도록 설정하는 방법을 보여주는 스크린샷입니다.":::

## <a name="deploy-a-service-bus-trigger-and-http-trigger"></a>Service Bus 트리거 및 HTTP 트리거 배포

1. GitHub에서 다음과 같은 샘플 리포지토리로 이동합니다. 여기에는 함수 앱과 두 개의 함수, 즉 HTTP 트리거와 Service Bus 큐 트리거가 포함되어 있습니다.

    <https://github.com/Azure-Samples/functions-vnet-tutorial>

1. 페이지 상단에서 **포크** 를 선택하여 고유한 GitHub 계정 또는 조직에서 이 리포지토리의 포크를 만듭니다.

1. 함수 앱의 왼쪽에 있는 메뉴에서 **배포 센터** 를 선택합니다. 그런 다음, **설정** 을 선택합니다.

1. **설정** 탭에서 다음 표에 나와 있는 배포 설정을 사용합니다.

    | 설정      | 제안 값  | Description      |
    | ------------ | ---------------- | ---------------- |
    | **원본** | GitHub | 2단계에서 샘플 코드에 대한 GitHub 리포지토리를 만들어야 합니다. | 
    | **조직**  | myOrganization | 리포지토리가 체크인된 조직입니다. 일반적으로 사용자의 계정입니다. |
    | **리포지토리** | functions-vnet-tutorial | https://github.com/Azure-Samples/functions-vnet-tutorial 에서 포크된 리포지토리입니다. |
    | **분기** | main | 만든 리포지토리의 주 분기입니다. |
    | **런타임 스택** | .NET | 샘플 코드는 C#입니다. |
    | **버전** | .NET Core 3.1 | 런타임 버전입니다. |

1. **저장** 을 선택합니다. 

    :::image type="content" source="./media/functions-create-vnet/12-deploy-portal.png" alt-text="포털을 통해 Azure Functions 코드를 배포하는 방법을 보여주는 스크린샷입니다.":::

1. 초기 배포에는 몇 분 정도 걸릴 수 있습니다. 앱이 배포되면 **로그** 탭에 **성공(활성)** 상태 메시지가 표시됩니다. 필요한 경우 페이지를 새로 고칩니다.

축하합니다! 샘플 함수 앱을 배포했습니다.

## <a name="lock-down-your-function-app"></a>함수 앱 잠금

이제 함수 앱을 잠글 프라이빗 엔드포인트를 만듭니다. 이 프라이빗 엔드포인트는 개인 IP 주소를 사용하여 비공개적으로 안전하게 가상 네트워크에 함수 앱을 연결합니다. 

자세한 내용은 [프라이빗 엔드포인트 설명서](../private-link/private-endpoint-overview.md)를 참조하세요.

1. 함수 앱의 왼쪽에 있는 메뉴에서 **네트워킹** 을 선택합니다.

1. **프라이빗 엔드포인트 연결** 에서 **프라이빗 엔드포인트 연결 구성** 을 선택합니다.

    :::image type="content" source="./media/functions-create-vnet/14-navigate-app-private-endpoint.png" alt-text="함수 앱 프라이빗 엔드포인트로 이동하는 방법을 보여주는 스크린샷입니다.":::

1. **추가** 를 선택합니다.

1. 열리는 창에서 다음과 같은 프라이빗 엔드포인트 설정을 사용합니다.

    :::image type="content" source="./media/functions-create-vnet/15-create-app-private-endpoint.png" alt-text="함수 앱 프라이빗 엔드포인트를 만드는 방법을 보여주는 스크린샷입니다. 이름은 functionapp-endpoint입니다. 구독은 'Private Test Sub CACHHAI'이고 가상 네트워크는 MyVirtualNet-tutorial입니다. 서브넷은 기본값입니다.":::

1. **확인** 을 선택하여 프라이빗 엔드포인트를 추가합니다. 
 
축하합니다! 프라이빗 엔드포인트를 추가하여 함수 앱, Service Bus 및 스토리지 계정에 대한 보안을 설정했습니다.

### <a name="test-your-locked-down-function-app"></a>잠긴 함수 앱 테스트

1. 함수 앱의 왼쪽에 있는 메뉴에서 **함수** 를 선택합니다.

1. **ServiceBusQueueTrigger** 를 선택합니다.

1. 왼쪽 메뉴에서 **모니터** 를 선택합니다. 
 
앱을 모니터링할 수 없음을 알 수 있습니다. 사용 중인 브라우저는 가상 네트워크에 액세스할 수 없으므로 가상 네트워크 내의 리소스에 직접 액세스할 수 없습니다. 
 
Application Insights를 사용하여 함수를 모니터링하는 다른 방법은 다음과 같습니다.

1. 함수 앱의 왼쪽에 있는 메뉴에서 **Application Insights** 를 선택합니다. 그런 다음, **Application Insights 데이터 보기** 를 선택합니다.

    :::image type="content" source="./media/functions-create-vnet/16-app-insights.png" alt-text="함수 앱에 대한 Application Insights를 보는 방법을 보여주는 스크린샷입니다.":::

1. 왼쪽 메뉴에서 **라이브 메트릭** 을 선택합니다.

1. 새 탭을 엽니다. Service Bus의 왼쪽에 있는 메뉴에서 **큐** 를 선택합니다.

1. 큐를 선택합니다.

1. 왼쪽 메뉴에서 **Service Bus Explorer** 를 선택합니다. **보내기** 에서 **콘텐츠 형식** 으로 **텍스트/일반** 을 선택합니다. 그런 다음 메시지를 입력합니다. 

1. **보내기** 를 선택하여 메시지를 보냅니다.

    :::image type="content" source="./media/functions-create-vnet/17-send-service-bus-message.png" alt-text="포털을 사용하여 Service Bus 메시지를 보내는 방법을 보여주는 스크린샷입니다.":::

1. **라이브 메트릭** 탭에서 Service Bus 큐 트리거가 발생한 것을 확인할 수 있습니다. 그렇지 않은 경우 **Service Bus Explorer** 에서 메시지를 다시 보냅니다.

    :::image type="content" source="./media/functions-create-vnet/18-hello-world.png" alt-text="함수 앱에 대한 라이브 메트릭을 사용하여 메시지를 보는 방법을 보여주는 스크린샷입니다.":::

축하합니다! 프라이빗 엔드포인트를 사용하여 함수 앱 설정을 테스트했습니다.

## <a name="understand-private-dns-zones"></a>개인 DNS 영역 이해
프라이빗 엔드포인트를 사용하여 Azure 리소스에 연결했습니다. 퍼블릭 엔드포인트 대신 개인 IP 주소에 연결하는 중입니다. 기존 Azure 서비스는 기존 DNS를 사용하여 페블릭 엔드포인트에 연결하도록 구성됩니다. 프라이빗 엔드포인트에 연결하려면 DNS 구성을 재정의해야 합니다.

프라이빗 엔드포인트로 구성된 각 Azure 리소스에 대해 개인 DNS 영역이 생성됩니다. 프라이빗 엔드포인트와 연결된 각 개인 IP 주소에 대한 DNS 레코드가 만들어집니다.

이 자습서에서는 다음과 같은 DNS 영역을 만들었습니다.

- privatelink.file.core.windows.net
- privatelink.blob.core.windows.net
- privatelink.servicebus.windows.net
- privatelink.azurewebsites.net

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 프리미엄 함수 앱, 스토리지 계정 및 Service Bus를 만들었습니다. 이러한 리소스는 모두 프라이빗 엔드포인트 뒤에 보안을 설정했습니다. 

다음 링크를 사용하여 Azure Functions 네트워킹 옵션 및 프라이빗 엔드포인트에 대해 자세히 알아보세요.

- [Azure Functions의 네트워킹 옵션](./functions-networking-options.md)
- [Azure Functions 프리미엄 계획](./functions-premium-plan.md)
- [Service Bus 프라이빗 엔드포인트](../service-bus-messaging/private-link-service.md)
- [Azure Storage 프라이빗 엔드포인트](../storage/common/storage-private-endpoints.md)
