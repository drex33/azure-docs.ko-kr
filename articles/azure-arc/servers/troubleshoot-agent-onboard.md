---
title: Azure Arc 지원 서버 에이전트 연결 문제 해결
description: 이 문서에서는 서비스에 연결할 때 Azure Arc 지원 서버에서 발생하는 Connected Machine 에이전트 관련 문제를 해결하는 방법을 설명합니다.
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 69f1556a9e59b748e4a415d76643bee0f3122e9e
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132871094"
---
# <a name="troubleshoot-azure-arc-enabled-servers-agent-connection-issues"></a>Azure Arc 지원 서버 에이전트 연결 문제 해결

이 문서에서는 Windows 또는 Linux용 Azure Connected Machine 에이전트를 구성하는 동안 발생할 수 있는 문제를 해결하고 해결하는 방법을 제공합니다. 서비스 연결을 구성하는 경우의 대화형 및 대규모 설치 방법이 모두 포함되어 있습니다. 일반 정보는 [Azure Arc 지원 서버 개요를 참조하세요.](./overview.md)

## <a name="agent-error-codes"></a>에이전트 오류 코드

Azure Arc 지원 서버 에이전트를 구성할 때 오류가 발생하는 경우 다음 표는 문제를 해결하기 위해 가능한 원인과 제안된 단계를 확인하는 데 도움이 될 수 있습니다. 계속하려면 콘솔 또는 스크립트 출력에 출력된 `AZCM0000`("0000"은 4자리 숫자일 수 있음) 오류 코드가 필요합니다.

