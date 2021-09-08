---
title: Azure Blockchain Service 중지 알림 및 지침
description: 관리형 또는 자체 관리형 블록체인 제품으로 Azure Blockchain Service 마이그레이션
ms.date: 05/10/2021
ms.topic: how-to
ms.openlocfilehash: d461698cf27bc425da337de7829e3e8f0b355196
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123309805"
---
# <a name="migrate-azure-blockchain-service"></a>Azure Blockchain Service 마이그레이션

Azure Blockchain Service의 원장 데이터를 대체 제품으로 마이그레이션할 수 있습니다.

> [!IMPORTANT]
> **2021년 9월 10일** 부로 Azure Blockchain이 사용 중지됩니다. 프로덕션 또는 평가에서 개발 상태에 따라 Azure Blockchain 서비스의 원장 데이터를 대체 제품으로 마이그레이션하세요.

## <a name="evaluate-alternatives"></a>대안 평가

마이그레이션을 계획할 때 첫 번째 단계는 대체 제품을 평가하는 것입니다. 개발 상태가 프로덕션 중인지 또는 평가 중인지에 따라 다음 대안을 평가합니다.

### <a name="production-or-pilot-phase"></a>프로덕션 또는 파일럿 단계

프로덕션 또는 파일럿 단계에 있는 블록체인 솔루션을 이미 배포하고 개발한 경우 다음과 같은 대안이 있습니다.

#### <a name="quorum-blockchain-service"></a>Quorum Blockchain Service

Quorum Blockchain Service는 Azure에서 Quorum을 원장 기술로 지원하는 ConsenSys의 관리형 제품입니다.

