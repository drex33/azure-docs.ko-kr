---
title: Azure Key Vault에서 프라이빗 링크 구성 문제 진단
description: Key Vault에 대한 일반적인 프라이빗 링크 문제를 해결하고 구성에 대해 자세히 알아봅니다.
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 85760971b8f4eb8bfbb431193636532a498d7236
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113665604"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>Azure Key Vault에서 프라이빗 링크 구성 문제 진단

## <a name="introduction"></a>소개

이 문서는 사용자가 Key Vault 및 프라이빗 링크 기능과 관련된 문제를 진단하고 해결하는 데 도움이 됩니다. 이 가이드에서는 처음으로 프라이빗 링크를 작동하거나 프라이빗 링크가 변경되어 작동이 중지된 상황을 수정하는 등의 구성 측면에 대해 설명합니다.

이 기능을 처음 사용하는 경우 [Azure Private Link와 Key Vault 통합](private-link-service.md)을 참조하세요.

### <a name="problems-covered-by-this-article"></a>이 문서에서 다루는 문제

- DNS 쿼리는 프라이빗 링크 기능을 사용하는 것으로 간주되는 개인 IP 주소 대신 키 자격 증명 모음에 대한 공용 IP 주소를 계속 반환합니다.
- 프라이빗 링크를 사용하는 특정 클라이언트가 요청한 모든 작업이 시간 초과 또는 네트워크 오류로 인해 실패하고 있으며 문제가 간헐적으로 발생하지 않습니다.
- 키 자격 증명 모음에 개인 IP 주소가 있지만 요청은 여전히 내부 오류 코드 `ForbiddenByFirewall`과 함께 `403` 응답을 받습니다.
- 프라이빗 링크를 사용하고 있지만 키 자격 증명 모음은 여전히 공용 인터넷의 요청을 수락합니다.
- 키 자격 증명 모음에는 두 개의 프라이빗 엔드포인트가 있습니다. 하나를 사용하는 요청은 정상적으로 작동하지만 나머지 하나를 사용하는 요청은 실패합니다.
- 프라이빗 링크를 사용하는 다른 구독, 키 자격 증명 모음 또는 가상 네트워크가 있습니다. 비슷한 배포를 새로 만들려고 하지만 여기에서 작업하기 위한 프라이빗 링크를 가져올 수 없습니다.

### <a name="problems-not-covered-by-this-article"></a>이 문서에서 다루지 않는 문제

- 일시적인 연결 문제가 있습니다. 특정 클라이언트에서 일부 요청이 작동 중이고 일부는 작동하지 않는 것을 볼 수 있습니다. *일시적인 문제는 일반적으로 프라이빗 링크 구성의 문제로 인해 발생하지 않습니다. 네트워크 또는 클라이언트 오버로드의 징후입니다.*
- BYOK(Bring Your Own Key), CMK(고객 관리형 키)를 지원하는 Azure 제품을 사용 중이거나 키 자격 증명 모음에 저장된 비밀에 액세스합니다. 키 자격 증명 모음 설정에서 방화벽을 사용하도록 설정하면 해당 제품에서 키 자격 증명 모음에 액세스할 수 없습니다. *제품별 설명서를 확인합니다. 방화벽을 사용하도록 설정된 키 자격 증명 모음에 대한 지원을 명시되어 있는지 확인합니다. 필요한 경우 특정 제품에 대한 사항은 지원에 문의하세요.*

### <a name="how-to-read-this-article"></a>이 문서를 읽는 방법

프라이빗 링크를 처음 접하는 경우 또는 복잡한 배포를 평가하는 경우 전체 문서를 읽는 것이 좋습니다. 또는 사용자가 경험하고 있는 문제에 더 적합한 섹션을 자유롭게 선택할 수 있습니다.

그럼 시작하겠습니다.

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. 클라이언트 연결을 소유하고 있는지 확인

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>가상 네트워크에서 클라이언트가 실행되는지 확인

이 가이드는 애플리케이션 코드에서 발생하는 키 자격 증명 모음에 대한 연결을 수정하는 데 도움을 주기 위해 작성되었습니다. Azure Virtual Machines, Azure Service Fabric 클러스터, Azure App Service, AKS(Azure Kubernetes Service) 등에서 실행되는 애플리케이션 및 스크립트를 예로 들 수 있습니다. 이 가이드는 브라우저에서 키 자격 증명 모음에 직접 액세스하는 Azure Portal 웹 기반 사용자 인터페이스에서 수행되는 액세스에도 적용됩니다.

프라이빗 링크의 정의에 따라 애플리케이션, 스크립트 또는 포털이 [프라이빗 엔드포인트 리소스](../../private-link/private-endpoint-overview.md)가 배포된 Virtual Network에 연결된 컴퓨터, 클러스터 또는 환경에서 실행되어야 합니다.

