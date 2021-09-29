---
title: 자습서 - 주 지역에서 데이터를 읽는 동안 발생하는 오류 시뮬레이션
titleSuffix: Azure Storage
description: 스토리지 계정이 RA-GZRS(읽기 액세스 지역 중복 스토리지)용으로 구성된 경우 주 지역에서 데이터를 읽는 동안 발생하는 오류를 시뮬레이션합니다.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: artek
ms.custom: devx-track-js
ms.openlocfilehash: fe955ede3c2936c0c842d19fbadb9a075830822c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603489"
---
# <a name="tutorial-simulate-a-failure-in-reading-data-from-the-primary-region"></a>자습서: 주 지역에서 데이터를 읽는 동안 발생하는 오류 시뮬레이션

이 자습서는 시리즈의 2부입니다. 여기에서 오류를 시뮬레이션하여 [RA-GZRS(읽기 액세스 지역 중복 스토리지)](../common/storage-redundancy.md)의 이점에 대해 알아봅니다.

오류를 시뮬레이션하려면 [정적 라우팅](#simulate-a-failure-with-an-invalid-static-route) 또는 [Fiddler](#simulate-a-failure-with-fiddler)를 사용하면 됩니다. 두 방법으로 [RA-GZRS(읽기 액세스 지역 중복)](../common/storage-redundancy.md) 스토리지 계정의 기본 엔드포인트에 대한 요청 실패를 시뮬레이션하여 애플리케이션이 대신 보조 엔드포인트에서 읽도록 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

시리즈 2부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> - 애플리케이션 실행 및 일시 중지
> - [잘못된 고정 경로](#simulate-a-failure-with-an-invalid-static-route) 또는 [Fiddler](#simulate-a-failure-with-fiddler)를 사용하는 실패 시뮬레이션
> - 기본 엔드포인트 복원 시뮬레이션

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에 이전 자습서: [Azure Storage를 통해 애플리케이션 데이터의 고가용성 지원][previous-tutorial]을 완료하세요.

정적 라우팅을 사용하여 실패를 시뮬레이트하려면 관리자 권한의 명령 프롬프트를 사용합니다.

Fiddler를 사용하여 실패를 시뮬레이트하려면 [Fiddler를 다운로드한 후 설치](https://www.telerik.com/download/fiddler)합니다.

## <a name="simulate-a-failure-with-an-invalid-static-route"></a>잘못된 고정 경로를 사용하여 실패 시뮬레이션

[RA-GZRS(읽기 액세스 지역 중복)](../common/storage-redundancy.md) 스토리지 계정의 기본 엔드포인트에 대한 모든 요청에 대해 잘못된 고정 경로를 만들 수 있습니다. 이 자습서에서는 로컬 호스트가 스토리지 계정에 대한 요청을 라우팅하기 위한 게이트웨이로 사용됩니다. 로컬 호스트를 게이트웨이로 사용하면 스토리지 계정의 기본 엔드포인트에 대한 모든 요청이 호스트 내부로 루프 백되어 실패가 발생하게 됩니다. 다음 단계에 따라 실패를 시뮬레이션하고 잘못된 고정 경로를 사용하여 기본 엔드포인트를 복원합니다.

### <a name="start-and-pause-the-application"></a>애플리케이션 시작 및 일시 중지

[이전 자습서][previous-tutorial]의 지침에 따라 샘플을 시작하고 테스트 파일을 다운로드하여 기본 스토리지에서 가져온 것인지 확인합니다. 대상 플랫폼에 따라 샘플을 수동으로 일시 중지하거나 프롬프트 시 대기할 수 있습니다.

### <a name="simulate-failure"></a>오류 시뮬레이션

애플리케이션이 일시 중지된 상태에서 관리자 권한으로 Windows에서 명령 프롬프트를 열거나 Linux에서 루트로 터미널을 실행합니다.

명령 프롬프트 또는 터미널에서 `STORAGEACCOUNTNAME`을 스토리지 계정의 이름으로 바꾼 후 다음 명령을 입력하여 스토리지 계정의 기본 엔드포인트 도메인에 대한 정보를 가져옵니다.

```
nslookup STORAGEACCOUNTNAME.blob.core.windows.net
```

나중에 사용하기 위해 스토리지 계정의 IP 주소를 텍스트 편집기에 복사합니다.

로컬 호스트의 IP 주소를 가져오려면 Windows 명령 프롬프트에서 `ipconfig`를 입력하거나 Linux 터미널에서 `ifconfig`를 입력합니다.

대상 호스트에 대한 정적 경로를 추가하려면 Windows 명령 프롬프트 또는 Linux 터미널에서 `<destination_ip>`를 스토리지 계정 IP 주소로, `<gateway_ip>`를 로컬 호스트 IP 주소로 바꾼 후 다음 명령을 입력합니다.

#### <a name="linux"></a>Linux

```
route add <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```
route add <destination_ip> <gateway_ip>
```

샘플이 실행 중인 창에서 애플리케이션을 다시 시작하거나 해당 키를 눌러 샘플 파일을 다운로드하고, 보조 스토리지에서 가져온 것인지 확인합니다. 샘플을 다시 일시 중지하거나 프롬프트에서 대기할 수 있습니다.

### <a name="simulate-primary-endpoint-restoration"></a>기본 엔드포인트 복원 시뮬레이션

다시 작동하는 기본 엔드포인트를 시뮬레이트하려면 라우팅 테이블에서 올바르지 않은 정적 경로를 삭제합니다. 이렇게 하면 기본 엔드포인트에 대한 모든 요청이 기본 게이트웨이를 통해 라우팅됩니다. Windows 명령 프롬프트 또는 Linux 터미널에서 다음 명령을 입력합니다.

#### <a name="linux"></a>Linux

```bash
route del <destination_ip> gw <gateway_ip>
```

#### <a name="windows"></a>Windows

```console
route delete <destination_ip>
```

그런 다음, 애플리케이션을 다시 시작하거나 해당 키를 눌러 샘플을 다시 다운로드합니다. 이번에는 기본 스토리지에서 가져온 것인지 확인합니다.

## <a name="simulate-a-failure-with-fiddler"></a>Fiddler를 사용하여 실패 시뮬레이션

Fiddler를 사용하여 오류를 시뮬레이션하려면 요청에 대해 실패한 응답을 RA-GZRS 스토리지 계정의 기본 엔드포인트에 삽입합니다.

다음 섹션에서는 오류를 시뮬레이션하고 Fiddler를 사용하여 기본 엔드포인트를 복원하는 방법을 설명합니다.

### <a name="launch-fiddler"></a>Fiddler 시작

Fiddler를 열고 **규칙** 과 **규칙 사용자 지정** 을 선택합니다.

![Fiddler 규칙 사용자 지정](media/simulate-primary-region-failure/figure1.png)

Fiddler ScriptEditor가 시작되고 **SampleRules.js** 파일을 표시합니다. 이 파일을 사용하여 Fiddler를 사용자 지정합니다.

`STORAGEACCOUNTNAME`을 스토리지 계정의 이름으로 바꾼 후 다음 코드 샘플을 `OnBeforeResponse` 함수에 붙여넣습니다. 샘플에 따라, `HelloWorld`를 다운로드할 테스트 파일의 이름(`sampleFile`과 같은 접두사)으로 바꿀 수도 있습니다. 새 코드는 즉시 실행되지 않도록 주석으로 처리됩니다.

완료되면 **파일** 과 **저장** 을 선택하여 변경 내용을 저장합니다. 다음 단계에서 사용하므로 ScriptEditor 창을 열어 둡니다.

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error.
        // When you're ready to stop sending back errors, comment these lines of script out again
        //     and save the changes.

        if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;
        }
    */
```

![사용자 지정된 규칙 붙여넣기](media/simulate-primary-region-failure/figure2.png)

### <a name="start-and-pause-the-application"></a>애플리케이션 시작 및 일시 중지

[이전 자습서][previous-tutorial]의 지침에 따라 샘플을 시작하고 테스트 파일을 다운로드하여 기본 스토리지에서 가져온 것인지 확인합니다. 대상 플랫폼에 따라 샘플을 수동으로 일시 중지하거나 프롬프트 시 대기할 수 있습니다.

### <a name="simulate-failure"></a>오류 시뮬레이션

애플리케이션이 일시 중지된 동안 Fiddler로 다시 전환하고 `OnBeforeResponse` 함수에 저장한 사용자 지정 규칙의 주석 처리를 제거합니다. **파일** 및 **저장** 을 선택하여 규칙이 적용되도록 변경 내용을 저장합니다. 이 코드에서는 RA-GZRS 스토리지 계정에 대한 요청을 찾고, 경로에 샘플 파일의 이름이 포함되어 있는 경우 `503 - Service Unavailable` 응답 코드를 반환합니다.

샘플이 실행 중인 창에서 애플리케이션을 다시 시작하거나 해당 키를 눌러 샘플 파일을 다운로드하고, 보조 스토리지에서 가져온 것인지 확인합니다. 샘플을 다시 일시 중지하거나 프롬프트에서 대기할 수 있습니다.

### <a name="simulate-primary-endpoint-restoration"></a>기본 엔드포인트 복원 시뮬레이션

Fiddler에서 사용자 지정 규칙을 제거하거나 다시 주석으로 처리합니다. **파일** 및 **저장** 을 선택하여 규칙이 더 이상 적용되지 않도록 합니다.

샘플이 실행 중인 창에서 애플리케이션을 다시 시작하거나 해당 키를 눌러 샘플 파일을 다운로드하고, 다시 기본 스토리지에서 가져온 것인지 확인합니다. 그런 후 샘플을 종료해도 됩니다.

## <a name="next-steps"></a>다음 단계

시리즈 2부에서는 읽기 액세스 지역 중복 스토리지 오류를 시뮬레이션하는 방법에 대해 알아보았습니다.

RA-GZRS 스토리지의 작동 방식 및 관련 위험에 대해 자세히 알아보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [RA-GZRS를 사용하여 HA 앱 디자인](../common/geo-redundant-design.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md
