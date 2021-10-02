---
title: 레거시 Azure DNS Private Zones를 새 리소스 모델로 마이그레이션
titleSuffix: Azure DNS
description: 이 가이드에서는 기존의 프라이빗 DNS 영역을 최신 리소스 모델로 마이그레이션하는 방법에 대한 단계별 지침을 제공합니다.
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: 566608da26cafab0d491663300136ebc815eb3ff
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357089"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>기존 Azure DNS Private Zones를 새 리소스 모델로 마이그레이션

공개 미리 보기 중에 “zoneType” 속성이 “Private”으로 설정된 “dnszones” 리소스를 사용하여 프라이빗 DNS 영역을 만들었습니다. 이러한 영역은 2019년 12월 31일 이후에는 지원되지 않으며 “dnszones” 대신 “privateDnsZones” 리소스 종류를 사용하는 GA 리소스 모델로 마이그레이션해야 합니다. 마이그레이션 프로세스는 간단하며, 이 프로세스를 자동화하는 PowerShell 스크립트가 제공됩니다. 이 가이드에서는 Azure DNS 프라이빗 영역을 새 리소스 모델로 마이그레이션하는 단계별 지침을 제공합니다.

마이그레이션이 필요한 dnszones 리소스를 확인하려면 Azure CLI에서 아래 명령을 실행합니다.

