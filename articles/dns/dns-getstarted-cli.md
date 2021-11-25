---
title: '빠른 시작: Azure DNS 영역 및 레코드 만들기 - Azure CLI'
titleSuffix: Azure DNS
description: 빠른 시작 - Azure DNS에 DNS 영역 및 레코드를 만드는 방법을 알아봅니다. Azure CLI를 사용하여 첫 번째 DNS 영역 및 레코드를 만들고 관리하는 단계별 가이드입니다.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.custom: devx-track-azurecli, mode-api
ms.openlocfilehash: 99212b68d6c957ea366c9436dd6b20c95e26d689
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133050720"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure DNS 영역 및 레코드 만들기

이 문서에서는 Windows, Mac 및 Linux에서 사용할 수 있는 Azure CLI를 사용하여 첫 번째 DNS 영역 및 레코드를 만드는 단계를 안내합니다. [Azure Portal](dns-getstarted-portal.md)이나 [Azure PowerShell](dns-getstarted-powershell.md)을 사용하여 이 단계를 수행할 수도 있습니다.

DNS 영역은 특정 도메인에 대한 DNS 레코드를 호스트하는 데 사용됩니다. Azure DNS에서 도메인 호스팅을 시작하려면 해당 도메인 이름의 DNS 영역을 만들어야 합니다. 그러면 이 DNS 영역 안에 도메인의 각 DNS 레코드가 생성됩니다. 마지막으로 DNS 영역을 인터넷에 게시하려면 도메인에 대한 이름 서버를 구성해야 합니다. 아래에서는 이러한 각 단계에 대해 설명합니다.

:::image type="content" source="media/dns-getstarted-portal/environment-diagram.png" alt-text="Azure Portal을 사용하는 DNS 배포 환경의 다이어그램." border="false":::

Azure DNS는 프라이빗 DNS 영역도 지원합니다. 프라이빗 DNS 영역에 대해 자세히 알아보려면 [프라이빗 도메인에 Azure DNS 사용](private-dns-overview.md)을 참조하세요. 프라이빗 DNS 영역을 만드는 방법에 대한 예제는 [CLI를 사용하여 Azure DNS 프라이빗 영역 시작](./private-dns-getstarted-cli.md)을 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

DNS 영역을 만들기 전에 DNS 영역을 포함할 리소스 그룹을 만듭니다.

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>DNS 영역 만들기

`az network dns zone create` 명령을 사용하여 DNS 영역을 만듭니다. 이 명령에 대한 도움말을 보려면 `az network dns zone create -h`을 입력합니다.

다음 예제에서는 *contoso.xyz* 라는 DNS 영역을 리소스 그룹 *MyResourceGroup* 에 만듭니다. 예제를 사용하여 DNS 영역을 만들고 사용자 고유 값으로 대체합니다.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>DNS 레코드 만들기

DNS 레코드를 만들려면 `az network dns record-set [record type] add-record` 명령을 사용합니다. A 레코드에 대한 도움말은 `azure network dns record-set A add-record -h`를 참조하세요.

다음 예제에서는 "MyResourceGroup" 리소스 그룹의 "contoso.xyz" DNS 영역에 상대적 이름이 "www"인 레코드를 만듭니다. 레코드의 정규화된 이름은 "www.contoso.xyz"입니다. 레코드 종류는 "A"이고, IP 주소가 "10.10.10.10"이고, 기본 TTL이 3600초(1시간)입니다.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>레코드 보기

사용자 영역에 DNS 레코드를 나열하려면 다음을 실행하세요.

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>이름 확인 테스트

이제 테스트 'A' 레코드가 포함된 테스트 DNS 영역이 있으므로 *nslookup* 이라는 도구를 사용하여 이름 확인을 테스트할 수 있습니다. 

**DNS 이름 확인을 테스트하려면**

1. 다음 cmdlet을 실행하여 영역에 대한 이름 서버 목록을 가져옵니다.

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. 이전 단계의 출력에서 이름 서버 이름 중 하나를 복사합니다.

1. 명령 프롬프트를 열고 다음 명령을 실행합니다.

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   다음은 그 예입니다.

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   다음 화면과 유사한 출력이 표시됩니다.

   ![스크린샷은 n s 조회 명령과 서버, 주소, 이름 및 주소에 대한 값을 포함하는 명령 프롬프트 창을 보여줍니다.](media/dns-getstarted-portal/nslookup.PNG)

호스트 이름 **www\.contoso.xyz** 는 구성한 대로 **10.10.10.10** 으로 확인됩니다. 이 결과는 이름 확인이 올바르게 작동하는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹을 삭제하면 이 빠른 시작에서 만든 모든 리소스를 삭제할 수 있습니다.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

Azure CLI를 사용하여 첫 번째 DNS 영역과 레코드를 만들었으면 사용자 지정 도메인에서 웹앱의 레코드를 만들 수 있습니다.

> [!div class="nextstepaction"]
> [사용자 지정 도메인에서 웹앱에 대한 DNS 레코드 만들기](./dns-web-sites-custom-domain.md)
