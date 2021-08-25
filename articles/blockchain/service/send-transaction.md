---
title: 스마트 계약 생성, 빌드 및 배포 - Azure Blockchain Service
description: Visual Studio Code에서 Ethereum 확장용 Azure Blockchain Development Kit를 사용하여 Azure Blockchain Service에서 스마트 계약을 만들고 빌드하고 배포하는 방법에 대한 자습서입니다.
ms.date: 11/30/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: 9a38314b9dbc54750da7dfa81ece487c87b73884
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2021
ms.locfileid: "122643525"
---
# <a name="tutorial-create-build-and-deploy-smart-contracts-on-azure-blockchain-service"></a>자습서: Azure Blockchain Service에서 스마트 계약 생성, 빌드 및 배포

이 자습서에서는 Visual Studio Code에서 Ethereum 확장용 Azure Blockchain Development Kit를 사용하여 Azure Blockchain Service에서 스마트 계약을 만들고 빌드하고 배포합니다. 또한 개발 키트를 사용하여 트랜잭션을 통해 스마트 계약 함수를 실행합니다.

[!INCLUDE [Retirement note](./includes/retirement.md)]

Etherum용 Azure Blockchain Development Kit를 사용하여 다음을 수행합니다.

> [!div class="checklist"]
> * 스마트 계약 만들기
> * 스마트 계약 배포
> * 트랜잭션을 통해 스마트 계약 함수 실행

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

