---
title: Azure Cloud Shell에서 사용자 토큰 가져오기
description: Azure Cloud Shell에서 인증 된 사용자에 대 한 토큰을 획득 하는 방법
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/29/2021
ms.openlocfilehash: 117fa3672c78de29cd88797add83fa6e3bf2bf79
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362734"
---
# <a name="acquire-a-token-in-azure-cloud-shell"></a>Azure Cloud Shell에서 토큰 획득

Azure Cloud Shell Azure Portal에 로그인 한 사용자를 자동으로 인증 하는 끝점을 제공 합니다. 이 끝점을 사용 하 여 Azure 서비스와 상호 작용 하는 액세스 토큰을 가져옵니다.

## <a name="authenticating-in-the-cloud-shell"></a>Cloud Shell에서 인증
Azure Cloud Shell에는 브라우저와 상호 작용 하 여 자동으로 로그인 하는 고유한 끝점이 있습니다. 이 끝점은 요청을 받으면 요청을 브라우저에 다시 보냅니다. 그러면 부모 포털 프레임으로 전달 됩니다. 포털 창에서 Azure Active Directory에 대 한 요청이 수행 되 고 결과 토큰이 반환 됩니다.

다른 자격 증명으로 인증 하려는 경우 또는를 사용 하 여이 작업을 수행할 수 있습니다. `az login``Connect-AzAccount`

## <a name="acquire-and-use-access-token-in-cloud-shell"></a>Cloud Shell에서 액세스 토큰 가져오기 및 사용

### <a name="acquire-token"></a>토큰 획득

다음 명령을 실행 하 여 사용자 액세스 토큰을 환경 변수로 설정 `access_token` 합니다.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The access token is $access_token
```

### <a name="use-token"></a>토큰 사용

다음 명령을 실행 하 여 이전 단계에서 얻은 토큰을 사용 하 여 계정의 모든 Virtual Machines 목록을 가져옵니다.

```
curl https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Compute/virtualMachines?api-version=2021-07-01 -H "Authorization: Bearer $access_token" -H "x-ms-version: 2019-02-02"
```

## <a name="handling-token-expiration"></a>토큰 만료 처리

로컬 인증 끝점은 토큰을 캐시 합니다. 원하는 만큼 자주 호출할 수 있으며, Azure Active Directory에 대 한 인증 호출은 캐시에 저장 된 토큰이 없거나 토큰이 만료 된 경우에만 수행 됩니다.

## <a name="limitations"></a>제한 사항
- 에 대해 Cloud Shell 토큰을 제공할 수 있는 리소스의 allowlist 있습니다. 명령을 실행 하 고와 유사한 메시지를 수신 하는 경우 `"error":{"code":"AudienceNotSupported","message":"Audience https://newservice.azure.com/ is not a supported MSI token audience...."}` 이 제한을 통해 제공 됩니다. [GitHub](https://github.com/Azure/CloudShell/issues) 에서이 서비스를 allowlist에 추가 하도록 요청 하는 문제를 파일에 추가할 수 있습니다.
- 명령을 사용 하 여 명시적으로 로그인 하는 경우에는 `az login` 브라우저를 실행 하는 컴퓨터가 아닌 Cloud Shell 컨테이너를 기반으로 하는 모든 조건부 액세스 규칙이 평가 됩니다. Cloud Shell 컨테이너는 이러한 정책에 대 한 관리 되는 장치로 계산 되지 않으므로 권한이 정책에 의해 제한 될 수 있습니다.
- Azure 관리 되는 Id는 Azure Cloud Shell에서 사용할 수 없습니다. [Azure 관리 되는 id에 대해 자세히](../active-directory/managed-identities-azure-resources/overview.md)알아보세요.