- **관리형 제품** - Quorum Blockchain Service는 Azure Blockchain Service에 비해 추가적인 관리 오버헤드가 없습니다.
- **원장 기술** - Azure Blockchain Service에서 사용되는 GoQuorum 원장 기술의 향상된 버전인 ConsenSys Quorum을 기반으로 합니다. 새로 배울 필요가 없습니다. 자세한 내용은 [Consensys Quorum FAQ](https://consensys.net/quorum/faq)를 참조하세요.
- **연속성** - 기존 데이터를 ConsenSys의 Quorum Blockchain Service로 마이그레이션할 수 있습니다. 자세한 내용은 [Azure Blockchain Service에서 데이터 내보내기](#export-data-from-azure-blockchain-service)를 참조하세요.

자세한 내용은 [Quorum Blockchain Service](https://consensys.net/QBS)를 참조하세요.

#### <a name="azure-vm-based-deployment"></a>Azure VM 기반 배포

IaaS VM에 블록체인을 배포하는 데 사용할 수 있는 여러 가지 블록체인 리소스 관리 템플릿이 있습니다.

- **원장 기술** - 새로운 ConsenSys Quorum을 포함하여 Quorum 원장 기술을 계속 사용할 수 있습니다.
- **자체 관리** - 배포 후 고객은 인프라 및 블록체인 스택을 관리합니다.

### <a name="new-deployment-or-evaluation-phase"></a>신규 배포 또는 평가 단계

새 솔루션 개발을 시작하거나 평가 단계에 있는 경우 시나리오 요구 사항에 따라 다음과 같은 대안이 있습니다.

- [Azure Marketplace의 Quorum 템플릿](https://azuremarketplace.microsoft.com/marketplace/apps/consensys.quorum-dev-quickstart)
- [Azure Marketplace의 Besu 템플릿](https://azuremarketplace.microsoft.com/marketplace/apps/consensys.hyperledger-besu-quickstart)

### <a name="how-to-migrate-to-an-alternative"></a>대체 제품으로 마이그레이션하는 방법

프로덕션 워크로드를 마이그레이션하려면 먼저 [Azure Blockchain 서비스에서 데이터를 내보냅니다](#export-data-from-azure-blockchain-service). 데이터의 복사본을 만든 후에는 해당 데이터를 원하는 대체 제품으로 전송할 수 있습니다.

권장하는 마이그레이션 대상은 ConsenSys Quorum Blockchain Service입니다. 이 서비스에 등록하려면 [Quorum Blockchain Service](https://consensys.net/QBS) 페이지에서 등록합니다.

Azure에서 가상 머신을 사용하여 블록체인 솔루션을 자체 관리하려면 [Azure VM 기반 Quorum 지침](#azure-vm-based-quorum-guidance)을 참조하여 트랜잭션 및 유효성 검사기 노드를 설정합니다.
## <a name="export-data-from-azure-blockchain-service"></a>Azure Blockchain Service에서 데이터 내보내기

현재 개발 상태에 따라 Azure Blockchain Service에서 기존 원장 데이터를 사용할 수도 있고, 새 네트워크를 시작하고 원하는 솔루션을 사용할 수 있습니다. Azure Blockchain Service에서 기존 원장 데이터를 사용할 필요가 없거나 사용하지 않으려는 모든 시나리오에서 선택한 솔루션에 따라 새 컨소시엄을 만드는 것이 좋습니다.

### <a name="open-support-case"></a>지원 사례 열기

유료 지원 플랜이 있는 경우 Microsoft 지원 티켓을 열어 컨소시엄을 일시 중지하고 블록체인 데이터를 내보냅니다.

1. 지원 티켓을 열려면 Azure Portal을 사용합니다. *문제 설명* 에 다음 세부 정보를 입력합니다.

    ![Azure Portal의 지원 티켓 문제 설명 양식](./media/migration-guide/problem-description.png)

    | 필드 | 응답 |
    |-------|--------- |
    | 문제 유형 | 기술 |
    | 서비스 | Azure Blockchain Service - 미리 보기 |
    | 요약 | 마이그레이션할 데이터 요청 |
    | 문제 유형 | 기타 |

1. *추가 세부 정보* 에 다음과 같은 세부 정보를 추가합니다.

    ![Azure Portal의 지원 티켓 추가 세부 정보 양식](./media/migration-guide/additional-details.png)

    - 구독 ID 또는 Azure Resource Manager 리소스 ID
    - 테넌트
    - 컨소시엄 이름
    - 지역
    - 멤버 이름
    - 마이그레이션을 시작하려는 날짜/시간

컨소시엄에 여러 멤버가 있는 경우 각 멤버는 각 멤버 데이터에 대한 별도의 지원 티켓을 열어야 합니다.

### <a name="pause-consortium"></a>컨소시엄 일시 중지

데이터를 내보내기 위해 컨소시엄이 일시 중지되고 이 시간에는 트랜잭션이 실패하므로 컨소시엄 멤버와 데이터 내보내기를 조정해야 합니다.

Azure Blockchain Service 팀은 컨소시엄을 일시 중지하고, 데이터의 스냅샷을 내보내고, 암호화된 형식으로 다운로드할 수 있도록 단기 SAS URL을 통해 데이터를 제공합니다. 스냅샷을 생성한 후 컨소시엄이 다시 시작됩니다.

> [!IMPORTANT]
> 네트워크에 대한 새 블록체인 트랜잭션을 시작하는 모든 애플리케이션을 중지해야 합니다. 활성 애플리케이션이 있으면 데이터가 손실되거나 원래 네트워크와 마이그레이션된 네트워크가 동기화되지 않을 수 있습니다.

### <a name="download-data"></a>데이터 다운로드

#### <a name="data-format-v1"></a>데이터 형식 v1

Microsoft 지원에서 제공한 단기 SAS URL 링크를 사용하여 데이터를 다운로드합니다.

> [!IMPORTANT]
> 7일 이내에 데이터를 다운로드해야 합니다.

API 액세스 키를 사용하여 데이터를 암호 해독합니다. [Azure Portal에서](configure-transaction-nodes.md#access-keys) 또는 [REST API를 통해](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) 키를 얻을 수 있습니다.

> [!CAUTION]
> 기본 트랜잭션 노드 API 액세스 키 1만이 해당 멤버의 모든 노드 데이터를 암호화하는 데 사용됩니다.
>
> 마이그레이션 중에 API 액세스 키를 초기화하지 마세요.

#### <a name="data-format-v2"></a>데이터 형식 v2

이 버전에서는 데이터 대신 SAS 토큰이 암호화되어 더 빠른 스냅샷 만들기가 가능합니다. ConsenSys Quorum Blockchain Service로 마이그레이션하기로 선택한 경우 Quorum Blockchain Service로의 가져오기도 더 빨라집니다.

SAS 토큰이 해독되면 데이터를 정상적으로 다운로드할 수 있습니다. 데이터 자체에는 추가 암호화 계층이 없습니다.

> [!IMPORTANT]
> 데이터 형식 v2로 스냅샷을 만들면 약 8-10배 더 빠르므로 가동 중지 시간이 줄어듭니다.

> [!CAUTION]
> 기본 트랜잭션 노드 API 액세스 키 1은 SAS 토큰을 암호화하는 데 사용됩니다.
>
> 마이그레이션 사이 또는 마이그레이션 중에 API 액세스 키를 초기화하지 마세요.

ConsenSys Quorum Blockchain Service 또는 IaaS VM 기반 배포에 데이터를 사용할 수 있습니다.

ConsenSys Quorum Blockchain Service 마이그레이션의 경우 [qbsmigration@consensys.net](mailto:qbsmigration@consensys.net)에서 ConsenSys에 문의하세요.

IaaS VM 기반 배포에 데이터를 사용하려면 이 문서의 [Azure VM 기반 Quorum 지침](#azure-vm-based-quorum-guidance) 섹션에 설명된 단계를 따르세요.

### <a name="delete-resources"></a>리소스 삭제

데이터 복사를 완료한 후에는 Azure Blockchain 멤버 리소스를 삭제하는 것이 좋습니다. 이러한 리소스가 있는 동안에는 요금이 계속 청구됩니다.

## <a name="azure-vm-based-quorum-guidance"></a>Azure VM 기반 Quorum 지침

다음 단계에 따라 트랜잭션 노드 및 유효성 검사기 노드를 만듭니다.

### <a name="transaction-node"></a>트랜잭션 노드

트랜잭션 노드는 두 개의 구성 요소로 이루어집니다. Tessera는 프라이빗 트랜잭션에 사용되고 Geth는 Quorum 애플리케이션에 사용됩니다. 유효성 검사기 노드에는 Geth 구성 요소만 필요합니다.

#### <a name="tessera"></a>Tessera

1. Java 11을 설치합니다. 예들 들어 `apt install default-jre`입니다.
1. `tessera-config.json`에서 경로를 업데이트합니다. `/working-dir/**`의 모든 참조를 `/opt/blockchain/data/working-dir/**`로 변경합니다.
1. 새 IP 주소에 따라 다른 트랜잭션 노드의 IP 주소를 업데이트합니다. Tessera 구성에서 HTTPS를 사용하도록 설정하지 않았으므로 HTTPS는 작동하지 않습니다. TLS를 구성하는 방법에 대한 자세한 내용은 [Tessera TLS 구성](https://docs.tessera.consensys.net/en/stable/HowTo/Configure/TLS/) 문서를 참조하세요.
1. 포트 9000에 대한 인바운드 연결을 허용하도록 NSG 규칙을 업데이트합니다.
1. 다음 명령을 사용하여 Tessera를 설치합니다.

    ```bash
    java -Xms512M -Xmx1731M -Dlogback.configurationFile=/tessera/logback-tessera.xml -jar tessera.jar -configfile /opt/blockchain/data/working-dir/tessera-config.json > tessera.log 2>&1 &
    ```

#### <a name="geth"></a>Geth

1. `/opt/blockchain/data/working-dir/dd/static-nodes.json`에서 enode 주소의 IP를 업데이트합니다. 공용 IP 주소가 허용됩니다.
1. `/geth/config.toml`의 StaticNodes 키에서 동일하게 IP 주소를 변경합니다.
1. 포트 30303에 대한 인바운드 연결을 허용하도록 NSG 규칙을 업데이트합니다.
1. 다음 명령을 사용하여 Geth를 실행합니다.

    ```bash
    export NETWORK_ID='' # Get network ID from metadata. The network ID is the same for consortium.

    PRIVATE_CONFIG=tm.ipc geth --config /geth/config.toml --datadir /opt/blockchain/data/working-dir/dd --networkid $NETWORK_ID --istanbul.blockperiod 5 --nodiscover --nousb --allow-insecure-unlock --verbosity 3 --txpool.globalslots 80000 --txpool.globalqueue 80000 --txpool.accountqueue 50000 --txpool.accountslots 50000 --targetgaslimit 700000000 --miner.gaslimit 800000000 --syncmode full --rpc --rpcaddr 0.0.0.0 --rpcport 3100 --rpccorsdomain '*' --rpcapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul --ws --wsaddr 0.0.0.0 --wsport 3000 --wsorigins '*' --wsapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul
    ```

### <a name="validator-node"></a>유효성 검사기 노드

유효성 검사기 노드 단계는 Geth 시작 명령에 추가 플래그 `-mine`이 있다는 점을 제외하고 트랜잭션 노드와 유사합니다. Tessera는 유효성 검사기 노드에서 시작되지 않습니다. 쌍을 이루는 Tessera 없이 Geth를 실행하려면 Geth 명령에서 `PRIVATE_CONFIG=ignore`를 전달합니다. 다음 명령을 사용하여 Geth를 실행합니다.

```bash
export NETWORK_ID=`j q '.APP_SETTINGS | fromjson | ."network-id"' env.json`

PRIVATE_CONFIG=ignore geth --config /geth/config.toml --datadir /opt/blockchain/data/working-dir/dd --networkid $NETWORK_ID --istanbul.blockperiod 5 --nodiscover --nousb --allow-insecure-unlock --verbosity 3 --txpool.globalslots 80000 --txpool.globalqueue 80000 --txpool.accountqueue 50000 --txpool.accountslots 50000 --targetgaslimit 700000000 --miner.gaslimit 800000000 --syncmode full --rpc --rpcaddr 0.0.0.0 --rpcport 3100 --rpccorsdomain '*' --rpcapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul --ws --wsaddr 0.0.0.0 --wsport 3000 --wsorigins '*' --wsapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul –mine
```

## <a name="upgrading-quorum"></a>Quorum 업그레이드

Azure Blockchain Service는 아래에 나열된 Quorum 버전 중 하나를 실행 중일 것입니다. 동일한 Quorum 버전을 사용할 수도 있고, 아래 단계에 따라 최신 버전의 ConsenSys Quorum을 사용할 수도 있습니다.

### <a name="upgrade-quorum-version-260-or-270-to-consensys-2110"></a>Quorum 2.6.0 또는 2.7.0 버전에서 ConsenSys 21.1.0 버전으로 업그레이드

Quorum 2.6 또는 2.7 버전에서 업그레이드하는 방법은 간단합니다. 다음 링크를 사용하여 새 버전을 다운로드하고 업데이트합니다.
1. [ConsenSys Quorum 및 관련 이진 파일 v21.1.0](https://github.com/ConsenSys/quorum/releases/tag/v21.1.0)을 다운로드합니다.
1. Tessera 최신 버전 [tessera-app-21.1.0-app.jar](https://github.com/ConsenSys/tessera/releases/tag/tessera-21.1.0) 파일을 다운로드합니다.

### <a name="upgrade-quorum-version-250-to-consensys-2110"></a>Quorum 2.5.0 버전에서 ConsenSys 21.1.0 버전으로 업그레이드

1. [ConsenSys Quorum 및 관련 이진 파일 v21.1.0](https://github.com/ConsenSys/quorum/releases/tag/v21.1.0)을 다운로드합니다.
1. Tessera 최신 버전 [tessera-app-21.1.0-app.jar](https://github.com/ConsenSys/tessera/releases/tag/tessera-21.1.0) 파일을 다운로드합니다.
2\.5.0 버전의 경우 몇 가지 사소한 genesis 파일 변경이 있습니다. genesis 파일에서 다음과 같이 변경합니다.

1. `byzantiumBlock` 값이 1로 설정되었습니다. 이 값은 0인 `constantinopleBlock`보다 작으면 안 됩니다. `byzantiumBlock` 값을 0으로 설정합니다.
1. `petersburgBlock`, `istanbulBlock`을 향후 블록으로 설정합니다. 이 값은 모든 노드에서 동일해야 합니다.
1. 이 단계는 선택 사항입니다. `ceil2Nby3Block`이 Azure Blockchain Service Quorum 2.5.0 버전에서 잘못 배치되었습니다. istanbul config 내에 있어야 하며 향후 블록 값을 설정해야 합니다. 이 값은 모든 노드에서 동일해야 합니다.
1. 다음 명령을 사용하여 geth를 실행하고 genesis 블록을 다시 초기화합니다.

    ```bash
    geth --datadir "Data Directory Path" init "genesis file path"
    ```

1.  Geth를 실행합니다.

## <a name="exported-data-reference"></a>내보낸 데이터 참조

이 섹션에서는 메타데이터 및 IaaS VM 배포로 데이터를 가져오는 데 도움이 되는 폴더 구조에 대해 설명합니다.

### <a name="metadata-info"></a>메타데이터 정보

| 속성               | 샘플                | Description           |
|--------------------|-----------------------|-----------------------|
| consortium_name    | \<ConsortiumName\>    | 컨소시엄 이름(Azure Blockchain Service에서 고유한 이름) |
| Consortium_Member_Count || 컨소시엄의 멤버 수 |
| member_name        | \<memberName\>        | 블록체인 멤버 이름(Azure Blockchain Service에서 고유한 이름) |
| node_name          | transaction-node      | 노드 이름(각 멤버에 여러 노드가 있음) |
| network_id         | 543                   | Geth 네트워크 ID      |
| is_miner           | False                 | Is_Miner == true(유효성 검사기 노드), Is_Miner == false(트랜잭션 노드) |
| quorum_version     | 2.7.0                 | Quorum 버전     |
| tessera_version    | 0.10.5                | Tessera 버전      |
| java_version       | java-11-openjdk-amd64 | Tessera에서 사용하는 Java 버전 |
| CurrentBlockNumber |                       | 블록체인 네트워크의 현재 블록 번호 |

## <a name="migrated-data-folder-structure"></a>마이그레이션된 데이터 폴더 구조

최상위 수준에는 멤버의 각 노드에 해당하는 폴더가 있습니다.

- **표준 SKU** - 두 개의 유효성 검사기 노드(validator-node-0 및 validator-node-1)
- **기본 SKU** - 하나의 유효성 검사기 노드(validator-node-0)
- **트랜잭션 노드** - 기본 트랜잭션 노드(transaction-node)

다른 트랜잭션 노드 폴더의 이름은 트랜잭션 노드 이름을 따라 지정됩니다.

### <a name="node-level-folder-structure"></a>노드 수준 폴더 구조

각 노드 수준 폴더에는 암호화 키를 사용하여 암호화된 Zip 파일이 포함되어 있습니다. 암호화 키를 얻는 방법에 대한 자세한 내용은 이 문서의 [데이터 다운로드](#download-data) 섹션을 참조하세요.

| 디렉터리/파일 | Description |
|----------------|--------------|
| /config/config.toml | Geth 매개 변수. 명령줄 매개 변수가 우선 |
| /config/genesis.json | Genesis 파일 |
| /config/logback-tessera.xml | Tessera에 대한 로그백 구성 |
| /config/static-nodes.json | 정적 노드. 부트스트랩 노드가 제거되고 자동 검색이 해제됨 |
| /config/tessera-config.json | Tessera 구성 |
| /data/c/ | Tessera DB |
| /data/dd/ | Geth 데이터 디렉터리 |
| /env/env | 메타데이터 |
| /keys/ | Tessera 키 |
| /scripts/ | 시작 스크립트(참조용으로만 제공됨) |

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-does-service-retirement-mean-for-existing-customers"></a>기존 고객에게 서비스 사용 중지는 무엇을 의미하나요?

2021년 9월 10일 이후에는 기존 Azure Blockchain Service를 배포할 수 없습니다. 사용 중지 날짜 전에 요구 사항에 따라 이 문서에서 제안하는 대안을 평가하세요.

### <a name="what-happens-to-existing-deployments-after-the-announcement-of-retirement"></a>사용 중지 발표 후 기존 배포는 어떻게 되나요?

기존 배포는 2021년 9월 10일까지 지원됩니다. 제안된 대안을 평가하고, 데이터를 대체 제품으로 마이그레이션하고, 대체 제품에서 요구 사항을 운영해 보고, Azure Blockchain Service의 배포에서 마이그레이션을 시작하세요.

### <a name="how-long-will-the-existing-deployments-be-supported-on-azure-blockchain-service"></a>Azure Blockchain Service에서 기존 배포가 지원되는 기간은 얼마인가요?

기존 배포는 2021년 9월 10일까지 지원됩니다.

### <a name="will-i-be-allowed-to-create-new-azure-blockchain-members-while-in-retirement-phase"></a>사용 중지 단계에서 새 Azure Blockchain 멤버를 만들 수 있나요?

2021년 5월 10일 이후에는 새 멤버 만들기 또는 배포가 지원되지 않습니다.