* [빠른 시작: Visual Studio Code를 사용하여 Azure Blockchain Service 컨소시엄 네트워크에 연결](connect-vscode.md) 완료
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Etherum용 Azure Blockchain Development Kit 확장](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x 이상](https://nodejs.org/download)
* [Git 2.10.x 이상](https://git-scm.com)
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

Windows에서는 node-gyp 모듈에 설치된 C++ 컴파일러가 필요합니다. MSBuild 도구를 사용할 수 있습니다.

* Visual Studio 2017이 설치된 경우 `npm config set msvs_version 2017 -g` 명령을 사용하여 MSBuild 도구를 사용하도록 npm을 구성합니다.
* Visual Studio 2019가 설치된 경우 npm에 대한 MS 빌드 도구 경로를 설정합니다. 예를 들어 `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* 그렇지 않으면 *관리자 권한으로 실행* 명령 셸에서 `npm install --global windows-build-tools`를 사용하여 독립 실행형 VS 빌드 도구를 설치합니다.

node-gyp에 대한 자세한 내용은 [GitHub의 node-gyp 리포지토리](https://github.com/nodejs/node-gyp)를 참조하세요.

## <a name="create-a-smart-contract"></a>스마트 계약 만들기

Etherum용 Azure Blockchain Development Kit는 프로젝트 템플릿과 Truffle 도구를 사용하여 계약을 스캐폴드, 빌드 및 배포하는 데 도움을 줍니다. 시작하기 전에 [빠른 시작: Visual Studio Code를 사용하여 Azure Blockchain Service 컨소시엄 네트워크에 연결](connect-vscode.md)을 완료해야 합니다. 이 빠른 시작에서는 Ethereum용 Azure Blockchain Development Kit의 설치 및 구성 방법을 안내합니다.

1. VS Code 명령 팔레트에서 **Blockchain: 새 Solidity 프로젝트** 를 선택합니다.
1. **기본 프로젝트 만들기** 를 선택합니다.
1. `HelloBlockchain`이라는 새 폴더를 만들고, **새 프로젝트 경로를 선택** 합니다.

Azure Blockchain Development Kit에서 새 Solidity 프로젝트를 만들고 초기화합니다. 기본 프로젝트에는 **HelloBlockchain** 스마트 계약 샘플과 Azure Blockchain Service의 컨소시엄 멤버에 빌드 및 배포하는 데 필요한 모든 파일이 포함되어 있습니다. 프로젝트를 만드는 데 몇 분 정도 걸릴 수 있습니다. Azure Blockchain의 출력을 선택하여 VS Code의 터미널 패널의 진행 상황을 모니터링할 수 있습니다.

프로젝트 구조는 다음 예제와 같습니다.

   ![Solidity 프로젝트](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>스마트 계약 빌드

스마트 계약은 프로젝트의 **contracts** 디렉터리에 있습니다. 블록체인에 배포하기 전에 스마트 계약을 컴파일합니다. **계약 빌드** 명령을 사용하여 프로젝트의 모든 스마트 계약을 컴파일합니다.

1. VS Code 탐색기 사이드바에서 프로젝트의 **contracts** 폴더를 펼칩니다.
1. 마우스 오른쪽 단추로 **HelloBlockchain.sol** 을 클릭하고, 메뉴에서 **계약 빌드** 를 선택합니다.

    ![계약 빌드 메뉴 선택 ](./media/send-transaction/build-contracts.png)

Azure Blockchain Development Kit에서 Truffle을 사용하여 스마트 계약을 컴파일합니다.

![Truffle 컴파일러 출력](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>스마트 계약 배포

Truffle은 마이그레이션 스크립트를 사용하여 계약을 Ethereum 네트워크에 배포합니다. 마이그레이션은 프로젝트의 **miginations** 디렉터리에 있는 JavaScript 파일입니다.

1. 스마트 계약을 배포하려면 마우스 오른쪽 단추로 **HelloBlockchain.sol** 을 클릭하고, 메뉴에서 **계약 배포** 를 선택합니다.
1. 명령 팔레트에서 Azure Blockchain 컨소시엄 네트워크를 선택합니다. 컨소시엄 블록체인 네트워크는 프로젝트를 만들 때 프로젝트의 Truffle 구성 파일에 추가되었습니다.
1. **니모닉 생성** 을 선택합니다. 파일 이름을 선택하고, 해당 니모닉 파일을 프로젝트 폴더에 저장합니다. 예: `myblockchainmember.env`. 니모닉 파일은 블록체인 멤버에 대한 Ethereum 프라이빗 키를 생성하는 데 사용됩니다.

Azure Blockchain Development Kit에서 Truffle을 사용하여 계약을 블록체인으로 배포하는 마이그레이션 스크립트를 실행합니다.

![성공적으로 배포된 계약](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function&quot;></a>계약 함수 호출
**HelloBlockchain** 계약의 **SendRequest** 함수는 **RequestMessage** 상태 변수를 변경합니다. 블록체인 네트워크의 상태를 변경하는 작업은 트랜잭션을 통해 수행됩니다. 트랜잭션을 통해 **SendRequest** 함수를 실행하는 스크립트를 만들 수 있습니다.

1. 새 파일을 Truffle 프로젝트의 루트에 만들고, 이름을 `sendrequest.js`로 지정합니다. 다음 Web3 JavaScript 코드를 파일에 추가합니다.

    ```javascript
    var HelloBlockchain = artifacts.require(&quot;HelloBlockchain");

    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Azure Blockchain Development Kit에서 프로젝트를 만들면 Truffle 구성 파일이 컨소시엄 블록체인 네트워크 엔드포인트의 세부 정보를 사용하여 생성됩니다. 프로젝트에서 **truffle-config.js** 를 엽니다. 구성 파일에는 각각 development라는 이름 및 컨소시엄과 동일한 이름의 두 개의 네트워크가 나열되어 있습니다.
1. VS Code의 터미널 창에서 Truffle을 사용하여 컨소시엄 블록체인 네트워크에서 스크립트를 실행합니다. 터미널 창 메뉴 모음의 드롭다운에서 **터미널** 탭 및 **PowerShell** 을 선택합니다.

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    \<blockchain network\>를 **truffle-config.js** 에 정의된 블록체인 네트워크 이름으로 바꿉니다.

Truffle이 블록체인 네트워크에서 스크립트를 실행합니다.

![트랜잭션이 전송되었음을 보여주는 출력](./media/send-transaction/execute-transaction.png)

트랜잭션을 통해 계약 함수를 실행하면 블록이 만들어질 때까지 트랜잭션이 처리되지 않습니다. 트랜잭션을 통해 수행되는 함수는 반환 값 대신 트랜잭션 ID를 반환합니다.

## <a name="query-contract-state"></a>계약 상태 쿼리

스마트 계약 함수는 상태 변수의 현재 값을 반환할 수 있습니다. 상태 변수의 값을 반환하는 함수를 추가해 보겠습니다.

1. **HelloBlockchain.sol** 에서 **getMessage** 함수를 **HelloBlockchain** 스마트 계약에 추가합니다.

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    이 함수는 계약의 현재 상태에 따라 상태 변수에 저장된 메시지를 반환합니다.

1. 마우스 오른쪽 단추로 **HelloBlockchain.sol** 을 클릭하고, 메뉴에서 **계약 빌드** 를 선택하여 스마트 계약의 변경 내용을 컴파일합니다.
1. 배포하려면 마우스 오른쪽 단추로 **HelloBlockchain.sol** 을 클릭하고, 메뉴에서 **계약 배포** 를 선택합니다. 메시지가 표시되면 명령 팔레트에서 Azure Blockchain 컨소시엄 네트워크를 선택합니다.
1. 다음으로 **getMessage** 함수를 호출하는 데 사용하는 스크립트를 만듭니다. 새 파일을 Truffle 프로젝트의 루트에 만들고, 이름을 `getmessage.js`로 지정합니다. 다음 Web3 JavaScript 코드를 파일에 추가합니다.

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");

    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. VS Code의 터미널 창에서 Truffle을 사용하여 블록체인 네트워크에서 스크립트를 실행합니다. 터미널 창 메뉴 모음의 드롭다운에서 **터미널** 탭 및 **PowerShell** 을 선택합니다.

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    \<blockchain network\>를 **truffle-config.js** 에 정의된 블록체인 네트워크 이름으로 바꿉니다.

스크립트에서 getMessage 함수를 호출하여 스마트 계약을 쿼리합니다. **RequestMessage** 상태 변수의 현재 값이 반환됩니다.

![RequestMessage 상태 변수의 현재 값을 보여주는 getmessage 쿼리의 출력](./media/send-transaction/execute-get.png)

값이 **Hello, blockchain!** 이 아닙니다. 대신, 반환되는 값은 자리 표시자입니다. 계약을 변경하여 배포하는 경우 변경된 계약은 새 주소로 배포되고 스마트 계약 생성자의 값이 상태 변수에 할당됩니다. Truffle 샘플인 **2_deploy_contracts.js** 마이그레이션 스크립트는 스마트 계약을 배포하고 자리 표시자 값을 인수로 전달합니다. 생성자는 **RequestMessage** 상태 변수를 자리 표시자 값으로 설정하고 이를 반환합니다.

1. **RequestMessage** 상태 변수를 설정하고 값을 쿼리하려면 **sendrequest.js** 및 **getmessage.js** 스크립트를 다시 실행합니다.

    ![RequestMessage가 설정되었음을 보여주는 sendrequest 및 getmessage 스크립트의 출력](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** 는 **RequestMessage** 상태 변수를 **Hello, blockchain!** 으로 설정하고, **getmessage.js** 는 **RequestMessage** 상태 변수의 값에 대한 계약을 쿼리하고 **Hello, blockchain!** 을 반환합니다.
## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 *블록체인 멤버 만들기* 필수 조건 빠른 시작에서 만든 `myResourceGroup` 리소스 그룹을 삭제하여 리소스를 삭제할 수 있습니다.

리소스 그룹을 삭제하려면 다음을 수행합니다.

1. Azure Portal의 왼쪽 탐색 창에서 **리소스 그룹** 으로 이동하고 삭제하려는 리소스 그룹을 선택합니다.
1. **리소스 그룹 삭제** 를 선택합니다. 리소스 그룹 이름을 입력하여 삭제를 확인하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Blockchain Development Kit를 사용하여 Solidity 프로젝트 샘플을 만들었습니다. 스마트 계약을 빌드하고 배포한 다음, Azure Blockchain Service에서 호스팅되는 블록체인 컨소시엄 네트워크에서 트랜잭션을 통해 함수를 호출했습니다.

> [!div class="nextstepaction"]
> [Azure Blockchain Service를 사용하여 블록체인 애플리케이션 개발](develop.md)
