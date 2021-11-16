---
title: 자습서 - Microsoft 스크립트를 사용하여 Azure IoT Hub에 대한 x.509 테스트 인증서 만들기 | Microsoft Docs
description: 자습서 - 사용자 지정 스크립트를 사용하여 Azure IoT Hub에 대한 CA 및 디바이스 인증서 만들기
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 06/26/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 65d511cabda3595828db9926603b6eb20efd2e83
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549948"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>자습서: Microsoft에서 제공하는 스크립트를 사용하여 테스트 인증서 만들기

Microsoft는 사용자 고유의 X.509 인증서를 만들어 IoT Hub에 인증하는 방법을 이해하는 데 도움이 되는 PowerShell 및 Bash 스크립트를 제공합니다. 스크립트는 GitHub [리포지토리](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)에 있습니다. 데모용으로만 제공됩니다. 만든 인증서는 프로덕션에 사용할 수 없습니다. 인증서는 하드 코딩된 암호("1234")를 포함하며 30일 후에 만료됩니다. 프로덕션 환경의 경우 인증서 만들기 및 수명 관리를 위해 사용자 고유의 모범 사례를 사용해야 합니다.

## <a name="powershell-scripts"></a>PowerShell 스크립트

### <a name="step-1---setup"></a>1단계 - 설정

Windows용 OpenSSL을 가져옵니다. 다운로드하는 위치는 <https://www.openssl.org/docs/faq.html#MISC4>를 참조하고, 원본에서 빌드하려면 <https://www.openssl.org/source/>를 참조하세요. 그런 다음, 예비 스크립트를 실행합니다.

1. GitHub [리포지토리](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)의 스크립트를 작업하려는 로컬 디렉터리에 복사합니다. 모든 파일은 이 디렉터리의 자식으로 만들어집니다.

1. 관리자 권한으로 PowerShell을 시작합니다.

1. 스크립트를 로드한 디렉터리로 변경합니다.

1. 명령줄에서 `$ENV:OPENSSL_CONF` 환경 변수를 openssl 구성 파일(openssl.cnf)이 있는 디렉터리로 설정합니다.

1. PowerShell에서 스크립트를 실행할 수 있도록 `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`를 실행합니다.

1. `. .\ca-certs.ps1`을 실행합니다. 그러면 스크립트의 함수를 PowerShell 전역 네임스페이스로 가져옵니다.

1. `Test-CACertsPrerequisites`을 실행합니다. PowerShell은 Windows 인증서 저장소를 사용하여 인증서를 관리합니다. 이 명령은 나중에 기존 인증서와 이름이 충돌하지 않고 OpenSSL이 올바르게 설정되었는지 확인합니다.

### <a name="step-2---create-certificates"></a>2단계 - 인증서 만들기

`New-CACertsCertChain [ecc|rsa]`을 실행합니다. ECC는 CA 인증서에 권장되지만 반드시 필요한 것은 아닙니다. 이 스크립트는 다음 CA 및 중간 인증서를 사용하여 디렉터리 및 Windows 인증서 저장소를 업데이트합니다.

* intermediate1.pem
* intermediate2.pem
* intermediate3.pem
* RootCA.cer
* RootCA.pem

스크립트가 실행되면 새 CA 인증서(RootCA.pem)를 IoT Hub에 추가합니다.

1. IoT Hub, [인증서]로 차례로 이동합니다.

1. **추가** 를 선택합니다.

1. CA 인증서의 표시 이름을 입력합니다.

1. 소유 증명을 건너뛰려면 **업로드 시 인증서 상태를 확인으로 설정** 옆에 있는 상자를 선택합니다.

1. CA 인증서를 업로드합니다.

1. **저장** 을 선택합니다.

### <a name="optional-step-3---prove-possession"></a>(선택 사항) 3단계 - 소유 증명

업로드하는 동안 인증서를 자동으로 확인하도록 선택하지 않은 경우 소유를 수동으로 증명합니다.

1. 새 CA 인증서를 선택합니다.

1. **인증서 세부 정보** 대화 상자에서 **확인 코드 생성** 을 선택합니다. 자세한 내용은 [CA 인증서 소유 증명](tutorial-x509-prove-possession.md)을 참조하세요.

1. 확인 코드가 포함된 인증서를 만듭니다. 예를 들어 확인 코드가 `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`인 경우 다음을 실행하여 새 인증서를 `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` 주체가 포함된 작업 디렉터리에 만듭니다. 이 스크립트는 `VerifyCert4.cer`이라는 인증서를 만듭니다.

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. **인증서 세부 정보** 대화 상자에서 `VerifyCert4.cer`을 IoT Hub에 업로드합니다.

1. **확인** 을 선택합니다.

### <a name="step-4---create-a-new-device"></a>4단계 - 새 디바이스 만들기

