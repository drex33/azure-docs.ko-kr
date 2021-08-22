---
title: Azure Portal을 사용하여 Blockchain Data Manager 구성 - Azure Blockchain Service
description: Azure Portal을 사용하여 Azure Blockchain Service의 Blockchain Data Manager를 만들고 관리합니다.
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: b80bbc1b302579f0145e7b5cb6c5e3e0561d2ef7
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "122642095"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Azure Portal을 사용하여 Blockchain Data Manager 구성

블록체인 데이터를 캡처하여 Azure Event Grid 토픽으로 전송하도록 Azure Blockchain Service에 대한 Blockchain Data Manager를 구성합니다.

[!INCLUDE [Retirement note](./includes/retirement.md)]

Blockchain Data Manager 인스턴스를 구성하려면 다음을 수행합니다.

* Azure Blockchain Service 트랜잭션 노드에 대한 Blockchain Data Manager 인스턴스 만들기
* 블록체인 애플리케이션 추가

## <a name="prerequisites"></a>필수 구성 요소

* [빠른 시작: Azure Portal을 사용하여 블록체인 멤버 만들기](create-member.md) 또는 [빠른 시작: Azure CLI를 사용하여 Azure Blockchain Service 블록체인 멤버 만들기](create-member-cli.md)를 완료합니다. Blockchain Data Manager를 사용하는 경우 Azure Blockchain Service *표준* 계층을 사용하는 것이 좋습니다.
* [Event Grid 토픽](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) 만들기
* [Azure Event Grid의 이벤트 처리기](../../event-grid/event-handlers.md) 알아보기

## <a name="create-instance"></a>인스턴스 만들기

Blockchain Data Manager 인스턴스는 Azure Blockchain Service 트랜잭션 노드에 연결하여 모니터링합니다. 트랜잭션 노드에 대한 액세스 권한이 있는 사용자만 연결을 만들 수 있습니다. 인스턴스는 트랜잭션 노드의 모든 원시 블록 및 원시 트랜잭션 데이터를 캡처합니다. Blockchain Data Manager는 web3.eth [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) 및 [getTransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) 쿼리에서 반환되는 정보의 상위 집합인 **RawBlockAndTransactionMsg** 메시지를 게시합니다.

아웃바운드 연결은 블록체인 데이터를 Azure Event Grid로 보냅니다. 인스턴스를 만들 때 단일 아웃바운드 연결을 구성합니다. Blockchain Data Manager는 지정된 Blockchain Data Manager 인스턴스에 대한 여러 개의 Event Grid 항목 아웃바운드 연결을 지원합니다. 블록체인 데이터는 단일 대상 또는 여러 대상으로 보낼 수 있습니다. 다른 대상을 추가하려면 인스턴스에 대한 아웃바운드 연결을 추가합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Blockchain Data Manager에 연결하려는 Azure Blockchain Service 멤버로 이동합니다. **Blockchain Data Manager** 를 선택합니다.
1. **추가** 를 선택합니다.

    ![Blockchain Data Manager 추가](./media/data-manager-portal/add-instance.png)

    다음 세부 정보를 입력합니다.

    설정 | 설명
    --------|------------
    이름 | 연결된 Blockchain Data Manager의 고유 이름을 입력합니다. Blockchain Data Manager 이름은 소문자와 숫자가 포함될 수 있으며 최대 길이는 20자입니다.
    트랜잭션 노드 | 트랜잭션 노드를 선택합니다. 읽기 권한이 있는 트랜잭션 노드만 나열됩니다.
    연결 이름 | 블록체인 트랜잭션 데이터가 전송되는 아웃바운드 연결의 고유 이름을 입력합니다.
    Event Grid 엔드포인트 | Blockchain Data Manager 인스턴스와 동일한 구독에서 Event Grid 토픽을 선택합니다.

