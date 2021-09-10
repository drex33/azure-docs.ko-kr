---
title: SCP를 사용하여 VM 간에 파일 이동
description: SCP 및 SSH 키 쌍을 사용하여 Azure에서 Linux VM 간에 안전하게 파일을 이동합니다.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/20/2021
ms.author: cynthn
ms.openlocfilehash: 5e7c342ae66594e502870030f8e8561ba861b05b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692105"
---
# <a name="use-scp-to-move-files-to-and-from-a-linux-vm"></a>SCP를 사용하여 Linux VM 간에 파일 이동 

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이 문서에서는 보안 복사본(SCP)를 사용하여 워크스테이션에서 Azure Linux VM으로 또는 Azure Linux VM에서 워크스테이션으로 파일을 이동하는 방법을 보여 줍니다. 워크스테이션과 Linux VM 간에 신속하고 안전하게 파일을 이동하는 것은 Azure 인프라 관리의 중요한 부분입니다. 

이 문서의 경우 [SSH 공개 및 프라이빗 키 파일](mac-create-ssh-keys.md)을 사용하여 Azure에 Linux VM이 배포되어 있어야 합니다. 또한 로컬 컴퓨터에 SCP 클라이언트가 필요합니다. SSH를 기반으로 빌드되었고 대부분의 Linux 및 Mac 컴퓨터와 PowerShell의 기본 Bash 셸에 포함되어 있습니다.


## <a name="quick-commands"></a>빠른 명령

Linux VM으로 파일 복사

```bash
scp file azureuser@azurehost:directory/targetfile
```

Linux VM에서 파일 복사

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>자세한 연습

예를 들어 Linux VM까지 Azure 구성 파일을 이동하고 로그 파일 디렉터리를 끌어오는 경우 모두 SCP 및 SSH 키를 사용하여 수행합니다.   

## <a name="ssh-key-pair-authentication"></a>SSH 키 쌍 인증

SCP는 전송 계층에 대해 SSH를 사용합니다. SSH는 대상 호스트에서 인증을 처리하고 SSH와 함께 기본적으로 제공되는 암호화된 터널에서 파일을 이동합니다. SSH 인증에는 사용자 이름 및 암호를 사용할 수 있습니다. 그러나 SSH 공개 및 프라이빗 키 인증이 보안 모범 사례로 권장됩니다. SSH에서 연결을 인증하면 SCP는 파일 복사를 시작합니다. 적절히 구성된 `~/.ssh/config`와 SSH 공개 및 프라이빗 키를 사용하면 서버 이름(또는 IP 주소)만 사용해서 SCP 연결을 설정할 수 있습니다. SSH 키가 하나만 있는 경우 SCP는 `~/.ssh/` 디렉터리에서 이 키를 찾은 후 VM에 로그인하는 데 기본적으로 사용합니다.

`~/.ssh/config`와 SSH 공개 및 프라이빗 키 구성에 대한 자세한 내용은 [SSH 키 만들기](mac-create-ssh-keys.md)를 참조하세요.

## <a name="scp-a-file-to-a-linux-vm"></a>파일을 Linux VM으로 SCP

첫 번째 예제에서는 Azure 구성 파일을 Automation 배포에 사용되는 Linux VM까지 복사합니다. 이 파일에는 비밀이 포함된 Azure API 자격 증명이 있으므로 보안이 중요합니다. SSH에서 제공하는 암호화된 터널은 파일의 콘텐츠를 보호합니다.

다음 명령에서는 로컬 *.azure/config* 파일을 FQDN *myserver.eastus.cloudapp.azure.com* 을 사용하여 Azure VM에 복사합니다. [FQDN이 설정](../create-fqdn.md)되지 않은 경우 VM의 IP 주소를 사용할 수도 있습니다. Azure VM의 관리자 사용자 이름은 *azureuser* 입니다. 파일은 */home/azureuser/* 디렉터리로 대상이 지정됩니다. 이 명령에서 고유한 값을 대체합니다.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>디렉터리를 Linux VM에서 SCP

이 예제에서는 Linux VM에서 워크스테이션으로 로그 파일 디렉터리를 복사합니다. 로그 파일에는 중요한 데이터 및 비밀 데이터가 있을 수도 있고 없을 수도 있습니다. 그러나 SCP를 사용하면 로그 파일의 콘텐츠가 암호화됩니다. SCP를 사용하여 파일을 전송하는 것이 로그 디렉터리 및 파일을 안전하게 워크스테이션으로 가져오는 가장 쉬우면서도 안전한 방법입니다.

다음 명령에서는 Azure VM에서 */home/azureuser/logs/* 디렉터리의 파일을 local/tmp 디렉터리로 복사합니다.

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

`-r` 플래그는 명령에 나열된 디렉터리의 시점부터 파일 및 디렉터리를 재귀적으로 복사하도록 SCP에 지시합니다.  또한 명령줄 구문은 `cp` 복사 명령과 비슷합니다.

## <a name="next-steps"></a>다음 단계

* [VMAccess 확장을 사용하여 사용자, SSH 관리 및 Azure Linux VM의 디스크 검사 또는 복구](../extensions/vmaccess.md?toc=/azure/virtual-machines/linux/toc.json)
