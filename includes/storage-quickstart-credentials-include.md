---
title: 포함 파일
description: 포함 파일
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 07/23/2021
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 463d251cc46e0a5735b1b5146bac30b3bc506f46
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674379"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Azure Portal에서 자격 증명 복사

샘플 애플리케이션이 Azure Storage에 대한 요청을 수행하는 경우 권한이 있어야 합니다. 요청에 권한을 부여하려면 스토리지 계정 자격 증명을 연결 문자열로 애플리케이션에 추가합니다. 스토리지 계정 자격 증명을 보려면 다음 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 스토리지 계정을 찾습니다.
3. 스토리지 계정 메뉴 창의 **보안 + 네트워킹** 에서 **액세스 키** 를 선택합니다. 여기에서 계정 액세스 키 및 각 키의 전체 연결 문자열을 볼 수 있습니다.

    ![Azure Portal에서 액세스 키 설정이 있는 위치를 보여 주는 스크린샷](./media/storage-access-keys-portal/portal-access-key-settings.png)
 
1. **액세스 키** 창에서 **키 표시** 를 선택합니다.
1. **key1** 섹션에서 **연결 문자열** 값을 찾습니다. **클립보드에 복사** 아이콘을 선택하여 연결 문자열을 복사합니다. 다음 단계에서 연결 문자열 값을 환경 변수에 추가합니다.

    ![Azure Portal에서 연결 문자열을 복사하는 방법을 보여주는 스크린샷](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>스토리지 연결 문자열 구성

연결 문자열을 복사한 후 애플리케이션을 실행 중인 로컬 컴퓨터의 새 환경 변수에 씁니다. 환경 변수를 설정하려면 콘솔 창을 열고 사용 중인 운영 체제의 지침을 따릅니다. `<yourconnectionstring>`을 실제 연결 문자열로 바꿉니다.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

Windows에서 환경 변수가 추가되면 명령 창의 새 인스턴스를 시작해야 합니다.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>프로그램 다시 시작

환경 변수가 추가되면 이 환경 변수를 읽어야 하는 실행 중인 프로그램을 다시 시작합니다. 예를 들어 개발 환경 또는 편집기를 다시 시작한 후에 계속합니다.