IoT Hub에 대한 디바이스를 만듭니다.

1. IoT Hub에서 **IoT 디바이스** 섹션으로 이동합니다.

1. ID가 `mydevice`인 새 디바이스를 추가합니다.

1. 인증에 대해 **X.509 CA 서명됨** 을 선택합니다.

1. `New-CACertsDevice mydevice`를 실행하여 새 디바이스 인증서를 만듭니다. 그러면 다음 파일이 작업 디렉터리에 만들어집니다.

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>5단계 - 디바이스 인증서 테스트

[인증서 인증 테스트](tutorial-x509-test-certificate.md)로 이동하여 디바이스 인증서가 IoT Hub에 인증할 수 있는지 확인합니다. PFX 버전의 인증서(`mydevice.pfx`)가 필요합니다.

### <a name="step-6---cleanup"></a>6단계 - 정리

시작 메뉴에서 **컴퓨터 인증서 관리** 를 열고, **인증서 - 로컬 컴퓨터 > 개인** 으로 차례로 이동합니다. "Azure IoT CA TestOnly*"에서 발급한 인증서를 제거합니다. 마찬가지로 **>신뢰할 수 있는 루트 인증 기관 > 인증서 및 >중간 인증 기관 > 인증서** 에서 해당 인증서를 제거합니다.

## <a name="bash-scripts"></a>Bash 스크립트

### <a name="step-1---setup"></a>1단계 - 설정

1. Bash를 시작합니다.

1. 작업하려는 디렉터리로 변경합니다. 모든 파일은 이 디렉터리에서 생성됩니다.

1. `*.cnf` 및 `*.sh`를 작업 디렉터리에 복사합니다.

### <a name="step-2---create-certificates"></a>2단계 - 인증서 만들기

1. `./certGen.sh create_root_and_intermediate`을 실행합니다. 그러면 다음 파일이 **certs** 디렉터리에 만들어집니다.

    * azure-iot-test-only.chain.ca.cert.pem
    * azure-iot-test-only.intermediate.cert.pem
    * azure-iot-test-only.root.ca.cert.pem

1. IoT Hub, **인증서** 로 차례로 이동합니다.

1. **추가** 를 선택합니다.

1. CA 인증서의 표시 이름을 입력합니다.

1. CA 인증서만 IoT Hub에 업로드합니다. 인증서 이름은 `./certs/azure-iot-test-only.root.ca.cert.pem.`입니다.

1. **저장** 을 선택합니다.

### <a name="step-3---prove-possession"></a>3단계 - 소유 증명

1. 이전 단계에서 만든 새 CA 인증서를 선택합니다.

1. **인증서 세부 정보** 대화 상자에서 **확인 코드 생성** 을 선택합니다. 자세한 내용은 [CA 인증서 소유 증명](tutorial-x509-prove-possession.md)을 참조하세요.

1. 확인 코드가 포함된 인증서를 만듭니다. 예를 들어 확인 코드가 `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`인 경우 다음을 실행하여 `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` 주체가 포함된 `verification-code.cert.pem`이라는 새 인증서를 작업 디렉터리에 만듭니다.

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. **인증서 세부 정보** 대화 상자에서 인증서를 IoT 허브에 업로드합니다.

1. **확인** 을 선택합니다.

### <a name="step-4---create-a-new-device"></a>4단계 - 새 디바이스 만들기

IoT 허브에 대한 디바이스를 만듭니다.

1. IoT Hub에서 [IoT 디바이스] 섹션으로 이동합니다.

1. ID가 `mydevice`인 새 디바이스를 추가합니다.

1. 인증에 대해 **X.509 CA 서명됨** 을 선택합니다.

1. `./certGen.sh create_device_certificate mydevice`를 실행하여 새 디바이스 인증서를 만듭니다. 그러면 `new-device.cert.pem` 및 `new-device.cert.pfx`라는 두 개의 파일이 작업 디렉터리에 만들어집니다.

### <a name="step-5---test-your-device-certificate"></a>5단계 - 디바이스 인증서 테스트

[인증서 인증 테스트](tutorial-x509-test-certificate.md)로 이동하여 디바이스 인증서가 IoT Hub에 인증할 수 있는지 확인합니다. PFX 버전의 인증서(`new-device.cert.pfx`)가 필요합니다.

### <a name="step-6---cleanup"></a>6단계 - 정리

bash 스크립트는 단순히 인증서를 작업 디렉터리에 만들므로 테스트가 완료되면 해당 인증서를 삭제하기만 하면 됩니다.

## <a name="next-steps"></a>다음 단계

인증서를 테스트하려면 [인증서 인증 테스트](tutorial-x509-test-certificate.md)로 이동하여 인증서가 디바이스를 IoT Hub에 인증할 수 있는지 확인합니다.