애플리케이션, 스크립트 또는 포털이 임의의 인터넷 연결 네트워크에서 실행되는 경우 이 가이드는 적용되지 않으며 프라이빗 링크를 사용할 수 없습니다. 이러한 제한은 사용자 브라우저 대신 요청 시 제공되는 원격 Azure 컴퓨터에서 실행되기 때문에 Azure Cloud Shell에서 실행되는 명령에도 적용됩니다.

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>관리 솔루션을 사용하는 경우 지정된 문서를 참조하세요.

이 가이드는 Microsoft에서 관리하는 솔루션에 적용할 수 없습니다. 여기서 자격 증명 모음은 고객 가상 네트워크와 독립적으로 존재하는 Azure 제품에 의해 액세스됩니다. 이러한 시나리오의 예로는 사용하지 않을 때 암호화를 위해 구성된 Azure Storage 또는 Azure SQL, 고객이 제공한 키를 사용하여 데이터를 암호화하는 Azure Event Hubs, 키 자격 증명 모음에 저장된 서비스 자격 증명에 액세스하는 Azure Data Factory, 키 자격 증명 모음에서 비밀을 검색하는 Azure Pipeline 등이 있습니다. 이러한 경우에 *방화벽을 사용하도록 설정한 키 자격 증명 모음을 지원하는 제품인지 확인해야* 합니다. 이 지원은 일반적으로 Key Vault 방화벽의 [신뢰할 수 있는 서비스](overview-vnet-service-endpoints.md#trusted-services) 기능을 통해 수행됩니다. 하지만 다양한 이유로 많은 제품이 신뢰할 수 있는 서비스 목록에 포함되지 않습니다. 이 경우 제품별 지원에 문의하세요.

적은 수의 Azure 제품이 *VNet 삽입* 개념을 지원합니다. 간단히 말해 이 제품은 고객 Virtual Network에 네트워크 디바이스를 추가하여 Virtual Network에 배포된 것처럼 요청을 보낼 수 있습니다. 주목할 만한 예는 [Azure Databricks](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)입니다. 이와 같은 제품은 프라이빗 링크를 사용하여 키 자격 증명 모음 요청을 할 수 있으며, 이 문제 해결 가이드가 도움이 될 수 있습니다.

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. 연결이 승인되었고 성공했는지 확인

다음 단계는 프라이빗 엔드포인트 연결이 승인되고 성공했는지 확인합니다.

1. Azure Portal을 열고 키 자격 증명 모음 리소스를 엽니다.
2. 왼쪽 메뉴에서 **네트워킹** 을 선택합니다.
3. **프라이빗 엔드포인트 연결** 탭을 클릭합니다. 이렇게 하면 모든 프라이빗 엔드포인트 연결 및 해당 상태가 표시됩니다. 연결이 없거나 Virtual Network에 대한 연결이 없는 경우 새 프라이빗 엔드포인트를 만들어야 합니다. 이 내용은 나중에 설명합니다.
4. **프라이빗 엔드포인트 연결** 상태에서 진단 중인 항목을 찾고 “연결 상태"가 **승인됨** 이고 “프로비저닝 상태"가 **성공** 인지 확인합니다.
    - 연결이 “보류 중" 상태인 경우 승인할 수 있습니다.
    - 연결이 “거부됨", “실패함", “오류", “연결 끊김” 또는 기타 상태인 경우 전혀 효과가 없으면 새 프라이빗 엔드포인트 리소스를 만들어야 합니다.

정리된 상태로 유지하기 위해 비효율적인 연결을 삭제하는 것이 좋습니다.

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. 키 자격 증명 모음 방화벽이 제대로 구성되어 있는지 확인

>[!IMPORTANT]
> 방화벽 설정을 변경하면 여전히 프라이빗 링크를 사용하지 않는 합법적인 클라이언트에서 액세스를 제거할 수 있습니다. 방화벽 구성의 각 변경 내용에 대한 의미를 알고 있는지 확인합니다.

중요한 개념은 프라이빗 링크 기능이 데이터 유출을 방지하기 위해 닫힌 Virtual Network의 키 자격 증명 모음에 대한 액세스 권한만 *제공* 한다는 것입니다. 기존 액세스는 *제거* 되지 않습니다. 공용 인터넷에서 액세스를 효과적으로 차단하려면 다음과 같이 키 자격 증명 모음 방화벽을 명시적으로 사용해야 합니다.

1. Azure Portal을 열고 키 자격 증명 모음 리소스를 엽니다.
2. 왼쪽 메뉴에서 **네트워킹** 을 선택합니다.
3. 위에서 **방화벽 및 가상 네트워크** 탭이 선택되어 있는지 확인합니다.
4. **프라이빗 엔드포인트 및 선택한 네트워크** 옵션이 선택되어 있는지 확인합니다. **모든 네트워크** 가 선택된 경우 외부 클라이언트가 키 자격 증명 모음에 여전히 액세스할 수 있는 이유가 여기에 해당합니다.

다음 문은 방화벽 설정에도 적용됩니다.

- 프라이빗 링크 기능에는 키 자격 증명 모음 방화벽 설정에서 "가상 네트워크"를 지정할 필요가 없습니다. 키 자격 증명 모음 방화벽 설정에서 가상 네트워크가 지정되지 않은 경우에도 키 자격 증명 모음의 개인 IP 주소를 사용하는 모든 요청(다음 섹션 참조)이 작동해야 합니다.
- 프라이빗 링크 기능에는 키 자격 증명 모음 방화벽 설정에서 IP 주소를 지정할 필요가 없습니다. 즉, 방화벽 설정에서 IP 주소가 지정되지 않은 경우에도 키 자격 증명 모음의 개인 IP 주소를 사용하는 모든 요청이 작동해야 합니다.

프라이빗 링크를 사용하는 경우 키 자격 증명 모음 방화벽에서 가상 네트워크 또는 IP 주소를 지정하는 유일한 동기는 다음과 같습니다.

- 일부 클라이언트는 프라이빗 링크를 사용하고, 일부는 서비스 엔드포인트를 사용하며, 일부는 공용 IP 주소를 사용하는 하이브리드 시스템이 있습니다.
- 프라이빗 링크로 전환하고 있습니다. 이 경우 모든 클라이언트에서 프라이빗 링크를 사용하고 있는지 확인한 후에는 키 자격 증명 모음 방화벽 설정에서 가상 네트워크 및 IP 주소를 제거해야 합니다.

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. 키 자격 증명 모음에 개인 IP 주소가 있는지 확인

### <a name="difference-between-private-and-public-ip-addresses"></a>개인 및 공용 IP 주소 간의 차이

개인 IP 주소는 항상 다음 형식 중 하나입니다.

- 10.x.x.x: 예: `10.1.2.3`, `10.56.34.12`.
- 172.16.x.x - 172.32.x.x: 예: `172.20.1.1`, `172.31.67.89`.
- 192.168.x.x: 예: `192.168.0.1`, `192.168.100.7`

특정 IP 주소 및 범위는 예약되어 있습니다.

- 224.x.x.x: 멀티캐스트
- 255.255.255.255: 브로드캐스트
- 127.x.x.x: 루프백
- 169.254.x.x: 링크-로컬
- 168.63.129.16: 내부 DNS

다른 모든 IP 주소는 공용입니다.

포털을 찾아보거나 IP 주소를 표시하는 명령을 실행하는 경우 IP 주소가 개인, 공용 또는 예약인지를 식별할 수 있는지 확인합니다. 프라이빗 링크가 작동하려면 키 자격 증명 모음 호스트 이름이 가상 네트워크 주소 공간에 속하는 개인 IP 주소로 확인되어야 합니다.

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>가상 네트워크에서 키 자격 증명 모음 개인 IP 주소 찾기

호스트 이름 확인을 진단해야 하며, 이를 위해서는 프라이빗 링크를 사용하는 키 자격 증명 모음의 정확한 개인 IP 주소를 알아야 합니다. 해당 주소를 찾으려면 다음 절차를 수행합니다.

1. Azure Portal을 열고 키 자격 증명 모음 리소스를 엽니다.
2. 왼쪽 메뉴에서 **네트워킹** 을 선택합니다.
3. **프라이빗 엔드포인트 연결** 탭을 클릭합니다. 이렇게 하면 모든 프라이빗 엔드포인트 연결 및 해당 상태가 표시됩니다.
4. 진단 중인 항목을 찾아 “연결 상태"가 **승인됨** 이며 프로비저닝 상태가 **성공** 인지 확인합니다. 이 내용이 표시되지 않으면 이 문서의 이전 섹션으로 돌아갑니다.
5. 올바른 항목을 찾으면 **프라이빗 엔드포인트** 열의 링크를 클릭합니다. 이렇게 하면 프라이빗 엔드포인트 리소스가 열립니다.
6. 개요 페이지에는 **사용자 지정 DNS 설정** 이라는 섹션이 표시될 수 있습니다. 키 자격 증명 모음 호스트 이름과 일치하는 항목이 하나만 있는지 확인합니다. 이 항목은 키 자격 증명 모음 개인 IP 주소를 표시합니다.
7. **네트워크 인터페이스** 에서 링크를 클릭하고 개인 IP 주소가 이전 단계에 표시된 것과 동일한지 확인할 수도 있습니다. 네트워크 인터페이스는 키 자격 증명 모음을 나타내는 가상 디바이스입니다.

IP 주소는 *동일한 가상 네트워크에서 실행되는* VM 및 기타 디바이스가 키 자격 증명 모음에 연결하는 데 사용할 주소입니다. 추가 조사를 수행하는 동안 IP 주소를 기록하거나 브라우저 탭을 열어 둡니다.

>[!NOTE]
> 키 자격 증명 모음에 여러 프라이빗 엔드포인트가 있는 경우 개인 IP 주소를 여러 개 포함합니다. 이는 각각 고유한 프라이빗 엔드포인트를 통해 동일한 키 자격 증명 모음에 액세스하는 여러 Virtual Network가 있는 경우에만 유용합니다(프라이빗 엔드포인트는 단일 가상 네트워크에 속함). 올바른 Virtual Network에 대한 문제를 진단하고 위의 절차에서 올바른 프라이빗 엔드포인트 연결을 선택했는지 확인합니다. 또한 동일한 가상 네트워크에서 동일한 Key Vault에 대해 여러 프라이빗 엔드포인트를 만들지 **마십시오**. 이는 필요하지 않으며 혼동의 원인이 됩니다.

## <a name="5-validate-the-dns-resolution"></a>5. DNS 확인 유효성 검사

DNS 확인은 키 자격 증명 모음 호스트 이름(예: `fabrikam.vault.azure.net`)을 IP 주소(예: `10.1.2.3`)로 변환하는 프로세스입니다. 다음 하위 섹션에서는 각 시나리오에서 DNS 확인의 예상 결과를 보여줍니다.

### <a name="key-vaults-without-private-link"></a>프라이빗 링크가 없는 키 자격 증명 모음

이 섹션은 학습 목적으로 작성되었습니다. 키 자격 증명 모음에 승인된 상태의 프라이빗 엔드포인트 연결이 없는 경우 호스트 이름을 확인하면 다음과 유사한 결과가 표시됩니다.

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

이름이 공용 IP 주소로 확인되고 `privatelink` 별칭이 없는 것을 볼 수 있습니다. 별칭은 나중에 설명되니 걱정하지 마세요.

위의 결과는 가상 네트워크에 연결된 컴퓨터나 인터넷에 연결된 임의 컴퓨터에 관계없이 예상됩니다. 이는 승인된 상태에서 키 자격 증명 모음에 프라이빗 엔드포인트 연결이 없기 때문에 발생하므로 키 자격 증명 모음에서 프라이빗 링크를 지원할 필요가 없습니다.

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>임의 인터넷 컴퓨터에서 확인되는 프라이빗 링크가 있는 키 자격 증명 모음

키 자격 증명 모음에 하나 이상의 프라이빗 엔드포인트 연결이 승인된 상태에서 인터넷에 연결된 임의 컴퓨터(프라이빗 엔드포인트가 있는 가상 네트워크에 연결되지 *않은* 컴퓨터)에서 호스트 이름을 확인할 경우 다음을 찾을 수 있습니다.

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  52.168.109.101
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
          data-prod-eus.vaultcore.azure.net
          data-prod-eus-region.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101
```

이전 시나리오에서 주목할 만한 차이점은 `{vaultname}.privatelink.vaultcore.azure.net` 값이 포함된 새 별칭이 있다는 것입니다. 즉, 키 자격 증명 모음 데이터 평면이 프라이빗 링크의 요청을 받을 준비가 된 것입니다.

방금 사용한 것과 같은 가상 네트워크 *외부* 의 컴퓨터에서 수행된 요청이 프라이빗 링크를 사용한다는 의미는 아닙니다. 호스트 이름이 여전히 공용 IP 주소로 확인된다는 사실을 알 수 있습니다. *가상 네트워크에 연결* 된 컴퓨터만 프라이빗 링크를 사용할 수 있습니다. 이에 대한 자세한 내용은 다음과 같습니다.

`privatelink` 별칭이 표시되지 않는 경우 키 자격 증명 모음에 `Approved` 상태의 프라이빗 엔드포인트 연결이 없다는 뜻입니다. 다시 시도하기 전에 [이 섹션](#2-confirm-that-the-connection-is-approved-and-succeeded)으로 돌아갑니다.

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>가상 네트워크에서 확인되는 프라이빗 링크가 있는 키 자격 증명 모음

키 자격 증명 모음에 하나 이상의 프라이빗 엔드포인트 연결이 승인된 상태에서 프라이빗 엔드포인트가 생성된 가상 네트워크에 연결된 컴퓨터에서 호스트 이름을 확인할 경우 다음과 같은 응답이 예상됩니다.

Windows:

```console
C:\> nslookup fabrikam.vault.azure.net
```

```output
Non-authoritative answer:
Address:  10.1.2.3
Aliases:  fabrikam.vault.azure.net
          fabrikam.privatelink.vaultcore.azure.net
```

Linux:

```console
joe@MyUbuntu:~$ host fabrikam.vault.azure.net
```

```output
fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3
```

두 가지 주목할 만한 차이점이 있습니다. 첫째, 이름이 개인 IP 주소로 확인됩니다. 이 문서의 [해당 섹션](#find-the-key-vault-private-ip-address-in-the-virtual-network)에서 찾은 IP 주소여야 합니다. 둘째, `privatelink` 뒤에 다른 별칭이 없습니다. 이 문제는 Virtual Network DNS 서버가 별칭 체인을 *가로채고* `fabrikam.privatelink.vaultcore.azure.net`이라는 이름에서 직접 개인 IP 주소를 반환하기 때문에 발생합니다. 이 항목은 실제로 프라이빗 DNS 영역에 있는 `A` 레코드입니다. 이에 대한 자세한 내용은 다음과 같습니다.

>[!NOTE]
> 위의 결과는 프라이빗 엔드포인트가 생성된 Virtual Network에 연결된 가상 머신에서만 발생합니다. 프라이빗 엔드포인트가 포함된 VM을 Virtual Network에 배포하지 않은 경우 VM을 배포하고 원격으로 연결한 다음 `nslookup` 명령(Windows) 또는 위의 `host` 명령(Linux)을 실행합니다.

이러한 명령을 프라이빗 엔드포인트가 생성된 Virtual Network에 연결된 가상 머신에서 실행하지만 이 가상 머신에서 키 자격 증명 모음 개인 IP 주소를 표시하지 **않는** 경우 다음 섹션이 문제를 해결하는 데 도움이 될 수 있습니다.

## <a name="6-validate-the-private-dns-zone"></a>6. 프라이빗 DNS 영역 유효성 검사

이전 섹션에서 설명한 대로 DNS 확인이 작동하지 않는 경우 프라이빗 DNS 영역에 문제가 있을 수 있으며 이 섹션이 도움이 될 수 있습니다. DNS 확인에 올바른 키 자격 증명 모음 개인 IP 주소가 표시되면 프라이빗 DNS 영역이 올바른 것일 수 있습니다. 이 전체 섹션을 건너뛸 수 있습니다.

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>필요한 프라이빗 DNS 영역 리소스가 있는지 확인

Azure 구독에는 다음과 같이 정확한 이름이 있는 [프라이빗 DNS 영역](../../dns/private-dns-privatednszone.md) 리소스가 있어야 합니다.

`privatelink.vaultcore.azure.net`

Portal의 구독 페이지로 이동하고 왼쪽 메뉴에서 "리소스"를 선택하여 이 리소스가 있는지 확인할 수 있습니다. 리소스 이름은 `privatelink.vaultcore.azure.net`이어야 하며 리소스 종류는 **프라이빗 DNS 영역** 이어야 합니다.

일반적으로 이 리소스는 공통적인 절차를 통해 프라이빗 엔드포인트를 만들 때 자동으로 생성됩니다. 하지만 이 리소스가 자동으로 생성되지 않아 수동으로 생성해야 하는 경우가 있습니다. 이 리소스가 실수로 삭제되었을 수도 있습니다.

이 리소스가 없으면 구독에 새 프라이빗 DNS 영역 리소스를 만듭니다. 이름은 공백이나 추가 점 없이 정확히 `privatelink.vaultcore.azure.net`이어야 합니다. 잘못된 이름을 지정하는 경우 이 문서에서 설명한 이름 확인이 작동하지 않습니다. 이 리소스를 만드는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 Azure 프라이빗 DNS 영역 만들기](../../dns/private-dns-getstarted-portal.md)를 참조하세요. 이 페이지를 따라가면 Virtual Network 생성이 생략될 수 있습니다. 이 시점에는 Virtual Network가 이미 있어야 하기 때문입니다. Virtual Machines을 사용하여 검증 절차를 건너뛸 수도 있습니다.

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Virtual Network가 프라이빗 DNS 영역에 연결되어 있는지 확인

프라이빗 DNS 영역이 충분하지 않습니다. 또한 프라이빗 엔드포인트를 포함하는 Virtual Network에 연결되어 있어야 합니다. 프라이빗 DNS 영역이 올바른 가상 네트워크에 연결되지 않은 경우 해당 Virtual Network의 DNS 확인은 프라이빗 DNS 영역을 무시합니다.

프라이빗 DNS 영역 리소스를 열고 왼쪽 메뉴에서 **가상 네트워크 연결** 옵션을 클릭합니다. 이렇게 하면 각각 구독에 Virtual Network의 이름이 있는 링크 목록이 표시됩니다. 프라이빗 엔드포인트 리소스를 포함하는 Virtual Network가 여기에 나열되어야 합니다. 나열되지 않으면 추가합니다. 자세한 단계는 [가상 네트워크 연결](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)을 참조하세요. "자동 등록 사용"을 선택하지 않은 상태로 둘 수 있습니다. 이 기능은 프라이빗 링크와 관련이 없습니다.

프라이빗 DNS 영역이 Virtual Network에 연결되면 Virtual Network에서 시작된 DNS 요청이 프라이빗 DNS 영역에서 이름을 찾습니다. 이는 프라이빗 엔드포인트가 생성된 Virtual Network에 연결된 가상 머신에서 올바른 주소 확인을 수행하는 데 필요합니다.

>[!NOTE]
> 방금 링크를 저장한 경우 Portal에서 작업이 완료되었다고 말하는 경우에도 이 링크를 적용하는 데 시간이 걸릴 수 있습니다. DNS 확인을 테스트하는 데 사용하는 VM을 다시 부팅해야 할 수도 있습니다.

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>프라이빗 DNS 영역에 올바른 A 레코드가 포함되어 있는지 확인합니다.

Portal을 사용하여 이름이 `privatelink.vaultcore.azure.net`인 프라이빗 DNS 영역을 엽니다. 개요 페이지에는 모든 레코드가 표시됩니다. 기본적으로 이름이 `@`이고 형식이 `SOA`인 레코드는 권한의 시작을 의미합니다. 이는 건드리지 마세요.

키 자격 증명 모음 이름 확인이 작동하려면 접미사나 점이 없는 단순 자격 증명 모음 이름을 가진 `A` 레코드가 있어야 합니다. 예를 들어, 호스트 이름이 `fabrikam.vault.azure.net`인 경우 접미사나 점 없이 `fabrikam`이라는 이름의 `A` 레코드가 있어야 합니다.

또한 `A` 레코드 값(IP 주소)은 [키 자격 증명 모음 개인 IP 주소](#find-the-key-vault-private-ip-address-in-the-virtual-network)여야 합니다. `A` 레코드를 찾았지만 잘못된 IP 주소를 포함하는 경우 잘못된 IP 주소를 제거하고 새 주소를 추가해야 합니다. 전체 `A` 레코드를 제거하고 새로 추가하는 것이 좋습니다.

>[!NOTE]
> `A` 레코드를 제거하거나 수정할 때마다 TTL(Time To Live) 값이 아직 만료되지 않았기 때문에 컴퓨터가 여전히 이전 IP 주소로 확인될 수 있습니다. 항상 TTL 값을 10초 이상 600초(10분) 이하로 지정하는 것이 좋습니다. 너무 긴 값을 지정하는 경우 클라이언트가 중단으로부터 복구하는 시간이 오래 걸릴 수 있습니다.

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>둘 이상의 Virtual Network에 대한 DNS 확인

Virtual Network가 여러 개 있고 각각에 동일한 키 자격 증명 모음을 참조하는 프라이빗 엔드포인트 리소스가 있는 경우 키 자격 증명 모음 호스트 이름을 네트워크에 따라 다른 개인 IP 주소로 확인해야 합니다. 즉, 각각 다른 Virtual Network에 연결되고 `A` 레코드의 다른 IP 주소를 사용하는 여러 프라이빗 DNS 영역도 필요합니다.

고급 시나리오에서는 Virtual Network에서 피어링을 사용하도록 설정할 수 있습니다. 이 경우에는 하나의 Virtual Network 프라이빗 엔드포인트 리소스가 필요하지만 둘 다 프라이빗 DNS 영역 리소스에 연결해야 할 수 있습니다. 이 시나리오는 이 문서에서 직접 다루지 않습니다.

### <a name="understand-that-you-have-control-over-dns-resolution"></a>DNS 확인을 제어할 수 있음을 이해합니다.

[이전 섹션](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine)에서 설명한 것처럼 프라이빗 링크가 있는 키 자격 증명 모음의 *공용* 등록에는 별칭 `{vaultname}.privatelink.vaultcore.azure.net`이 있습니다. Virtual Network에서 사용하는 DNS 서버는 공용 등록을 사용하지만 *비공개* 등록에 대한 모든 별칭을 검사하고, 비공개 등록이 발견되면 공용 등록에 정의된 별칭을 따르는 것을 중지합니다.

이 논리는 Virtual Network가 이름이 `privatelink.vaultcore.azure.net`인 프라이빗 DNS 영역에 연결되어 있고 키 자격 증명 모음의 공용 DNS 등록에 `fabrikam.privatelink.vaultcore.azure.net`인 별칭이 있는 경우(키 자격 증명 모음 호스트 이름 접미사가 프라이빗 DNS 영역 이름과 정확히 일치함) *프라이빗 DNS 영역* 에서 DNS 쿼리가 이름이 `fabrikam`인 `A` 레코드를 찾습니다. `A` 레코드가 발견되면 DNS 쿼리에서 해당 IP 주소가 반환되고 공용 DNS 등록에서 추가 조회가 수행되지 않습니다.

여기에서 볼 수 있듯이 이름 확인은 사용자의 관리 하에 있습니다. 이 디자인의 근거는 다음과 같습니다.

- 사용자 지정 DNS 서버와 온-프레미스 네트워크와의 통합을 포함하는 복잡한 시나리오가 있을 수 있습니다. 이 경우 이름을 IP 주소로 변환하는 방법을 제어해야 합니다.
- 프라이빗 링크 없이 키 자격 증명 모음에 액세스해야 할 수 있습니다. 이 경우 Virtual Network에서 호스트 이름을 확인하면 공용 IP 주소를 반환해야 하며, 프라이빗 링크가 없는 키 자격 증명 모음에는 이름 등록에 `privatelink` 별칭이 없기 때문에 이 문제가 발생합니다.

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. 키 자격 증명 모음에 대한 요청이 프라이빗 링크를 사용하는지 확인

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>키 자격 증명 모음의 `/healthstatus` 엔드포인트 쿼리

키 자격 증명 모음은 진단에 사용할 수 있는 `/healthstatus` 엔드포인트를 제공합니다. 응답 헤더에는 키 자격 증명 모음 서비스에 표시되는 원본 IP 주소가 포함됩니다. 다음 명령을 사용하여 해당 엔드포인트를 호출할 수 있습니다(**키 자격 증명 모음 호스트 이름을 사용해야 함**).

Windows(PowerShell):

```powershell
PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers
```

```output
Key                           Value
---                           -----
Pragma                        no-cache
x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
x-ms-keyvault-service-version 1.2.27.0
x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security     max-age=31536000;includeSubDomains
Content-Length                4
Cache-Control                 no-cache
Content-Type                  application/json; charset=utf-8
```

Linux 또는 `curl`을 포함하는 Windows 10 최신 버전:

```console
joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
```

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: application/json; charset=utf-8
x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
x-ms-keyvault-service-version: 1.2.27.0
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
Strict-Transport-Security: max-age=31536000;includeSubDomains
Content-Length: 4
```

이와 유사한 출력을 얻지 못하거나 네트워크 오류가 발생하면 지정한 호스트 이름을 통해 키 자격 증명 모음에 액세스할 수 없다는 의미입니다(예: `fabrikam.vault.azure.net`). 호스트 이름이 올바른 IP 주소로 확인되지 않거나 전송 계층에 연결 문제가 있습니다. 이러한 문제는 라우팅 문제, 패키지 삭제 및 기타 이유로 인해 발생할 수 있습니다. 자세히 조사해야 합니다.

응답은 `x-ms-keyvault-network-info` 헤더를 포함해야 합니다.

```console
x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
```

`x-ms-keyvault-network-info` 헤더의 `addr` 필드는 요청 원본의 IP 주소를 표시합니다. 이 IP 주소는 다음 중 하나일 수 있습니다.

- 요청을 수행하는 컴퓨터의 개인 IP 주소. 이는 요청이 프라이빗 링크 또는 서비스 엔드포인트를 사용하고 있음을 나타냅니다. 이는 프라이빗 링크에 예상되는 결과입니다.
- 요청을 수행하는 컴퓨터가 *아닌* 일부 다른 개인 IP 주소. 이는 일부 사용자 지정 라우팅이 적용됨을 나타냅니다. 여전히 요청이 프라이빗 링크 또는 서비스 엔드포인트를 사용하고 있음을 나타냅니다.
- 일부 공용 IP 주소. 이는 요청이 게이트웨이(NAT) 디바이스를 통해 인터넷으로 라우팅되고 있음을 나타냅니다. 이는 요청이 프라이빗 링크를 사용하지 않고 일부 문제를 해결해야 함을 나타냅니다. 일반적인 이유는 1) 프라이빗 엔드포인트가 승인 및 성공 상태가 아니며, 2) 호스트 이름이 키 자격 증명 모음의 개인 IP 주소로 확인되지 않기 때문입니다. 이 문서에는 두 경우 모두에 대한 문제 해결 작업이 포함되어 있습니다.