| 오류 코드 | 가능한 원인: | 제안된 수정 사항 |
|------------|----------------|-----------------------|
| AZCM0000 | 작업이 성공적으로 완료되었습니다. | 해당 없음 |
| AZCM0001 | 알 수 없는 오류가 발생했습니다. | 추가 지원이 필요하면 Microsoft 지원에 문의합니다. |
| AZCM0011 | 사용자가 작업을 취소했습니다(CTRL+C). | 이전 명령을 다시 시도합니다. |
| AZCM0012 | 제공된 액세스 토큰이 잘못되었습니다. | 새 액세스 토큰을 얻고 다시 시도합니다. |
| AZCM0013 | 제공된 태그가 잘못되었습니다. | 태그를 쉼표로 구분하여 큰따옴표로 묶고, 공백이 있는 이름 또는 값을 작은따옴표로 묶었는지 확인합니다(`--tags "SingleName='Value with spaces',Location=Redmond"`).
| AZCM0014 | 클라우드가 잘못되었습니다. | 지원되는 클라우드(`AzureCloud` 또는 `AzureUSGovernment`)를 지정합니다. |
| AZCM0015 | 지정된 상관 관계 ID가 올바른 GUID가 아닙니다. | 올바른 GUID를 `--correlation-id`에 제공합니다. |
| AZCM0016 | 필수 매개 변수가 없습니다. | 출력을 검토하여 누락된 매개 변수를 확인합니다. |
| AZCM0017 | 리소스 이름이 잘못되었습니다. | 영숫자, 하이픈 및/또는 밑줄만 사용하는 이름을 지정합니다. 이름은 하이픈 또는 밑줄로 끝날 수 없습니다. |
| AZCM0018 | 명령이 관리자 권한을 사용하지 않고 실행되었습니다. | 관리자 권한 명령 프롬프트 또는 콘솔 세션에서 관리자 또는 루트 권한으로 명령을 다시 시도합니다. |
| AZCM0041 | 제공된 자격 증명이 잘못되었습니다. | 디바이스 로그인의 경우 서버 리소스가 만들어질 테넌트 및 구독에 대한 액세스 권한이 지정된 사용자 계정에 있는지 확인합니다. 서비스 주체 로그인의 경우 클라이언트 ID와 비밀이 정확한지 확인하고, 비밀 만료 날짜를 확인하고, 서버 리소스를 만들 동일한 테넌트에서 서비스 주체가 제공되었는지 확인합니다. |
| AZCM0042 | Azure Arc 사용 가능한 서버 리소스를 만들 수 없습니다. | 지정된 사용자/서비스 주체가 지정된 리소스 그룹에 Azure Arc 사용 가능한 서버 리소스를 만들 수 있는 액세스 권한이 있는지 확인합니다. |
| AZCM0043 | Azure Arc 사용 가능한 서버 리소스를 삭제하지 못했습니다. | 지정된 사용자/서비스 주체가 지정된 리소스 그룹에서 Azure Arc 사용 가능한 서버 리소스를 삭제할 수 있는 액세스 권한이 있는지 확인합니다. 리소스가 더 이상 Azure에 없으면 `--force-local-only` 플래그를 사용하여 계속 진행합니다. |
| AZCM0044 | 이름이 같은 리소스가 이미 있습니다. | 매개 변수에 다른 이름을 `--resource-name` 지정하거나 Azure에서 기존 Azure Arc 지원 서버를 삭제하고 다시 시도합니다. |
| AZCM0061 | 에이전트 서비스에 연결할 수 없습니다. | 관리자 권한 사용자 컨텍스트(관리자/루트)에서 명령을 실행하고 HIMDS 서비스가 서버에서 실행되는지 확인합니다. |
| AZCM0062 | 서버를 연결하는 동안 오류가 발생했습니다. | 출력에서 다른 오류 코드를 검토하여 더 구체적인 정보를 확인합니다. Azure 리소스를 만든 후에 오류가 발생한 경우 먼저 리소스 그룹에서 Arc 서버를 삭제한 후에 다시 시도해야 합니다. |
| AZCM0063 | 서버 연결을 끊는 동안 오류가 발생했습니다. | 출력에서 다른 오류 코드를 검토하여 더 구체적인 정보를 확인합니다. 이 오류가 계속 발생하면 Azure에서 리소스를 삭제한 다음, 서버에서 `azcmagent disconnect --force-local-only`를 실행하여 에이전트의 연결을 끊을 수 있습니다. |
| AZCM0064 | 에이전트 서비스에서 응답하지 않습니다. | `himds` 서비스의 상태를 확인하여 서비스가 실행되고 있는지 확인합니다. 실행되고 있지 않으면 서비스를 시작합니다. 실행되고 있으면 잠시 기다린 후 다시 시도합니다. |
| AZCM0065 | 내부 에이전트 통신 오류가 발생했습니다. | 지원이 필요하면 Microsoft 지원에 문의합니다. |
| AZCM0066 | 에이전트 웹 서비스가 응답하지 않거나 사용할 수 없습니다. | 지원이 필요하면 Microsoft 지원에 문의합니다. |
| AZCM0067 | 에이전트가 이미 Azure에 연결되어 있습니다. | 먼저 [에이전트 연결 끊기](manage-agent.md#unregister-machine)의 단계를 수행한 다음, 다시 시도합니다. |
| AZCM0068 | Azure에서 서버 연결을 끊는 동안 내부 오류가 발생했습니다. | 지원이 필요하면 Microsoft 지원에 문의합니다. |
| AZCM0081 | Azure Active Directory 관리 ID 인증서를 다운로드하는 동안 오류가 발생했습니다. | 서버를 Azure에 연결하려고 시도하는 동안 이 메시지가 표시되면 에이전트에서 Azure Arc 서비스와 통신할 수 없습니다. Azure에서 리소스를 삭제하고 연결을 다시 시도합니다. |
| AZCM0101 | 명령이 구문 분석되지 않았습니다. | `azcmagent <command> --help`를 실행하여 올바른 명령 구문을 검토합니다. |
| AZCM0102 | 컴퓨터 호스트 이름을 검색할 수 없습니다. | `hostname`을 실행하여 시스템 수준 오류 메시지를 확인한 다음, Microsoft 지원에 문의합니다. |
| AZCM0103 | RSA 키를 생성하는 동안 오류가 발생했습니다. | 지원이 필요하면 Microsoft 지원에 문의합니다. |
| AZCM0104 | 시스템 정보를 읽지 못했습니다. | 시스템에 대한 관리자/루트 권한이 `azcmagent`를 실행하는 데 사용되는 ID에 있는지 확인하고 다시 시도합니다. |

## <a name="agent-verbose-log"></a>에이전트 자세한 정보 로그

이 문서의 뒷부분에 설명된 문제 해결 단계를 수행하기 전에 필요한 최소 정보는 자세한 정보 로그입니다. 이 로그에는 자세한 정보(-v) 인수를 사용한 경우의 **azcmagent** 도구 명령 출력이 포함됩니다. 로그 파일은 Windows의 경우 `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log`, Linux의 경우 `/var/opt/azcmagent/log/azcmagent.log`에 기록됩니다.

### <a name="windows"></a>Windows

다음은 대화형 설치를 수행할 때 Windows용 Connected Machine 에이전트에서 자세한 정보 로깅을 사용하도록 설정하는 명령의 예입니다.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

다음은 서비스 주체를 사용하여 대규모 설치를 수행할 때 Windows용 Connected Machine 에이전트에서 자세한 정보 로깅을 사용하도록 설정하는 명령의 예입니다.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

다음은 대화형 설치를 수행할 때 Linux용 Connected Machine 에이전트에서 자세한 정보 로깅을 사용하도록 설정하는 명령의 예입니다.

>[!NOTE]
>**azcmagent** 를 실행하려면 Linux 머신에 대한 ‘루트’ 액세스 권한이 있어야 합니다.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

다음은 서비스 주체를 사용하여 대규모 설치를 수행할 때 Linux용 Connected Machine 에이전트에서 자세한 정보 로깅을 사용하도록 설정하는 명령의 예입니다.

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>서비스에 대한 에이전트 연결 이슈

다음 표에는 몇 가지 알려진 오류 및 문제 해결 방법에 대한 제안 사항이 나와 있습니다.

|메시지 |오류 |가능한 원인: |솔루션 |
|--------|------|---------------|---------|
|권한 부여 토큰 디바이스 흐름을 가져오지 못함 |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |`login.windows.net` 엔드포인트에 연결할 수 없음 | 엔드포인트 연결을 확인합니다. |
|권한 부여 토큰 디바이스 흐름을 가져오지 못함 |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |프록시 또는 방화벽에서 `login.windows.net` 엔드포인트에 대한 액세스를 차단하고 있습니다. | 엔드포인트 연결을 확인하고 방화벽 또는 프록시 서버에서 차단되지 않았는지 확인합니다. |
|권한 부여 토큰 디바이스 흐름을 가져오지 못함  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | 그룹 정책 개체 ‘컴퓨터 구성\ 관리 템플릿\ 사용자 프로필\ 시스템 다시 시작 시 지정된 기간(일)보다 오래된 사용자 프로필 삭제’가 사용하도록 설정되었습니다. | GPO가 사용하도록 설정되었으며 영향을 받는 머신을 대상으로 하는지 확인합니다. 자세한 내용은 각주 <sup>[1](#footnote1)</sup>을 참조하세요. |
|SPN에서 권한 부여 토큰을 가져오지 못함 |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |프록시 또는 방화벽에서 `login.windows.net` 엔드포인트에 대한 액세스를 차단하고 있습니다. |엔드포인트 연결을 확인하고 방화벽 또는 프록시 서버에서 차단되지 않았는지 확인합니다. |
|SPN에서 권한 부여 토큰을 가져오지 못함 |`Invalid client secret is provided` |서비스 주체 암호가 잘못되었거나 유효하지 않습니다. |서비스 주체 암호를 확인합니다. |
| SPN에서 권한 부여 토큰을 가져오지 못함 |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |서비스 주체 및/또는 테넌트 ID가 잘못되었습니다. |서비스 주체 및/또는 테넌트 ID를 확인합니다.|
|ARM 리소스 응답 가져오기 |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |자격 증명 및/또는 사용 권한이 잘못되었습니다. |사용자 또는 서비스 주체가 **Azure Connected Machine 온보딩** 역할의 멤버인지 확인합니다. |
|ARM 리소스를 AzcmagentConnect하지 못함 |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Azure 리소스 공급자가 등록되어 있지 않습니다. |[리소스 공급자](./agent-overview.md#register-azure-resource-providers)를 등록합니다. |
|ARM 리소스를 AzcmagentConnect하지 못함 |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |프록시 서버 또는 방화벽에서 `management.azure.com` 엔드포인트에 대한 액세스를 차단하고 있습니다. |엔드포인트 연결을 확인하고 방화벽 또는 프록시 서버에서 차단되지 않았는지 확인합니다. |

<a name="footnote1"></a><sup>1</sup>이 GPO가 사용하도록 설정되었으며 Connected Machine 에이전트가 있는 머신에 적용되는 경우 *himds* 서비스에 대해 지정된 기본 제공 계정과 연결된 사용자 프로필이 삭제됩니다. 따라서 서비스와 통신하는 데 사용되며 30일 동안 로컬 인증서 저장소에 캐시된 인증 인증서도 삭제됩니다. 30일 한도 전에 인증서 갱신을 시도합니다. 이슈를 해결하려면 [머신의 등록을 취소](manage-agent.md#unregister-machine)하는 단계를 수행한 다음, `azcmagent connect`를 실행하여 서비스에 다시 등록합니다.

## <a name="next-steps"></a>다음 단계

여기에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 받으세요.

* [Microsoft Q&A](/answers/topics/azure-arc.html)를 통해 Azure 전문가의 답변을 받습니다.

* 고객 환경을 개선하기 위한 공식 Microsoft Azure 계정인 [@AzureSupport](https://twitter.com/azuresupport)와 연결합니다. Azure 지원은 Azure 커뮤니티를 답변, 지원 및 전문가에게 연결합니다.

* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동하여 **지원 받기** 를 선택합니다.
