---
title: 개별 센서 관리
description: 활성화 파일 관리, 백업 수행, 독립 실행형 센서 업데이트를 포함하여 개별 센서를 관리하는 방법에 대해 알아봅니다.
ms.date: 05/26/2021
ms.topic: how-to
ms.openlocfilehash: 7c54c2eef98cdf3c68f4c03f09f95972d57aba71
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567119"
---
# <a name="manage-individual-sensors"></a>개별 센서 관리

이 문서에서는 개별 센서를 관리하는 방법을 설명합니다. 활성화 파일 관리, 백업 수행, 독립 실행형 센서 업데이트 등의 작업이 포함됩니다.

또한 여러 센서를 동시에 관리할 수 있는 온-프레미스 관리 콘솔에서 특정 센서 관리 작업을 수행할 수 있습니다.

센서 온보딩 및 등록에 Azure Portal을 사용합니다.

## <a name="manage-sensor-activation-files"></a>센서 활성화 파일 관리

센서는 Azure Portal의 Azure Defender for IoT로 온보딩되었습니다. 각 센서는 로컬로 연결된 센서 또는 클라우드 연결 센서로 온보딩되었습니다.

고유한 활성화 파일은 배포하는 각 센서에 업로드됩니다. 새 파일을 사용하는 시점 및 방법에 대한 자세한 내용은 [새 활성화 파일 업로드](#upload-new-activation-files)를 참조하세요. 파일을 업로드할 수 없으면 [활성화 파일 업로드 문제 해결](#troubleshoot-activation-file-upload)을 참조하세요.

### <a name="about-activation-files-for-locally-connected-sensors"></a>로컬로 연결된 센서의 활성화 파일 정보

로컬로 연결된 센서는 Azure 구독과 연결됩니다. 로컬로 연결된 센서의 활성화 파일에는 만료 날짜가 포함되어 있습니다. 이 날짜로부터 한 달 전에 센서 콘솔 상단에 경고 메시지가 표시됩니다. 경고 메시지는 활성화 파일을 업데이트할 때까지 유지됩니다.

:::image type="content" source="media/how-to-manage-individual-sensors/system-setting-screenshot.png" alt-text="시스템 설정의 스크린샷.":::

활성화 파일이 만료된 경우에도 Defender for IoT 기능을 계속 사용할 수 있습니다. 

### <a name="about-activation-files-for-cloud-connected-sensors"></a>클라우드 연결 센서에 대한 활성화 파일 정보

클라우드 연결 센서는 Defender for IoT 허브와 연결됩니다. 이러한 센서는 활성화 파일 기간의 영향을 받지 않습니다. 클라우드 연결 센서의 활성화 파일은 Defender for IoT 허브에 대한 연결을 확인하는 데 사용됩니다.

### <a name="upload-new-activation-files"></a>새 활성화 파일 업로드

다음과 같은 경우 온보딩된 센서에 대한 새 활성화 파일을 업로드해야 할 수 있습니다.

- 활성화 파일이 로컬로 연결된 센서에서 만료되는 경우. 

- 다른 센서 관리 모드에서 작업하려는 경우. 

- 클라우드 연결 센서에 새 Defender for IoT 허브를 할당하려는 경우

새 활성화 파일을 추가하려면 다음을 수행합니다.

1. **센서 관리** 페이지로 이동합니다.

2. 새 활성화 파일을 업로드하려는 센서를 선택합니다.

3. 삭제합니다.

4. 새 모드의 **온보딩** 페이지나 Defender for IoT 허브에서 센서를 다시 온보딩합니다.

5. **활성화 파일 다운로드** 페이지에서 활성화 파일을 다운로드합니다.

6. 파일을 저장합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/download-activation-file.png" alt-text="Defender for IoT에서 활성화 파일을 다운로드합니다.":::

7. Defender for IoT 센서 콘솔에 로그인합니다.

8. 센서 콘솔에서 **시스템 설정** >  **다시 활성화** 를 선택합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/reactivation.png" alt-text="시스템 설정 화면에서 다시 활성화 선택.":::

9. **업로드** 를 선택하고 저장한 파일을 선택합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/upload-the-file.png" alt-text="저장한 파일을 업로드합니다.":::

10. **활성화** 를 선택합니다.

### <a name="troubleshoot-activation-file-upload"></a>활성화 파일 업로드 문제 해결

활성화 파일을 업로드할 수 없는 경우 오류 메시지가 표시됩니다. 다음 이벤트가 발생했을 수 있습니다.

- **로컬로 연결된 센서**: 활성화 파일이 유효하지 않습니다. 파일이 유효하지 않으면 Defender for IoT 포털로 이동합니다. **센서 관리** 페이지에서 유효하지 않은 파일이 있는 센서를 선택하고 새 활성화 파일을 다운로드합니다.

- **클라우드 연결 센서**: 센서를 인터넷에 연결할 수 없습니다. 센서의 네트워크 구성을 확인합니다. 인터넷에 액세스하기 위해 웹 프록시를 통해 센서를 연결해야 하는 경우 **센서 네트워크 구성** 화면에서 프록시 서버가 올바르게 구성되어 있는지 확인합니다. \*.azure-devices.net:443이 방화벽 및/또는 프록시에서 허용되는지 확인합니다. 와일드카드가 지원되지 않거나 더 많은 제어를 원하는 경우 특정 Defender for IoT 허브에 대한 FQDN을 방화벽 및/또는 프록시에서 열어야 합니다. 자세한 내용은 [참조 - IoT Hub 엔드포인트](../../iot-hub/iot-hub-devguide-endpoints.md)를 참조하세요.  

- **클라우드 연결 센서의 경우**: 활성화 파일이 유효하지만 Defender for IoT가 해당 파일을 거부했습니다. 이 문제를 해결할 수 없는 경우 Defender for IoT 포털 사이트 및 센서 페이지에서 다른 활성화 파일을 다운로드할 수 있습니다. 그래도 문제가 해결되지 않을 경우 Microsoft 지원에 문의하세요.

## <a name="manage-certificates"></a>인증서 관리

센서 설치 후에는 로컬 자체 서명된 인증서가 생성되어 센서 웹 애플리케이션에 액세스하는 데 사용됩니다. 센서에 처음 로그인할 때 관리자 사용자에게 SSL/TLS 인증서를 제공하라는 메시지가 표시됩니다.  자세한 내용은 [로그인 및 센서 활성화](how-to-activate-and-set-up-your-sensor.md)를 참조하세요.

이 문서에서는 인증서 업데이트, 인증서 CLI 명령 작업, 지원되는 인증서 및 인증서 매개 변수에 대한 정보를 제공합니다.

### <a name="about-certificates"></a>인증서 정보

Azure Defender for IoT는 SSL/TLS 인증서를 사용하여 다음을 수행합니다.

- CA 서명 인증서를 업로드하여 조직에서 요청하는 특정 인증서 및 암호화 요구 사항을 충족합니다.

- 관리 콘솔과 연결된 센서 간 그리고 관리 콘솔과 고가용성 관리 콘솔 간의 유효성 검사를 허용합니다. 유효성 검사에서는 CRL(인증서 해지 목록) 및 인증서 만료 날짜에 대한 평가가 진행됩니다. 유효성 검사에 실패하면 관리 콘솔과 센서 간의 통신이 중단되고 콘솔에 유효성 검사 오류가 표시됩니다. 이 옵션은 설치 후 기본적으로 사용하도록 설정됩니다.

- Syslog, Splunk 또는 ServiceNow로 전달되는 경고 정보 등 타사 전달 규칙 및 Active Directory와의 통신의 유효성은 검사합니다.

### <a name="about-crl-servers"></a>CRL 서버 정보

유효성 검사가 켜져 있으면 어플라이언스는 인증서에 정의된 CRL 서버에 대한 연결을 설정할 수 있어야 합니다. 기본적으로 인증서는 HTTP 포트 80에서 CRL URL을 참조합니다. 일부 조직의 보안 정책은 이 포트에 대한 액세스를 차단할 수 있습니다. 조직에 포트 80에 대한 액세스 권한이 없는 경우 다음을 수행할 수 있습니다.
1. 인증서에 다른 URL과 특정 포트를 정의합니다. 
- URL은 https://가 아닌 http://로 정의해야 합니다.
- 대상 CRL 서버가 정의한 포트에서 수신 대기할 수 있는지 확인합니다. 
1. 포트 80에서 CRL에 액세스할 프록시 서버를 사용합니다.
1. CRL 유효성 검사를 수행하지 마세요. 이 경우 인증서에서 CRL URL 참조를 제거합니다.

### <a name="about-ssltls-certificates"></a>SSL/TLS 인증서 정보

Defender for IoT 센서 및 온-프레미스 관리 콘솔은 다음 기능에 SSL 및 TLS 인증서를 사용합니다.

 - 사용자와 어플라이언스의 웹 콘솔 간 보안 통신.
 
 - 센서와 온-프레미스 관리 콘솔의 REST API에 대한 보안 통신.
 
 - 센서와 온-프레미스 관리 콘솔 간의 보안 통신.

설치가 완료되면 어플라이언스는 웹 콘솔에 대한 사전 액세스를 허용하는 로컬 자체 서명된 인증서를 생성합니다.

 > [!NOTE]
 > 어플라이언스가 세션의 클라이언트이자 개시 장치인 통합 및 전달 규칙의 경우 특정 인증서가 사용되며 시스템 인증서와 관련이 없습니다.  
 >
 >이러한 경우 인증서는 일반적으로 서버에서 수신되거나, 통합 설정을 위해 특정 인증서가 제공되는 비대칭 암호화를 사용합니다.

어플라이언스는 고유한 인증서 파일을 사용할 수 있습니다. 업로드한 인증서를 교체하려면 다음을 수행합니다.

- 버전 10.0 이후의 경우 인증서는 시스템 설정 메뉴에서 바꿀 수 있습니다.

- 버전 10.0 이전의 경우 명령줄 도구를 사용하여 SSL 인증서를 바꿀 수 있습니다.

### <a name="update-certificates"></a>인증서 업데이트

센서 관리자가 인증서를 업데이트할 수 있습니다.

인증서를 업데이트하려면 다음을 수행합니다.  

1. **시스템 설정** 을 선택합니다.

1. **SSL/TLS 인증서** 를 선택합니다.
1. 인증서를 삭제하거나 편집하고 새 인증서를 추가합니다.

    - 인증서 이름을 추가합니다.
    
    - CRT 파일 및 키 파일을 업로드하고 암호를 입력합니다.
    - 필요한 경우 PEM 파일을 업로드합니다.

유효성 검사 설정을 변경하려면 다음을 수행합니다.

1. **인증서 유효성 검사 사용** 토글을 설정 또는 해제합니다.

1. **저장** 을 선택합니다.

이 옵션을 사용하도록 설정하고 유효성 검사에 실패하면 관리 콘솔과 센서 간의 통신이 중단되고 콘솔에 유효성 검사 오류가 표시됩니다.

### <a name="certificate-support"></a>인증서 지원

다음 인증서가 지원됩니다.

- 프라이빗 및 엔터프라이즈 키 인프라(프라이빗 PKI)

- 퍼블릭 키 인프라(퍼블릭 PKI) 

- 어플라이언스에서 로컬로 생성된 인증서(로컬에서 자체 서명됨). 

> [!IMPORTANT]
> 자체 서명된 인증서는 사용하지 않는 것을 권장합니다. 이 유형의 연결은 안전하지 않으며 테스트 환경에만 사용해야 합니다. 자체 서명된 인증서는 인증서 소유자의 유효성을 검사할 수 없고 시스템의 보안을 유지할 수 없으므로 프로덕션 네트워크에서는 사용할 수 없습니다.

### <a name="supported-ssl-certificates"></a>지원되는 SSL 인증서 

지원되는 매개 변수는 다음과 같습니다. 

**인증서 CRT**

- 도메인 이름에 대한 기본 인증서 파일

- 서명 알고리즘 = SHA256RSA
- 서명 해시 알고리즘 = SHA256
- 유효 개시 시점 = 유효한 지난 날짜
- 유효 종료 시점 = 유효한 미래 날짜
- 공개 키 = RSA 2,048비트(최소) 또는 4,096비트
- CRL 배포 지점 = .crl 파일의 URL
- 주체 CN = URL은 예를 들면, Sensor.contoso.<span>com, 또는 *.contoso.<span>com과 같은 와일드카드 인증서일 수 있습니다.
- 주체 (C)ountry = 정의됨(예: US)
- 주체 (OU) Org Unit = 정의됨(예: Contoso Labs)
- 주체 (O)rganization = 정의됨(예: Contoso Inc)

**키 파일**

- CSR을 만들 때 생성된 키 파일입니다.

- RSA 2,048비트(최소) 또는 4,096비트입니다.

 > [!Note]
 > 키 길이 4,096비트를 사용하는 경우
 > - 각 연결이 시작될 때 SSL 핸드셰이크가 느려집니다.  
 > - 핸드셰이크 중 CPU 사용량이 증가합니다. 

**인증서 체인**

- CA에서 제공한 중간 인증서 파일(있는 경우)

- 서버의 인증서를 발급한 CA 인증서가 파일의 첫 번째에 있어야 하며 그다음으로 루트까지 다른 인증서가 있어야 합니다. 
- 모음 특성을 포함할 수 있습니다.

**암호**

- 하나의 키가 지원됩니다.

- 인증서를 가져올 때 설정합니다.

다른 매개 변수를 사용하는 인증서도 작동할 수 있지만 Microsoft에서 지원하지 않습니다.

#### <a name="encryption-key-artifacts"></a>암호화 키 아티팩트

**.pem – 인증서 컨테이너 파일**

PEM(Privacy Enhanced Mail) 파일은 메일을 보호하는 데 사용되는 일반 파일 형식이었습니다. 오늘날 PEM 파일은 인증서와 함께 사용되며 x509 ASN1 키를 사용합니다.  

컨테이너 파일은 RFC 1421부터 1424 사이에 정의되며, 퍼블릭 인증서만 포함할 수 있는 컨테이너 형식입니다. 예를 들어 Apache 설치, CA 인증서, 파일, ETC, SSL 또는 인증서가 있습니다. 여기에는 공개 키, 프라이빗 키 및 루트 인증서를 포함한 전체 인증서 체인이 포함될 수 있습니다.  

또한 CSR을 PKCS10 형식으로 인코딩할 수 있으며, PEM으로 변환될 수 있습니다.

**.cert .cer .crt – 인증서 컨테이너 파일**

다른 확장명으로 포맷된 `.pem` 또는 `.der` 파일입니다. 해당 파일은 Windows 탐색기에서 인증서로 인식됩니다. `.pem` 파일은 Windows 탐색기에서 인식할 수 없습니다.

**.key – 프라이빗 키 파일**

키 파일은 PEM 파일과 동일한 형식이지만 확장명이 다릅니다.

#### <a name="other-commonly-available-key-artifacts"></a>기타 일반적으로 사용 가능한 키 아티팩트

**.csr - 인증서 서명 요청**.  

이 파일은 인증 기관에 전송하는 데 사용됩니다. 실제 형식은 RFC 2986에 정의된 PKCS10이며, 요청된 인증서의 일부 또는 모든 주요 세부 정보를 포함할 수 있습니다. 예를 들어 주체, 조직 및 상태입니다. CA에서 서명하는 인증서의 공개 키이며 반환되는 인증서를 수신합니다.  

반환된 인증서는 공개 키를 포함하지만 프라이빗 키가 포함되지 않은 퍼블릭 인증서입니다. 

**.pkcs12 .pfx .p12 - 암호 컨테이너**. 

원래 PKCS(공개 키 암호 표준)에서 RSA에 의해 정의된 12 변형은 원래 Microsoft에서 향상되었으며 나중에 RFC 7292로 제출되었습니다.  

이 컨테이너 형식에는 퍼블릭 및 프라이빗 인증서 쌍을 모두 포함하는 암호가 필요합니다. `.pem`  파일과 달리 이 컨테이너는 완전히 암호화됩니다.  

OpenSSL을 사용하여 이 파일을 퍼블릭 키와 프라이빗 키가 모두 포함된 `.pem`  파일로 전환할 수 있습니다. `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**.der – 이진 인코딩 PEM**.

ASN.1 구문은 Base64 인코딩 `.der` 파일인 `.pem`  파일을 통해 이진으로 인코딩합니다. 

OpenSSL은 이러한 파일을 `.pem`으로 변환할 수 있습니다. `openssl x509 -inform der -in to-convert.der -out converted.pem`  

Windows에서 해당 파일을 인증서 파일로 인식합니다. 기본적으로 Windows는 인증서를 다른 확장명으로 포맷된 `.der` 파일로 내보냅니다.  

**.crl - 인증서 해지 목록**.  
인증 기관은 인증서가 만료되기 전에 인증서의 권한을 해제하는 방법으로 이러한 파일을 생성합니다.
 
##### <a name="cli-commands"></a>CLI 명령

`cyberx-xsense-certificate-import` CLI 명령을 사용하여 인증서를 가져옵니다. 이 도구를 사용하려면 WinSCP 또는 Wget과 같은 도구를 사용하여 인증서 파일을 디바이스에 업로드해야 합니다.

이 명령은 다음 입력 플래그를 지원합니다.

- `-h`: 명령줄 도움말 구문을 표시합니다.

- `--crt`: 인증서 파일(.crt 확장명)의 경로입니다.

- `--key`:  \*.key 파일입니다. 키 길이는 최소 2,048비트 이상이어야 합니다.

- `--chain`: 인증서 체인 파일의 경로입니다(선택 사항).

- `--pass`: 인증서를 암호화하는 데 사용되는 암호입니다(선택 사항).

- `--passphrase-set`: 기본값 = `False`, 사용되지 않음. 이전 인증서와 함께 제공된 이전 암호를 사용하려면 `True`로 설정합니다(선택 사항).

CLI 명령을 사용하는 경우

- 인증서 파일을 어플라이언스에서 읽을 수 있는지 확인합니다.

- 인증서의 도메인 이름과 IP가 IT 부서가 계획한 구성과 일치하는지 확인합니다.

### <a name="use-openssl-to-manage-certificates"></a>OpenSSL을 사용하여 인증서 관리

다음 명령을 사용하여 인증서를 관리합니다.

| Description | CLI 명령 |
|--|--|
| 새 프라이빗 키 및 인증서 서명 요청 생성 | `openssl req -out CSR.csr -new -newkey rsa:2048 -nodes -keyout privateKey.key` |
| 자체 서명된 인증서 생성 | `openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt` |
| 기존 프라이빗 키에 대한 CSR(인증서 서명 요청)을 생성합니다. | `openssl req -out CSR.csr -key privateKey.key -new` |
| 기존 인증서를 기반으로 인증서 서명 요청 생성 | `openssl x509 -x509toreq -in certificate.crt -out CSR.csr -signkey privateKey.key` |
| 프라이빗 키에서 암호 제거 | `openssl rsa -in privateKey.pem -out newPrivateKey.pem` |

인증서, CSR 또는 프라이빗 키 내의 정보를 확인해야 하는 경우 다음 명령을 사용합니다.

| Description | CLI 명령 |
|--|--|
| CSR(인증서 서명 요청) 확인 | `openssl req -text -noout -verify -in CSR.csr` |
| 프라이빗 키 확인 | `openssl rsa -in privateKey.key -check` |
| 인증서 확인 | `openssl x509 -in certificate.crt -text -noout`  |

프라이빗 키가 인증서와 일치하지 않거나 사이트에 설치한 인증서를 신뢰할 수 없다는 오류가 표시되면 다음 명령을 사용하여 오류를 해결합니다.

| Description | CLI 명령 |
|--|--|
| 공개 키의 MD5 해시를 확인하여 CSR 또는 프라이빗 키에 있는 것과 일치하는지 확인 | 1. `openssl x509 -noout -modulus -in certificate.crt | openssl md5` <br /> 2. `openssl rsa -noout -modulus -in privateKey.key | openssl md5` <br /> 3. `openssl req -noout -modulus -in CSR.csr | openssl md5 ` |

인증서와 키를 다른 형식으로 변환하여 특정 유형의 서버 또는 소프트웨어와 호환되도록 하려면 다음 명령을 사용합니다.

| Description | CLI 명령 |
|--|--|
| DER 파일(.crt .cer .der)을 PEM으로 변환  | `openssl x509 -inform der -in certificate.cer -out certificate.pem`  |
| PEM 파일을 DER로 변환 | `openssl x509 -outform der -in certificate.pem -out certificate.der`  |
| 프라이빗 키와 인증서를 포함하는 PKCS#12 파일(.pfx .p12)을 PEM으로 변환 | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes` <br />`-nocerts`를 추가하여 프라이빗 키만 출력하거나 `-nokeys`를 추가하여 인증서만 출력할 수 있습니다. |
| PEM 인증서 파일 및 프라이빗 키를 PKCS#12(.pfx .p12)로 변환합니다. | `openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt` |

## <a name="connect-a-sensor-to-the-management-console"></a>관리 콘솔에 센서 연결

이 섹션에서는 센서와 온-프레미스 관리 콘솔 간의 연결을 확인하는 방법을 설명합니다. 개별 네트워크에서 작업 중이며 센서에서 관리 콘솔로 디바이스 및 경고 정보를 보내려는 경우 이 작업을 수행해야 합니다. 이 연결을 통해 관리 콘솔에서 센서에 시스템 설정을 푸시하고 센서에서 다른 관리 작업을 수행할 수도 있습니다.

연결하려면 다음을 수행합니다.

1. 온-프레미스 관리 콘솔에 로그인합니다.

2. **시스템 설정** 을 선택합니다.

3. **센서 설정 – 연결 문자열** 섹션에서 자동으로 생성된 연결 문자열을 복사합니다.

   :::image type="content" source="media/how-to-manage-individual-sensors/connection-string-screen.png" alt-text="이 화면에서 연결 문자열을 복사합니다."::: 

4. 센서 콘솔에 로그인합니다.

5. 왼쪽 창에서 **시스템 설정** 을 선택합니다.

6. **관리 콘솔 연결** 을 선택합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/management-console-connection-screen.png" alt-text="관리 콘솔 연결 대화 상자의 스크린샷.":::

7. **연결 문자열** 상자에 연결 문자열을 붙여넣고 **연결** 을 선택합니다.

8. 온-프레미스 관리 콘솔의 **사이트 관리** 창에서 센서를 영역에 할당합니다.

## <a name="change-the-name-of-a-sensor"></a>센서 이름 변경

센서 콘솔의 이름을 변경할 수 있습니다. 새 이름이 콘솔 웹 브라우저, 다양한 콘솔 창, 문제 해결 로그에 표시됩니다.

센서 이름을 변경하는 프로세스는 로컬로 연결된 센서 및 클라우드 연결 센서에 따라 다릅니다. 기본 이름은 **센서** 입니다.

### <a name="change-the-name-of-a-locally-connected-sensor"></a>로컬로 연결된 센서의 이름 변경

이름을 변경하는 방법은 다음과 같습니다.

1. 콘솔 왼쪽 창의 맨 아래에서 현재 센서 레이블을 선택합니다.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/label-name.png" alt-text="센서 레이블을 표시하는 스크린샷.":::

1. **센서 이름 편집** 대화 상자에서 이름을 입력합니다.

1. **저장** 을 선택합니다. 새 이름이 적용됩니다.

### <a name="change-the-name-of-a-cloud-connected-sensor"></a>클라우드 연결 센서의 이름 변경

센서가 클라우드 연결 센서로 등록된 경우 센서 이름은 등록하는 동안 할당된 이름으로 정의됩니다. 이 이름은 처음으로 로그인할 때 업로드한 활성화 파일에 포함됩니다. 센서의 이름을 변경하려면 새 활성화 파일을 업로드해야 합니다.

이름을 변경하는 방법은 다음과 같습니다.

1. Azure Defender for IoT 포털에서 사이트 및 센서 페이지로 이동합니다.

1. 사이트 및 센서 페이지에서 센서를 삭제합니다.

1. 시작 페이지에서 **온보딩 센서** 를 선택하여 새 이름으로 등록합니다.

1. 새 활성화 파일을 다운로드합니다.

1. Defender for IoT 센서 콘솔에 로그인합니다.

1. 센서 콘솔에서 **시스템 설정** 을 선택한 다음 **다시 활성화** 를 선택합니다.

   :::image type="content" source="media/how-to-manage-sensors-on-the-cloud/reactivate.png" alt-text="활성화 파일을 업로드하여 센서를 다시 활성화합니다.":::

1. **업로드** 를 선택하고 저장한 파일을 선택합니다.

1. **활성화** 를 선택합니다.

## <a name="update-the-sensor-network-configuration"></a>센서 네트워크 구성 업데이트

센서 네트워크 구성은 센서 설치 중에 정의되었습니다. 구성 매개 변수를 변경할 수 있습니다. 프록시 구성을 설정할 수도 있습니다.

새 IP 주소를 만드는 경우 다시 로그인해야 할 수 있습니다.

구성을 변경하려면 다음을 수행합니다.

1. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

2. **설정** 창에서 **네트워크** 를 선택합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/edit-network-configuration-screen.png" alt-text="네트워크 설정을 구성합니다.":::

3. 매개 변수 설정:

    | 매개 변수 | Description |
    |--|--|
    | IP 주소 | 센서 IP 주소 |
    | 서브넷 마스크 | 마스크 주소 |
    | 기본 게이트웨이 | 기본 게이트웨이 주소 |
    | DNS | DNS 서버 주소 |
    | 호스트 이름 | 센서 호스트 이름 |
    | Proxy (프록시) | 프록시 호스트 및 포트 이름 |

4. **저장** 을 선택합니다.

## <a name="synchronize-time-zones-on-the-sensor"></a>센서의 표준 시간대를 동기화

모든 사용자에게 동일한 시간과 지역이 표시되도록 센서의 시간 및 지역을 구성할 수 있습니다.

:::image type="content" source="media/how-to-manage-individual-sensors/time-and-region.png" alt-text="시간 및 지역을 구성합니다.":::

| 매개 변수 | Description |
|--|--|
| 표준 시간대 | 다음에 대해 표준 시간대를 정의합니다.<br />- 경고<br />- 동향 및 통계 위젯<br />- 데이터 마이닝 보고서<br />   \- 위험 평가 보고서<br />- 공격 벡터 |
| 날짜 형식 | 다음 형식 옵션 중 하나를 선택합니다.<br />- dd/MM/yyyy HH:mm:ss<br />- MM/dd/yyyy HH:mm:ss<br />- yyyy/MM/dd HH:mm:ss |
| 날짜 및 시간 | 현재 날짜와 현지 시간을 선택한 형식으로 표시합니다.<br />예를 들어 실제 위치가 미국 및 뉴욕에 있지만 표준 시간대가 유럽 및 베를린으로 설정된 경우, 베를린 현지 시각에 따라 시간이 표시됩니다. |

센서 시간을 구성하려면 다음을 수행합니다.

1. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

2. **시스템 설정** 창에서 **시간 및 지역** 을 선택합니다.

3. 매개 변수를 설정하고 **저장** 을 선택합니다.

## <a name="set-up-backup-and-restore-files"></a>백업 설정 및 파일 복원

시스템 백업은 매일 오전 3:00에 자동으로 수행됩니다. 데이터는 센서의 다른 디스크에 저장됩니다. 기본 위치는 `/var/cyberx/backups`입니다.

이 파일을 내부 네트워크에 자동으로 전송할 수 있습니다.

> [!NOTE]
> - 백업 및 복원 프로시저는 동일한 버전에서만 수행할 수 있습니다.
> - 일부 아키텍처에서는 백업을 사용할 수 없습니다. `/var/cyberx/properties/backup.properties` 파일에서 사용하도록 설정할 수 있습니다.

온-프레미스 관리 콘솔을 사용하여 센서를 제어하는 경우, 센서의 백업 일정을 사용하여 이러한 백업을 수집하고 관리 콘솔 또는 외부 백업 서버에 저장할 수 있습니다.

**백업되는 항목**: 구성 및 데이터.

**백업되지 않는 항목:** PCAP 파일 및 로그. PCAP 및 로그를 수동으로 백업하고 복원할 수 있습니다.

센서 백업 파일은 `<sensor name>-backup-version-<version>-<date>.tar` 형식으로 이름이 자동 지정됩니다. 예제는 `Sensor_1-backup-version-2.6.0.102-2019-06-24_09:24:55.tar`입니다.

백업을 구성하려면 다음을 수행합니다.

- 관리 계정에 로그인하고 `$ sudo cyberx-xsense-system-backup`을 입력합니다.

최신 백업 파일을 복원하려면 다음을 수행합니다.

- 관리 계정에 로그인하고 `$ sudo cyberx-xsense-system-restore`를 입력합니다.

백업을 외부 SMB 서버에 저장하려면 다음을 수행합니다.

1. 외부 SMB 서버에 공유 폴더를 만듭니다.

    SMB 서버에 액세스하는 데 필요한 폴더 경로, 사용자 이름, 암호를 가져옵니다.

2. 센서에서 백업용 디렉터리를 만듭니다.

    - `sudo mkdir /<backup_folder_name_on_cyberx_server>`

    - `sudo chmod 777 /<backup_folder_name_on_cyberx_server>/`

3. 편집 `fstab`:

    - `sudo nano /etc/fstab`

    - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_cyberx_server> cifsrw,credentials=/etc/samba/user,vers=X.X,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0`

4. SMB 서버에 대해 공유할 자격 증명을 편집하고 만듭니다.

    `sudo nano /etc/samba/user` 

5. 추가:

    - `username=&gt:user name&lt:`

    - `password=<password>`

6. 디렉터리를 탑재합니다.

    `sudo mount -a`

7. Defender for IoT 센서의 공유 폴더에 백업 디렉터리를 구성합니다.  

    - `sudo nano /var/cyberx/properties/backup.properties`

    - `set backup_directory_path to <backup_folder_name_on_cyberx_server>`

### <a name="restore-sensors"></a>센서 복원

센서 콘솔에서 백업을 복원할 수 있으며 CLI를 사용해서 복원할 수도 있습니다.

**콘솔에서 복원하려면 다음을 수행합니다.**

- 센서의 **시스템 설정** 창에서 **이미지 복원** 을 선택합니다.

:::image type="content" source="media/how-to-manage-individual-sensors/restore-image-screen.png" alt-text="단추를 선택하여 이미지를 복원합니다.":::

콘솔에 복원 오류가 표시됩니다.

**CLI를 사용하여 복원하려면 다음을 수행합니다.**

- 관리 계정에 로그인하고 `$ sudo cyberx-management-system-restore`를 입력합니다.

## <a name="update-a-standalone-sensor-version"></a>독립 실행형 센서 버전 업데이트

다음 프로시저에서는 센서 콘솔을 사용하여 독립 실행형 센서를 업데이트하는 방법을 설명합니다. 업데이트 프로세스는 30분 정도 걸립니다.

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.

2. Defender for IoT로 이동합니다.

3. **업데이트** 페이지로 이동합니다.

   :::image type="content" source="media/how-to-manage-individual-sensors/updates-page.png" alt-text="Defender for IoT 업데이트 페이지의 스크린샷.":::

4. **센서** 섹션에서 **다운로드** 를 선택하고 파일을 저장합니다.

5. 센서 콘솔의 사이드바에서 **시스템 설정** 을 선택합니다.

6. **버전 업데이트** 창에서 **업데이트** 를 선택합니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/upgrade-pane-v2.png" alt-text="업데이트 창의 스크린샷.":::

7. Defender for IoT **업데이트** 페이지에서 다운로드한 파일을 선택합니다.

8. 업데이트 프로세스가 시작되고 그동안 시스템이 두 번 다시 부팅됩니다. 업데이트 프로세스가 완료되기 전에 첫 번째 다시 부팅한 후에는 로그인 창과 함께 시스템이 열립니다. 로그인하면 업그레이드 버전이 사이드바의 왼쪽 아래에 나타납니다.

    :::image type="content" source="media/how-to-manage-individual-sensors/defender-for-iot-version.png" alt-text="로그인한 후 표시되는 업그레이드 버전의 스크린샷.":::

## <a name="forward-sensor-failure-alerts"></a>센서 오류 경고 전달

타사에 경고를 전달하여 다음에 대한 정보를 제공할 수 있습니다.

- 센서 연결 해제

- 원격 백업 실패

이 정보는 시스템 알림에 대한 전달 규칙을 만들 때 전송됩니다.

> [!NOTE]
> 관리자는 시스템 알림을 보낼 수 있습니다.

알림을 보내려면 다음을 수행합니다.

1. 온-프레미스 관리 콘솔에 로그인합니다.
1. 측면 메뉴에서 **전달** 을 선택합니다.
1. 전달 규칙을 만듭니다.
1. **보고서 시스템 알림** 을 선택합니다.

전달 규칙에 대한 자세한 내용은 [전달 경고 정보](how-to-forward-alert-information-to-partners.md)를 참조하세요.

## <a name="adjust-system-properties"></a>시스템 속성 조정

시스템 속성은 센서의 다양한 작업 및 설정을 제어합니다. 이러한 항목을 편집하거나 수정하면 센서 콘솔 작업이 손상될 수 있습니다.

설정을 변경하기 전에 [Microsoft 지원](https://support.microsoft.com/)에 문의하세요.

시스템 속성에 액세스하려면 다음을 수행합니다.

1. 온-프레미스 관리 콘솔 또는 센서에 로그인합니다.

2. **시스템 설정** 을 선택합니다.

3. **일반** 섹션에서 **시스템 속성** 을 선택합니다.

## <a name="see-also"></a>참고 항목

[위협 인텔리전스 리서치 및 패키지](how-to-work-with-threat-intelligence-packages.md)

[관리 콘솔에서 센서 관리](how-to-manage-sensors-from-the-on-premises-management-console.md)