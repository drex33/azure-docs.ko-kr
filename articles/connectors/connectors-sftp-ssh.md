---
title: SSH를 사용하여 SFTP 서버에 연결
description: SSH 및 Azure Logic Apps를 사용하여 SFTP 서버에 대한 파일을 모니터링, 만들기, 관리, 전송 및 수신하는 작업 자동화
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 08/05/2021
tags: connectors
ms.openlocfilehash: 32638d71f2c700700be5eb1b2ad63f18969d82a0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529342"
---
# <a name="create-and-manage-sftp-files-using-ssh-and-azure-logic-apps"></a>SSH 및 Azure Logic Apps를 사용하여 SFTP 파일 만들기 및 관리

[SSH(Secure Shell)](https://www.ssh.com/ssh/protocol/) 프로토콜을 사용하여 [SFTP(보안 파일 전송 프로토콜)](https://www.ssh.com/ssh/sftp/) 서버에서 파일을 만들고 관리하는 작업을 자동화하려면 Azure Logic Apps 및 SFTP-SSH 커넥터를 사용하여 자동화된 통합 워크플로를 만들면 됩니다. SFTP는 신뢰할 수 있는 데이터 스트림을 통해 파일 액세스, 파일 전송 및 파일 관리를 제공하는 네트워크 프로토콜입니다.

다음은 자동화할 수 있는 몇 가지 예제 작업입니다.

* 파일이 추가되거나 변경될 때 모니터링합니다.
* 파일 가져오기/만들기/복사/이름 바꾸기/업데이트/나열/삭제를 수행합니다.
* 폴더를 만듭니다.
* 파일 콘텐츠 및 메타데이터를 가져옵니다.
* 보관을 폴더로 추출합니다.

워크플로에서 SFTP 서버의 이벤트를 모니터링하고 다른 동작에 출력을 제공하는 트리거를 사용할 수 있습니다. 그런 다음, SFTP 서버에서 다양한 태스크를 수행하는 동작을 사용할 수 있습니다. 또한 SFTP-SSH 동작의 출력을 사용하는 다른 동작을 포함할 수도 있습니다. 예를 들어 정기적으로 SFTP 서버에서 파일을 검색하는 경우 Office 365 Outlook 커넥터 또는 Outlook.com 커넥터를 사용하여 해당 파일 및 해당 콘텐츠에 대한 이메일 경고를 보낼 수 있습니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

SFTP-SSH 커넥터와 SFTP 커넥터 간의 차이점을 보려면 이 항목의 뒷부분에 나오는 [SFTP-SSH와 SFTP 비교](#comparison) 섹션을 검토합니다.

## <a name="limits"></a>제한

* SFTP-SSH 커넥터는 현재 이러한 SFTP 서버를 지원하지 않습니다.

  * IBM DataPower
  * MessageWay
  * OpenText Secure MFT
  * OpenText GXS

* [청크](../logic-apps/logic-apps-handle-large-messages.md)를 지원하는 SFTP-SSH 동작은 최대 1GB의 파일을 처리할 수 있지만 청크를 지원하지 않는 SFTP-SSH 동작은 최대 50MB의 파일을 처리할 수 있습니다. 기본 청크 크기는 15MB입니다. 그러나 이 크기는 5MB에서 시작하여 점진적으로 최대 50MB까지 동적으로 변경될 수 있습니다. 동적 크기 조정은 네트워크 대기 시간, 서버 응답 시간 등의 요인을 기반으로 합니다.

  > [!NOTE]
  > [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)의 논리 앱에서는 이 커넥터의 ISE 레이블 지정 버전이 [ISE 메시지 한도](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)를 대신 사용하기 위해 청크가 필요합니다.

  대신 사용할 [상수 청크 크기를 지정](#change-chunk-size)할 때 이 적응형 동작을 재정의할 수 있습니다. 이 크기는 5MB~50MB 범위에 이릅니다. 예를 들어 45MB 파일이 있고 대기 시간 없이 이 파일 크기를 지원할 수 있는 네트워크가 있다고 가정하겠습니다. 적응형 청크를 사용하면 호출이 한 번 발생하지 않고 여러 번 발생합니다. 호출 수를 줄이려면 50MB 청크 크기를 설정해 보면 됩니다. 15MB 청크를 사용하는 경우와 같은 다른 시나리오에서 이 논리 앱의 제한 시간이 초과되면 크기를 5MB로 줄여 봅니다.

  청크 크기는 연결과 관련이 있습니다. 이 특성은 청크를 지원하는 동작과 청크를 지원하지 않는 동작에 동일한 연결을 사용할 수 있음을 의미합니다. 이 경우 청크를 지원하지 않는 동작의 청크 크기는 5MB에서 50MB 사이입니다. 다음 표에서는 청크를 지원하는 SFTP-SSH 동작을 보여 줍니다.

  | 작업 | 청크 지원 | 청크 크기 재정의 지원 |
  |--------|------------------|-----------------------------|
  | **파일 복사** | 아니요 | 해당 없음 |
  | **파일 만들기** | 예 | 예 |
  | **폴더 만들기** | 해당 없음 | 해당 없음 |
  | **파일 삭제** | 해당 없음 | 해당 없음 |
  | **폴더에 보관 추출** | 해당 없음 | 해당 없음 |
  | **파일 콘텐츠 가져오기** | 예 | 예 |
  | **경로를 사용하여 파일 콘텐츠 가져오기** | 예 | 예 |
  | **파일 메타데이터 가져오기** | 해당 없음 | 해당 없음 |
  | **경로를 사용하여 파일 메타데이터 가져오기** | 해당 없음 | 해당 없음 |
  | **폴더의 파일 나열** | 해당 없음 | 해당 없음 |
  | **파일 이름 바꾸기** | 해당 없음 | 해당 없음 |
  | **파일 업데이트** | 아니요 | 해당 없음 |
  ||||

* SFTP-SSH 트리거는 메시지 청크를 지원하지 않습니다. 파일 콘텐츠를 요청하는 경우 트리거는 15MB 이하의 파일만 선택합니다. 15MB보다 큰 파일을 가져오려면 다음 패턴을 따릅니다.

  1. 파일 속성만 반환하는 SFTP-SSH 트리거를 사용합니다. 이러한 트리거에는 설명 **(속성만)** 이 포함된 이름이 있습니다.

  1. SFTP-SSH **파일 콘텐츠 가져오기** 동작을 사용 하여 트리거를 따릅니다. 이 동작은 전체 파일을 읽고 메시지 청크를 암시적으로 사용합니다.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>SFTP-SSH와 SFTP 비교

다음 목록에서는 SFTP 커넥터와 다른 주요 SFTP-SSH 기능을 설명합니다.

* .NET을 지원하는 오픈 소스 SSH(Secure Shell) 라이브러리인 [SSH.NET 라이브러리](https://github.com/sshnet/SSH.NET)를 사용합니다.

* SFTP 서버의 지정된 경로에 폴더를 만드는 **폴더 만들기** 작업을 제공합니다.

* SFTP 서버의 파일 이름을 바꾸는 **파일 이름 바꾸기** 작업을 제공합니다.

* *최대 1시간 동안* SFTP 서버에 대한 연결을 캐시합니다. 이 기능은 성능을 개선하고 커넥터에서 서버에 연결을 시도하는 빈도를 줄입니다. 이 캐싱 동작에 대한 기간을 설정하려면 SFTP 서버의 SSH 구성에서 [**ClientAliveInterval** 속성](https://man.openbsd.org/sshd_config#ClientAliveInterval)을 편집합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 워크플로에서 SFTP 계정에 액세스할 수 있도록 하는 SFTP 서버 주소 및 계정 자격 증명. SSH 프라이빗 키 및 SSH 프라이빗 키 암호에 대한 액세스도 필요합니다. 청크를 사용하여 대용량 파일을 업로드하려면 SFTP 서버의 루트 폴더에 대한 읽기 및 쓰기 권한이 모두 필요합니다. 그렇지 않으면 "401 권한 없음" 오류가 발생합니다.

  SFTP-SSH 커넥터는 프라이빗 키 인증과 암호 인증을 모두 지원합니다. 그러나 SFTP-SSH 커넥터는 다음과 같은 프라이빗 키 형식, 암호화 알고리즘, 지문, 키 교환 알고리즘‘만’ 지원합니다.

  * **프라이빗 키 형식**: OpenSSH와 ssh.com 형식의 RSA(Rivest Shamir Adleman) 및 DSA(디지털 서명 알고리즘) 키 프라이빗 키가 PuTTY(.ppk) 파일 형식인 경우 먼저 [키를 OpenSSH(.pem) 파일 형식으로 변환](#convert-to-openssh)합니다.
  * **암호화 알고리즘**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC 및 AES-256-CBC
  * **지문**: MD5
  * **키 교환 알고리즘**: curve25519-sha256, curve25519-sha256@libssh.org, ecdh-sha2-nistp256, ecdh-sha2-nistp384, ecdh-sha2-nistp521, diffie-hellman-group-exchange-sha256, diffie-hellman-group-exchange-sha1, diffie-hellman-group16-sha512, diffie-hellman-group14-sha256, diffie-hellman-group14-sha1, diffie-hellman-group1-sha1

  워크플로에 SFTP-SSH 트리거 또는 동작을 추가한 후에는 SFTP 서버에 대한 연결 정보를 제공해야 합니다. 이 연결에 대한 SSH 프라이빗 키를 제공하는 경우 ***키를 수동으로 입력하거나 편집하지 마세요**. 이로 인해 연결이 실패할 수 있습니다. 대신, SSH 프라이빗 키 파일에서 _*_키를 복사_*_ 하고 해당 키를 연결 세부 정보에 *_붙여넣어야 합니다_**. 자세한 내용은 이 문서의 뒷부분에 나오는 [SSH를 사용하여 SFTP에 연결](#connect) 섹션을 참조하세요.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* SFTP 계정에 액세스하려는 논리 앱 워크플로. SFTP-SSH 트리거를 시작하려면 [빈 논리 앱 워크플로를 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). SFTP-SSH 동작을 사용하려면 **되풀이** 트리거 같은 다른 트리거를 통해 워크플로를 시작합니다.

## <a name="how-sftp-ssh-triggers-work"></a>SFTP-SSH 트리거 작동 방법

<a name="polling-behavior"></a>

### <a name="polling-behavior"></a>폴링 동작

SFTP-SSH 트리거는 SFTP 파일 시스템을 폴링하여 마지막 폴링 이후 변경된 파일을 찾습니다. 일부 도구를 통해 파일을 변경하는 경우 타임스탬프를 유지할 수 있습니다. 이러한 경우 트리거가 작동할 수 있도록 이 기능을 사용하지 않도록 설정해야 합니다. 아래에는 몇 가지 일반적인 설정이 나와 있습니다.

| SFTP 클라이언트 | 작업 |
|-------------|--------|
| Winscp | **옵션** > **기본 설정** > **전송** > **편집** > **타임스탬프 보존** > **사용 안 함** 으로 이동 |
| FileZilla | **전송** > **전송된 파일의 타임스탬프 보존** > **사용 안 함** 으로 이동 |
|||

트리거는 새 파일을 찾으면 해당 파일이 완전한 상태이며 부분적으로 작성된 것이 아닌지 확인합니다. 예를 들어 트리거가 파일 서버를 확인할 때 파일을 변경하는 중일 수 있습니다. 부분적으로 작성된 파일이 반환되지 않도록 하기 위해 트리거는 최근 변경된 내용이 있는 파일의 타임스탬프를 기록하되 해당 파일을 즉시 반환하지는 않으며, 서버를 다시 폴링할 때만 해당 파일을 반환합니다. 이 동작으로 인해 트리거 폴링 간격의 최대 2배까지 지연이 발생하는 경우도 있습니다.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>트리거 되풀이 이동 및 드리프트

SFTP-SSH 트리거와 같이 먼저 연결을 만들어야 하는 연결 기반 트리거는 [되풀이 트리거](../connectors/connectors-native-recurrence.md)와 같이 기본적으로 Azure Logic Apps에서 실행되는 기본 제공 트리거와 다릅니다. 되풀이 연결 기반 트리거에서 되풀이 일정은 실행을 제어하는 유일한 드라이버가 아니며, 표준 시간대는 최초 시작 시간만 결정합니다. 후속 실행은 되풀이 일정, 마지막 트리거 실행 *및* 런타임 시 예기치 않은 동작을 발생시킬 수 있는 기타 요인에 따라 달라집니다. 예를 들어 예기치 않은 동작에는 DST(일광 절약 시간제)가 시작되고 끝날 때 지정된 일정을 유지하지 못하는 오류가 포함될 수 있습니다. DST가 적용될 때 되풀이 시간이 이동하지 않게 하려면 되풀이를 수동으로 조정합니다. 이렇게 하면 워크플로가 예상 시간에 계속 실행됩니다. 그렇지 않으면 DST가 시작될 때 시작 시간이 1시간 앞으로 이동하고, DST가 종료되면 1시간 뒤로 이동합니다. 자세한 내용은 [연결 기반 트리거의 되풀이](../connectors/apis-list.md#recurrence-for-connection-based-triggers)를 참조하세요.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>PuTTY 기반 키를 OpenSSH로 변환

PuTTY 형식 및 OpenSSH 형식은 서로 다른 파일 이름 확장명을 사용합니다. PuTTY 형식은 .ppk 또는 PuTTY 프라이빗 키, 파일 이름 확장명을 사용합니다. OpenSSH 형식은 .pem, 즉 Privacy Enhanced Mail 파일 이름 확장명을 사용합니다. 프라이빗 키가 PuTTY 형식이고 OpenSSH 형식을 사용해야 하는 경우 먼저 다음 단계에 따라 키를 OpenSSH 형식으로 변환합니다.

### <a name="unix-based-os"></a>Unix 기반 OS

1. 시스템에 PuTTY 도구가 설치되어 있지 않은 경우 지금 설치합니다. 예를 들면 다음과 같습니다.

   `sudo apt-get install -y putty`

1. 다음 명령을 실행하여 SFTP-SSH 커넥터와 함께 사용할 수 있는 파일을 만듭니다.

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   예를 들면 다음과 같습니다.

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows OS

1. 아직 다운로드하지 않은 경우 [최신 PuTTY 생성기(puttygen.exe) 도구](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)를 다운로드한 다음, 도구를 시작합니다.

1. 이 화면에서 **로드** 를 선택합니다.

   !["로드" 선택](./media/connectors-sftp-ssh/puttygen-load.png)

1. PuTTY 형식의 프라이빗 키 파일을 찾아 **열기** 를 선택합니다.

1. **변환** 메뉴에서 **OpenSSH 키 내보내기** 를 선택합니다.

   !["OpenSSH 키 내보내기" 선택](./media/connectors-sftp-ssh/export-openssh-key.png)

1. `.pem` 파일 이름 확장명을 사용하여 프라이빗 키 파일을 저장합니다.

## <a name="considerations"></a>고려 사항

이 섹션에서는 이 커넥터의 트리거 및 동작을 사용할 때 고려해야 할 사항을 설명합니다.

<a name="different-folders-trigger-processing-file-storage"></a>

### <a name="use-different-sftp-folders-for-file-upload-and-processing"></a>파일 업로드 및 처리에 서로 다른 SFTP 폴더 사용

SFTP 서버에서 업로드된 파일을 저장하는 폴더와 트리거에서 이러한 파일을 처리하기 위해 모니터링하는 폴더는 별도로 두어야 합니다. 그렇지 않으면 트리거가 작동하지 않고 예기치 않게 동작합니다. 예를 들어 트리거에서 임의 개수의 파일 처리를 건너뛸 수 있습니다. 그러나 이 요구 사항은 해당 폴더 간에 파일을 이동할 방법이 필요함을 의미합니다. 

이 트리거 문제가 발생하면 트리거가 모니터링하는 폴더에서 파일을 제거하고 다른 폴더를 사용하여 업로드된 파일을 저장합니다.

<a name="create-file"></a>

### <a name="create-file"></a>파일 만들기

SFTP 서버에서 파일을 만들려면 SFTP-SSH **파일 만들기** 동작을 사용하면 됩니다. 이 동작으로 파일이 생성되면 Logic Apps 서비스가 SFTP 서버를 자동으로 호출하여 파일의 메타데이터를 가져옵니다. 그러나 Logic Apps 서비스에서 메타데이터를 가져오는 호출을 전송하기 전에 새로 만든 파일을 이동할 경우 `404` 오류 메시지 `'A reference was made to a file or folder which does not exist'`가 표시됩니다. 파일을 만든 후 파일의 메타데이터 읽기를 건너뛰려면 [**모든 파일 메타데이터 가져오기** 속성을 **아니요**](#file-does-not-exist)로 설정하는 단계를 수행합니다.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>SSH를 사용하여 SFTP에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고, 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 빈 논리 앱의 경우 검색 상자에서 필터로 `sftp ssh`를 입력합니다. 트리거 목록에서 원하는 트리거를 선택합니다.

   또는

   기존 논리 앱의 경우 작업을 추가하려는 마지막 단계에서 **새 단계** 를 선택합니다. 검색 상자에서 필터로 `sftp ssh`을 입력합니다. 작업 목록에서 원하는 작업을 선택합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시되는 더하기 기호( **+** )를 선택한 다음, **작업 추가** 를 선택합니다.

1. 연결에 필요한 정보를 입력합니다.

   > [!IMPORTANT]
   >
   > **SSH 프라이빗 키** 속성에 SSH 프라이빗 키를 입력할 때 이 속성에 대한 완전하고 올바른 값을 제공하는 데 도움이 되는 다음 추가 단계를 수행합니다. 잘못된 키로 인해 연결 실패가 발생합니다.

   모든 텍스트 편집기를 사용할 수 있지만 예를 들어 Notepad.exe를 사용하여 키를 올바르게 복사하고 붙여넣는 방법을 보여주는 샘플 단계는 다음과 같습니다.

   1. 텍스트 편집기에서 SSH 프라이빗 키 파일을 엽니다. 이러한 단계는 예제로 메모장을 사용합니다.

   1. 메모장의 **편집** 메뉴에서 **모두 선택** 을 선택합니다.

   1. **편집** > **복사** 를 선택합니다.

   1. SFTP-SSH 트리거 또는 동작에서 **SSH 프라이빗 키** 속성에 복사한 전체 키를 *붙여넣습니다*. 이는 여러 줄을 지원합니다. **_키를 수동으로 입력하거나 편집하지 마세요_**.

1. 연결 세부 정보 입력을 완료한 후 **만들기** 를 선택합니다.

1. 이제 선택한 트리거 또는 작업에 대해 필요한 세부 정보를 제공하고 논리 앱의 워크플로를 계속 빌드합니다.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>청크 크기 재정의

청크에서 사용하는 기본 적응형 동작을 재정의하려면 상수 청크 크기를 5MB~50MB로 지정하면 됩니다.

1. 동작의 오른쪽 위 모서리에서 줄임표 단추( **...** )를 선택한 다음, **설정** 을 선택합니다.

   ![SFTP-SSH 설정 열기](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. **콘텐츠 전송** 의 **청크 크기** 속성에 `5`~`50`의 정수 값을 입력합니다. 예를 들면 다음과 같습니다. 

   ![대신 사용할 청크 크기 지정](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. 완료되면 **완료** 를 선택합니다.

## <a name="examples"></a>예제

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - SSH 트리거: 파일이 추가되거나 수정되는 경우

이 트리거는 SFTP 서버에서 파일이 추가되거나 변경되는 경우 워크플로를 시작합니다. 예제 후속 작업과 같이, 워크플로는 조건을 사용하여 파일 콘텐츠가 지정된 조건을 충족하는지 여부를 확인할 수 있습니다. 콘텐츠가 조건을 충족하는 경우 **파일 콘텐츠 가져오기** SFTP-SSH 동작이 콘텐츠를 가져올 수 있으며, 다른 SFTP-SSH 동작은 해당 파일을 SFTP 서버의 다른 폴더에 배치할 수 있습니다.

**엔터프라이즈 예제**: 이 트리거를 사용하여 고객의 주문을 나타내는 새 파일에 대한 SFTP 폴더를 모니터링할 수 있습니다. 그런 다음, **파일 콘텐츠 가져오기** 와 같은 SFTP-SSH 동작을 사용할 수 있으므로 추가로 처리할 주문의 콘텐츠를 가져오고 주문 데이터베이스에 해당 주문을 저장합니다.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP - SSH 동작: 경로를 사용하여 파일 콘텐츠 가져오기

이 동작은 파일 경로를 지정하여 SFTP 서버의 파일에서 콘텐츠를 가져옵니다. 따라서 예를 들어 이전 예제의 트리거와 파일의 콘텐츠가 충족해야 하는 조건을 추가할 수 있습니다. 조건이 true인 경우 콘텐츠를 가져오는 작업을 실행할 수 있습니다.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-problems"></a>문제 해결

이 섹션에서는 일반적인 오류 또는 문제에 대한 가능한 해결 방법을 설명합니다.

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>504 오류: "연결된 대상이 일정 시간 후 제대로 응답하지 않아서 연결 시도가 실패했거나 연결된 호스트가 응답하지 못했기 때문에 설정된 연결이 실패했습니다." 또는 "SFTP 서버에 대한 요청이 '00:00:30'초 넘게 걸렸습니다."

이 오류는 논리 앱이 SFTP 서버와의 연결을 성공적으로 설정할 수 없는 경우에 발생할 수 있습니다. 이 문제에 대한 다른 이유가 있을 수 있으므로 다음 문제 해결 옵션을 사용해 보세요.

* 연결 제한 시간은 20초입니다. SFTP 서버의 성능이 양호하고 중간 디바이스(예: 방화벽)가 오버헤드를 추가하지 않는지 확인합니다.

* 방화벽이 설정된 경우 지역에 대한 **관리형 커넥터 IP** 주소를 승인된 목록에 추가해야 합니다. 논리 앱의 지역에 대한 IP 주소를 찾으려면 [관리형 커넥터 아웃바운드 IP - Azure Logic Apps](/connectors/common/outbound-ip-addresses)를 참조하세요.

* 이 오류가 간헐적으로 발생하는 경우 SFTP-SSH 동작의 **재시도 정책** 설정을 기본 4회 재시도보다 많은 재시도 횟수로 변경합니다.

* SFTP 서버가 각 IP 주소의 연결 수에 제한을 두는지 확인합니다. 제한이 있는 경우 동시 논리 앱 인스턴스 수를 제한해야 할 수 있습니다.

* 연결 설정 비용을 줄이려면 SFTP 서버에 대한 SSH 구성에서 [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) 속성을 약 1시간으로 늘려야 합니다.

* SFTP 서버 로그를 검토하여 논리 앱의 요청이 SFTP 서버에 도달했는지 확인합니다. 연결 문제에 대한 자세한 정보를 얻기 위해 방화벽 및 SFTP 서버에서 네트워크 추적을 실행할 수도 있습니다.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404 오류: "존재하지 않는 파일 또는 폴더가 참조되었습니다."

이 오류는 워크플로에서 SFTP-SSH **파일 만들기** 동작을 사용하여 SFTP 서버에 파일을 만들었지만 Logic Apps 서비스가 파일의 메타데이터를 가져오기 전에 해당 파일을 즉시 이동할 경우에 발생할 수 있습니다. 워크플로에서 **파일 만들기** 동작을 실행하면 Logic Apps 서비스에서 SFTP 서버를 자동으로 호출하여 파일의 메타데이터를 가져옵니다. 그러나 논리 앱에서 파일을 이동하면 Logic Apps 서비스에서 더 이상 파일을 찾을 수 없으므로 `404` 오류 메시지가 표시됩니다.

파일 이동을 방지하거나 지연할 수 없는 경우 다음 단계를 수행하여 파일을 만든 후 파일의 메타데이터 읽기를 건너뛸 수 있습니다.

1. **파일 만들기** 동작에서 **새 매개 변수 추가** 목록을 열고, **모든 파일 메타데이터 가져오기** 속성을 선택하고, 값을 **아니요** 로 설정합니다.

1. 나중에 이 파일 메타데이터가 필요할 경우 **파일 메타데이터 가져오기** 동작을 사용할 수 있습니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에 설명된 트리거, 동작, 제한 등 이 커넥터에 대한 자세한 기술 정보는 [커넥터의 참조 페이지](/connectors/sftpwithssh/)에서 확인할 수 있습니다.

> [!NOTE]
> [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)의 논리 앱에서는 이 커넥터의 ISE 레이블 지정 버전이 [ISE 메시지 한도](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)를 대신 사용하기 위해 청크가 필요합니다.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
