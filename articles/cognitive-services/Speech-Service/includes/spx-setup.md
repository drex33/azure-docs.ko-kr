---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/28/2021
ms.author: v-demjoh
ms.custom: ignite-fall-2021
ms.openlocfilehash: 153276c1f3ccc63bbca0f286d3ae18c9e4de1b18
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253746"
---
## <a name="download-and-install"></a>다운로드 및 설치

#### <a name="windows-install"></a>[Windows 설치](#tab/windowsinstall)

Windows에 Speech CLI를 설치하려면 다음 단계를 수행합니다.

1. Windows의 경우 플랫폼에 맞는 [Visual Studio 2019용 Microsoft Visual C++ 재배포 가능 패키지](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)가 필요합니다. 처음 설치하려면 다시 시작해야 할 수 있습니다.
1. [.NET Core 3.1 SDK](/dotnet/core/install/windows)를 설치합니다.
2. 다음 명령을 입력하여 NuGet을 통해 음성 CLI를 설치합니다.

   ```console
   dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI
   ```
`spx`를 입력하여 Speech CLI에 대한 도움말을 표시합니다.

> [!NOTE]
> NuGet 대신 Windows용 Speech CLI를 [zip 파일](https://aka.ms/speech/spx-windows)로 다운로드하여 추출할 수 있습니다.

### <a name="font-limitations"></a>글꼴 제한 사항

Windows에서 Speech CLI는 로컬 컴퓨터에서 명령 프롬프트에 사용할 수 있는 글꼴만 표시할 수 있습니다.
[Windows 터미널](https://www.microsoft.com/p/windows-terminal/9n0dx20hk701)은 Speech CLI에서 대화형으로 생성된 모든 글꼴을 지원합니다.

파일로 출력하면 메모장과 같은 텍스트 편집기나 Microsoft Edge와 같은 웹 브라우저도 모든 글꼴을 표시할 수 있습니다.

#### <a name="linux-install"></a>[Linux 설치](#tab/linuxinstall)

다음 Linux 배포판은 음성 CLI를 사용하는 x64 아키텍처에 대해 지원됩니다.

* CentOS 7/8
* Debian 9/10 
* RHEL(Red Hat Enterprise Linux) 7/8
* Ubuntu 16.04(9월까지), Ubuntu 18.04/20.04

> [!NOTE]
> 추가 아키텍처는 Speech CLI가 아닌 Speech SDK에서 지원됩니다. 자세한 내용은 [Speech SDK 정보](../speech-sdk.md)를 참조하세요.

x64 CPU에서 Linux에 Speech CLI를 설치하려면 다음 단계를 수행합니다.

1. [.NET Core 3.1 SDK](/dotnet/core/install/linux)를 설치합니다.
2. 다음 명령을 입력하여 NuGet을 통해 음성 CLI를 설치합니다.

    `dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI`

3. RHEL/CentOS Linux에서 [linux용 OpenSSL을 구성](../how-to-configure-openssl-linux.md)합니다.
4. Ubunutu 20.04 Linux에서 [GStreamer를 설치](../how-to-use-codec-compressed-audio-input-streams.md)합니다.

`spx`를 입력하여 Speech CLI에 대한 도움말을 표시합니다.

> [!NOTE]
> NuGet 대신 Linux 이진 파일을 [zip 파일](https://aka.ms/speech/spx-linux)로 다운로드할 수 있습니다.
> `spx-netcore-30-linux-x64.zip`을 `~/spx` 디렉터리로 추출하여 이진 파일에 `sudo chmod +r+x spx`를 입력하고 `~/spx` 경로를 PATH 시스템 변수에 추가합니다.


#### <a name="docker-install-windows-linux-macos"></a>[Docker 설치(Windows, Linux, macOS)](#tab/dockerinstall)

> [!IMPORTANT]
> Docker 컨테이너 내에서 Speech CLI를 실행하는 경우에는 컴퓨터의 마이크를 사용할 수 없습니다. 그러나 로컬에 탑재된 디렉터리에서는 오디오 파일을 읽고 저장할 수 있습니다. 

> [!NOTE]
> 다음 예제에서는 Docker 허브에서 공용 컨테이너 이미지를 가져옵니다. 익명 끌어오기 요청을 수행하는 대신 먼저 Docker 허브 계정(`docker login`)을 사용하여 인증하는 것이 좋습니다. 공용 콘텐츠를 사용할 때 신뢰성을 향상시키려면 개인 Azure 컨테이너 레지스트리에서 이미지를 가져오고 관리하세요. [공용 이미지 사용에 대해 자세히 알아봅니다](../../../container-registry/buffer-gate-public-content.md).

Docker 컨테이너에서 Speech CLI를 설치하려면 다음 단계를 수행합니다.

1. 아직 설치되지 않은 경우 플랫폼에 <a href="https://www.docker.com/get-started" target="_blank">Docker Desktop을 설치</a>합니다.
2. 새 명령 프롬프트 또는 터미널에서 다음 명령을 입력합니다. 
   ```console   
   docker pull msftspeech/spx
   ```
3. 다음 명령을 입력합니다. Speech CLI에 대한 도움말 정보가 표시되어야 합니다. 
   ```console 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>컨테이너에 디렉터리 탑재

Speech CLI 도구는 구성 설정을 파일로 저장하고 명령(도움말 명령 제외)을 수행할 때 이러한 파일을 로드합니다.
Docker 컨테이너 내에서 Speech CLI를 사용하는 경우 도구에서 구성 설정을 저장하거나 찾을 수 있도록 컨테이너에서 로컬 디렉터리를 탑재해야 합니다. 그러면 도구에서 음성의 오디오 파일 등 명령에 필요한 모든 파일을 읽거나 쓸 수 있습니다.

Windows에서 다음 명령을 입력하여 컨테이너 내에서 사용할 수 있는 로컬 디렉터리 Speech CLI를 만듭니다.

`mkdir c:\spx-data`

또는 Linux 또는 macOS에서 터미널에 다음 명령을 입력하여 디렉터리를 만들고 절대 경로를 확인합니다.

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Speech CLI를 호출할 때 절대 경로를 사용합니다.

### <a name="run-speech-cli-in-the-container"></a>컨테이너에서 Speech CLI 실행

이 설명서에서는 비 Docker 설치에서 사용되는 Speech CLI `spx` 명령을 보여 줍니다.
Docker 컨테이너에서 `spx` 명령을 호출하는 경우, Speech CLI에서 구성 값을 저장하고 찾고 파일을 읽고 쓸 수 있는 파일 시스템에 컨테이너의 디렉터리를 탑재해야 합니다.

Windows에서 명령은 다음과 같이 시작됩니다.

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

Linux 또는 macOS에서 명령은 아래 샘플과 같습니다. `ABSOLUTE_PATH`를 탑재된 디렉터리의 절대 경로로 바꿉니다. 이 경로는 이전 섹션의 `pwd` 명령에 의해 반환되었습니다. 

키와 지역을 설정하기 전에 이 명령을 실행하면 키와 지역을 설정하라는 오류 메시지가 표시됩니다.
```console   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

컨테이너에 설치된 `spx` 명령을 사용하려면 항상 위에 표시된 전체 명령과 요청의 매개 변수를 차례로 입력합니다.
예를 들어 Windows에서 이 명령은 키를 설정합니다.

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

명령줄 도구와의 보다 확장된 상호 작용을 위해 entrypoint 매개 변수를 추가하여 대화형 bash 셸로 컨테이너를 시작할 수 있습니다.
Windows에서 다음 명령을 입력하여 여러 `spx` 명령을 입력할 수 있는 대화형 명령줄 인터페이스를 표시하는 컨테이너를 시작합니다.
```console
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>구독 구성 만들기

음성 CLI 사용을 시작하려면 음성 구독 키 및 지역 식별자를 입력해야 합니다. [음성 서비스 무료로 사용해 보기](../overview.md#try-the-speech-service-for-free)의 단계를 따라 이러한 자격 증명을 가져오세요.
구독 키와 지역 식별자가 있으면(예: `eastus`, `westus`) 다음 명령을 실행합니다.

```console
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

구독 인증은 이제 향후 SPX 요청에 대해 저장됩니다. 이러한 저장된 값 중 하나를 제거해야 하는 경우 `spx config @region --clear` 또는 `spx config @key --clear`를 실행합니다.