```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>필수 구성 요소

최신 버전의 Azure PowerShell을 설치했는지 확인합니다. Azure PowerShell(Az) 및 설치 방법에 대한 자세한 내용은 [Azure Az PowerShell 모듈 소개를 방문하세요.](/powershell/azure/new-azureps-module-az)

Azure PowerShell용 Az.PrivateDns 모듈을 설치했는지 확인합니다. 이 모듈을 설치하려면 관리자 권한으로 실행되는 PowerShell 창(관리 모드)을 열고 다음 명령을 입력합니다.

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>마이그레이션 프로세스는 완전히 자동화되어 있으며 가동 중지 시간을 유발하지 않습니다. 그러나 중요한 프로덕션 환경에서 Azure DNS 프라이빗 영역(미리 보기)를 사용 중인 경우에는 계획된 유지 관리 기간 동안 다음 마이그레이션 프로세스를 실행해야 합니다. 마이그레이션 스크립트를 실행 중일 때는 프라이빗 DNS 영역의 구성이나 레코드 세트를 수정하지 않도록 하세요.

## <a name="installing-the-script"></a>스크립트 설치

관리자 권한으로 실행되는 PowerShell 창(관리 모드)을 열고 다음 명령을 실행합니다.

```powershell
install-script PrivateDnsMigrationScript
```

스크립트를 설치하라는 메시지가 표시되면 “A”를 입력합니다.

![스크립트 설치](./media/private-dns-migration-guide/install-migration-script.png)

[https://www.powershellgallery.com/packages/PrivateDnsMigrationScript](https://www.powershellgallery.com/packages/PrivateDnsMigrationScript) 에서 최신 버전의 PowerShell 스크립트를 수동으로 받을 수 있습니다.

>[!IMPORTANT]
>마이그레이션 스크립트는 Azure 클라우드 셸에서 실행해서는 안 되며, 인터넷에 연결된 VM 또는 로컬 머신에서 실행해야 합니다.

## <a name="running-the-script"></a>스크립트 실행

다음 명령을 사용하여 스크립트를 실행합니다.

```powershell
PrivateDnsMigrationScript.ps1
```

![스크립트 실행](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>구독 ID를 입력하고 Azure에 로그인

마이그레이션하려는 프라이빗 DNS 영역을 포함하는 구독 ID를 입력하라는 메시지가 표시됩니다. Azure 계정에 로그인해야 합니다. 스크립트가 구독의 DNS Private Zones 리소스에 액세스할 수 있도록 로그인을 완료합니다.

![Azure에 로그인](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>마이그레이션할 DNS 영역 선택

스크립트가 구독의 모든 프라이빗 DNS 영역 목록을 가져오고 마이그레이션하려는 항목을 확인하라는 메시지가 표시됩니다. 모든 프라이빗 DNS 영역을 마이그레이션하려면 “A”를 입력합니다. 이 단계를 수행하면 스크립트가 새 리소스 모델을 사용하여 새로운 프라이빗 DNS 영역을 만들고 새로운 DSN 영역에 데이터를 복사합니다. 이 단계는 어떤 식으로든 기존의 프라이빗 DNS 영역을 변경하지 않습니다.

![DNS 영역 선택](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>DNS 확인을 새로운 DNS 영역으로 전환

영역 및 레코드가 새 리소스 모델에 복사되면 스크립트가 DNS 확인을 새로운 DNS 영역으로 전환하라는 메시지를 표시합니다. 이 단계는 기존의 프라이빗 DNS 영역 및 가상 네트워크 간의 연결을 제거합니다. 기존 영역이 가상 네트워크와 연결이 끊기면 위의 단계에서 생성된 새로운 DNS 영역이 이러한 가상 네트워크의 DNS 확인을 자동으로 인계받습니다.

모든 가상 네트워크의 DNS 확인을 전환하려면 ‘A’를 선택합니다.

![이름 확인 전환](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>DNS 확인이 작동하는지 확인

계속하기 전에 DNS 영역에서 DNS 확인이 제대로 작동하는지 확인합니다. Azure VM에 로그인하고 마이그레이션된 영역을 대상으로 nslookup 쿼리를 실행하여 DNS 확인이 작동하는지 확인할 수 있습니다.

![이름 확인이 작동하는지 확인](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

DNS 쿼리가 확인되지 않는다면 몇 분 정도 기다린 후 쿼리를 다시 시도해 보세요. DNS 쿼리가 제대로 작동하는 경우 스크립트가 프라이빗 DNS 영역에서 가상 네트워크를 제거하라는 메시지를 표시하면 ‘Y’를 입력합니다.

![이름 확인이 작동하는지 확인](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>어떠한 이유로 마이그레이션된 영역에 대한 DNS 확인이 제대로 작동하지 않을 경우 위의 단계에서 ‘N’을 입력하면 스크립트가 DNS 확인을 다시 기존 영역으로 전환합니다. 지원 티켓을 만들면 DNS 영역의 마이그레이션에 대한 지원을 받을 수 있습니다.

## <a name="cleanup"></a>정리

이 단계는 기존 DNS 영역을 삭제하며, 사용자가 DNS 확인이 제대로 작동하는지 확인한 후에만 실행해야 합니다. 각 프라이빗 DNS 영역을 삭제하라는 메시지가 표시됩니다. 해당 영역의 DNS 확인이 제대로 작동하는지 확인한 후에는 메시지가 표시될 때마다 ‘Y’를 입력합니다.

![정리](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>자동화 업데이트

SDK를 사용하여 개발된 템플릿, PowerShell 스크립트 또는 사용자 지정 코드를 포함한 자동화를 사용하는 경우 프라이빗 DNS 영역의 새 리소스 모델을 사용하도록 자동화를 업데이트해야 합니다. 다음은 새로운 프라이빗 DNS CLI/PS/SDK 설명서에 대한 링크입니다.
* [Azure DNS Private Zones REST API](/rest/api/dns/privatedns/privatezones)
* [Azure DNS Private Zones CLI](/cli/azure/network/private-dns/link/vnet)
* [Azure DNS Private Zones PowerShell](/powershell/module/az.privatedns/)
* [Azure DNS Private Zones SDK](/dotnet/api/overview/azure/privatedns/management)

## <a name="need-further-help"></a>추가 도움이 필요한 경우

마이그레이션 프로세스와 관련하여 추가로 도움이 필요한 경우 또는 어떠한 이유로 위에 나열된 단계가 도움이 되지 않는 경우에는 지원 티켓을 만듭니다. PowerShell 스크립트가 생성한 음성 텍스트 파일을 지원 티켓에 포함하세요.

## <a name="next-steps"></a>다음 단계

* [Azure PowerShell](./private-dns-getstarted-powershell.md) 또는 [Azure CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 프라이빗 영역을 만드는 방법을 알아봅니다.

* Azure DNS에서 프라이빗 영역으로 실현할 수 있는 몇 가지 일반 [프라이빗 영역 시나리오](./private-dns-scenarios.md)에 대해 읽어보세요.

* 특정 종류의 작업에 필요한 특정 동작을 포함하여 Azure DNS의 프라이빗 영역에 대한 일반적인 질문과 대답은 [프라이빗 DNS FAQ](./dns-faq-private.yml)를 참조하세요.

* [DNS 영역 및 레코드 개요](dns-zones-records.md)를 참조하여 DNS 영역 및 레코드에 대해 알아봅니다.

* Azure의 몇 가지 다른 주요 [네트워킹 기능](../networking/fundamentals/networking-overview.md)을 알아봅니다.