>[!NOTE]
> `/healthstatus`에 대한 요청이 작동하지만 `x-ms-keyvault-network-info` 헤더가 없는 경우 엔드포인트가 키 자격 증명 모음에 의해 제공되지 않을 수 있습니다. 위의 명령은 HTTPS 인증서의 유효성도 검사하므로 누락된 헤더는 변조되었음을 나타내는 징후일 수 있습니다.

### <a name="query-the-key-vault-ip-address-directly"></a>키 자격 증명 모음 IP 주소 직접 쿼리

>[!IMPORTANT]
> HTTPS 인증서 유효성 검사 없이 키 자격 증명 모음에 액세스하는 것은 위험하며 학습 목적으로만 사용할 수 있습니다. 프로덕션 코드는 이 클라이언트 측 유효성 검사 없이 키 자격 증명 모음에 액세스해서는 안 됩니다. 문제를 진단하는 데 그치는 경우에도 키 자격 증명 모음에 대한 요청에서 HTTPS 인증서 검증을 자주 사용하지 않도록 설정하면 드러나지 않는 변조 시도가 발생할 수 있습니다.

최신 버전의 PowerShell을 설치한 경우 `-SkipCertificateCheck`를 사용하여 HTTPS 인증서 확인을 건너 뛴 다음 [키 자격 증명 모음 IP 주소](#find-the-key-vault-private-ip-address-in-the-virtual-network)를 직접 대상으로 지정할 수 있습니다.

```powershell
PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers
```

`curl`을 사용하는 경우 `-k` 인수와 동일한 작업을 수행할 수 있습니다.

```console
joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus
```

응답은 이전 섹션과 동일해야 합니다. 즉, 동일한 값을 가진 `x-ms-keyvault-network-info` 헤더를 포함해야 합니다. `/healthstatus` 엔드포인트는 키 자격 증명 모음 또는 IP 주소를 사용하는지 여부가 중요하지 않습니다.

`x-ms-keyvault-network-info`가 키 자격 증명 모음 호스트 이름을 사용하여 요청에 대한 값을 반환하고 IP 주소를 사용하는 요청에 대한 값을 반환하는 경우, 각 요청은 다른 엔드포인트를 대상으로 합니다. 이전 섹션에서 `x-ms-keyvault-network-info`의 `addr` 필드에 대한 설명을 참조하여 어떤 사례가 잘못되었으며 수정이 필요한지 판단할 수 있습니다.

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. 영향을 미치는 기타 변경사항 및 사용자 지정

다음 항목은 포괄적이지 않은 조사 작업입니다. 이 작업은 추가 문제를 찾을 수 있는 위치를 알려 주지만, 이러한 시나리오에서 문제를 해결하려면 고급 네트워크 지식이 있어야 합니다.

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>Virtual Network에서 사용자 지정 DNS 서버 진단

Portal에서 Virtual Network 리소스를 엽니다. 왼쪽 메뉴에서 **DNS 서버** 를 엽니다. "사용자 지정"을 사용하는 경우 DNS 확인이 이 문서에 설명된 것과 다를 수 있습니다. DNS 서버에서 키 자격 증명 모음 호스트 이름을 확인하는 방법을 진단해야 합니다.

Azure에서 제공하는 기본 DNS 서버를 사용하는 경우 이 문서 전체를 적용할 수 있습니다.

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>가상 머신에서 호스트를 재정의하거나 사용자 지정 DNS 서버를 진단합니다.

대부분의 운영 체제에서는 일반적으로 `hosts` 파일을 변경하여 호스트 이름에 따라 명시적인 고정 IP 주소를 설정할 수 있습니다. DNS 서버를 재정의하도록 허용할 수도 있습니다. 이러한 시나리오 중 하나를 사용하는 경우 시스템 관련 진단 옵션을 진행합니다.

### <a name="promiscuous-proxies-fiddler-etc"></a>무차별 프록시(Fiddler 등)

명시적으로 언급한 경우를 제외하고 이 문서의 진단 옵션은 환경에 무차별 프록시가 없는 경우에만 작동합니다. 이러한 프록시는 진단 중인 시스템에만 설치되는 경우가 많지만(Fidler가 가장 일반적인 예), 고급 관리자는 루트 CA(인증 기관)를 덮어쓰고 네트워크의 여러 컴퓨터를 서비스하는 게이트웨이 디바이스에 무차별 프록시를 설치할 수 있습니다. 이러한 프록시는 보안과 안정성에 상당한 영향을 줄 수 있습니다. Microsoft는 이러한 제품을 사용하는 구성을 지원하지 않습니다.

### <a name="other-things-that-may-affect-connectivity"></a>연결에 영향을 줄 수 있는 기타 항목

다음은 고급 또는 복잡한 시나리오에서 찾을 수 있는 항목에 대한 포괄적이지 않은 목록입니다.

- 방화벽 설정. Virtual Network에 연결된 Azure Firewall 또는 Virtual Network 또는 컴퓨터에 배포된 사용자 지정 방화벽 솔루션.
- 네트워크 피어링. 사용되는 DNS 서버 및 트래픽이 라우팅되는 방식에 영향을 줄 수 있습니다.
- 사용자 지정 게이트웨이(NT) 솔루션. DNS 쿼리에서의 트래픽을 포함하여 트래픽이 라우팅되는 방식에 영향을 줄 수 있습니다.