1. **확인** 을 선택합니다.

    1분 내에 Blockchain Data Manager 인스턴스가 만들어집니다. 인스턴스가 배포된 후 자동으로 시작됩니다. 실행 중인 Blockchain Data Manager 인스턴스는 트랜잭션 노드에서 블록체인 이벤트를 캡처하고 아웃바운드 연결에 데이터를 보냅니다.

    새 인스턴스는 Azure Blockchain Service 멤버의 Blockchain Data Manager 인스턴스 목록에 표시됩니다.

    ![블록체인 데이터 멤버 인스턴스 목록](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>블록체인 애플리케이션 추가

블록체인 애플리케이션을 추가하는 경우 Blockchain Data Manager는 애플리케이션에 대한 이벤트 및 속성 상태를 디코딩합니다. 그렇지 않으면 원시 블록 및 원시 트랜잭션 데이터만 전송됩니다. Blockchain Data Manager는 계약을 배포할 때 계약 주소도 검색합니다. Blockchain Data Manager 인스턴스에 여러 블록체인 애플리케이션을 추가할 수 있습니다.

> [!IMPORTANT]
> 현재는 Solidity [배열 유형](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) 또는 [매핑 유형](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types)을 선언하는 블록체인 애플리케이션은 완전히 지원되지 않습니다. 배열 또는 매핑 형식으로 선언된 속성은 *ContractPropertiesMsg* 또는 *DecodedContractEventsMsg* 메시지에서 디코드되지 않습니다.

Blockchain Data Manager에는 애플리케이션을 추가하는 스마트 계약 ABI 및 바이트 코드 파일이 필요합니다.

### <a name="get-contract-abi-and-bytecode"></a>계약 ABI 및 바이트코드 가져오기

계약 ABI는 스마트 계약 인터페이스를 정의합니다. 스마트 계약과 상호 작용하는 방법을 설명합니다. [Azure Blockchain Development Kit for Ethereum 확장](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)을 사용하여 계약 ABI를 클립보드에 복사할 수 있습니다.

1. Visual Studio Code 탐색기 창에서 Solidity 프로젝트의 **build/contracts** 폴더를 펼칩니다.
1. 마우스 오른쪽 단추로 계약 메타데이터 JSON 파일을 클릭합니다. 파일 이름에는 스마트 계약 이름 뒤에 **.json** 확장명이 붙습니다.
1. **계약 ABI 복사** 를 선택합니다.

    ![계약 ABI 복사가 선택된 Visual Studio Code 창](./media/data-manager-portal/abi-devkit.png)

    계약 ABI가 클립보드에 복사됩니다.

1. **abi** 배열을 JSON 파일로 저장합니다. 예: *abi.json*. 이 파일은 이후 단계에서 사용됩니다.

Blockchain Data Manager에는 스마트 계약용으로 배포된 바이트 코드가 필요합니다. 배포된 바이트 코드는 스마트 계약 바이트 코드와 다릅니다. Azure 블록체인 개발 키트 확장을 사용하여 바이트 코드를 클립보드에 복사합니다.

1. Visual Studio Code 탐색기 창에서 Solidity 프로젝트의 **build/contracts** 폴더를 펼칩니다.
1. 마우스 오른쪽 단추로 계약 메타데이터 JSON 파일을 클릭합니다. 파일 이름에는 스마트 계약 이름 뒤에 **.json** 확장명이 붙습니다.
1. **트랜잭션 바이트 코드 복사** 를 선택합니다.

    ![트랜잭션 바이트 코드 복사가 선택된 Visual Studio Code 창](./media/data-manager-portal/bytecode-devkit.png)

    바이트 코드가 클립보드에 복사됩니다.

1. **바이트 코드** 값을 JSON 파일로 저장합니다. 예: *bytecode.json*. 이 파일은 이후 단계에서 사용됩니다.

다음 예제는 VS Code 편집기에서 열려 있는 *abi.json* 및 *bytecode.json* 파일을 보여줍니다. 파일은 다음과 비슷합니다.

![abi.json 및 bytecode.json 파일 예제](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>계약 ABI 및 바이트 코드 URL 가져오기

Blockchain Data Manager을 사용하려면 애플리케이션을 추가할 때 URL을 통해 계약 ABI 및 바이트 코드 파일에 액세스할 수 있어야 합니다. Azure Storage 계정을 사용하여 URL에 대한 프라이빗 액세스를 제공할 수 있습니다.

#### <a name="create-storage-account"></a>스토리지 계정 만들기

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>계약 파일 업로드

1. 스토리지 계정에 대한 새 컨테이너를 만듭니다. **컨테이너 > 컨테이너** 를 선택합니다.

    ![스토리지 계정 컨테이너 만들기](./media/data-manager-portal/create-container.png)

    | 필드 | 설명 |
    |-------|-------------|
    | 속성  | 컨테이너 이름을 지정합니다. 예: *smartcontract* |
    | 공용 액세스 수준 | *프라이빗(익명 액세스 없음)* 선택 |

1. **확인** 을 선택하여 컨테이너를 만듭니다.
1. 컨테이너를 선택하고 **업로드** 를 선택합니다.
1. [계약 ABI 및 바이트 코드](#get-contract-abi-and-bytecode) 가져오기 섹션에서 만든 JSON 파일 두 개를 모두 선택합니다.

    ![Blob 업로드](./media/data-manager-portal/upload-blobs.png)

    **업로드** 를 선택합니다.

#### <a name="generate-url"></a>URL 생성

각 Blob에 대해 공유 액세스 서명을 생성합니다.

1. ABI JSON Blob을 선택합니다.
1. **SAS 생성** 을 선택합니다.
1. 원하는 액세스 서명 만료를 설정한 다음, **Blob SAS 토큰 및 URL 생성** 을 선택합니다.

    ![SAS 토큰 생성](./media/data-manager-portal/generate-sas.png)

1. 다음 섹션에서 사용할 수 있도록 **Blob SAS URL** 을 복사하여 저장합니다.
1. 바이트 코드 JSON Blob에도 [URL 생성](#generate-url) 단계를 반복합니다.

### <a name="add-application-to-instance"></a>인스턴스에 애플리케이션 추가

1. 인스턴스 목록에서 Blockchain Data Manager 인스턴스를 선택합니다.
1. **블록체인 애플리케이션** 을 선택합니다.
1. **추가** 를 선택합니다.

    ![블록체인 애플리케이션 추가](./media/data-manager-portal/add-application.png)

    블록체인 애플리케이션의 이름과 스마트 계약 ABI 및 바이트 코드 URL을 입력합니다.

    설정 | 설명
    --------|------------
    속성 | 추적할 블록체인 애플리케이션의 고유 이름을 입력합니다.
    계약 ABI | 계약 ABI 파일의 URL 경로입니다. 자세한 내용은 [계약 ABI 및 바이트 코드 URL 만들기](#create-contract-abi-and-bytecode-url)를 참조하세요.
    계약 바이트 코드 | 바이트 코드 파일의 URL 경로입니다. 자세한 내용은 [계약 ABI 및 바이트 코드 URL 만들기](#create-contract-abi-and-bytecode-url)를 참조하세요.

1. **확인** 을 선택합니다.

    애플리케이션이 생성되면 블록체인 애플리케이션 목록에 표시됩니다.

    ![블록체인 애플리케이션 목록](./media/data-manager-portal/artifact-list.png)

Azure Storage 계정을 삭제해도 되고 또 다른 블록체인 애플리케이션을 구성하는 데 사용할 수도 있습니다. Azure Storage 계정을 삭제하려면 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 연결된 스토리지 계정과 기타 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

## <a name="stop-instance"></a>인스턴스 중지

블록체인 이벤트 캡처를 중지하고 아웃바운드 연결에 데이터를 보내려는 경우 Blockchain Manager 인스턴스를 중지합니다. 인스턴스가 중지되면 Blockchain Data Manager에 대한 요금이 발생하지 않습니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/blockchain-service)을 참조하세요.

1. **개요** 로 이동하고 **중지** 를 선택합니다.

    ![인스턴스 중지](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>다음 단계

Blockchain Data Manager 및 Azure Cosmos DB를 사용하여 블록체인 트랜잭션 메시지 탐색기를 만드는 다음 자습서를 시도합니다.

> [!div class="nextstepaction"]
> [Blockchain Data Manager를 사용하여 Azure Cosmos DB로 데이터 보내기](data-manager-cosmosdb.md)