---
title: 온-프레미스 관리 콘솔 관리
description: 백업 및 복원과 같은 온-프레미스 관리 콘솔 옵션, 호스트 이름 정의, 센서에 대한 프록시 설정에 대해 알아봅니다.
ms.date: 1/12/2021
ms.topic: article
ms.openlocfilehash: 369bd751d2b21fc9cdf824b2bd56f20b47f2819d
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531086"
---
# <a name="manage-the-on-premises-management-console"></a>온-프레미스 관리 콘솔 관리

이 문서에서는 백업 및 복원, 커밋된 디바이스 활성화 파일 다운로드, 인증서 업데이트, 센서에 프록시 설정과 같은 온-프레미스 관리 콘솔 옵션에 대해 설명합니다.

Azure Portal에서 온-프레미스 관리 콘솔을 온보딩합니다.

## <a name="upload-an-activation-file"></a>활성화 파일 업로드

처음 로그인하면 온-프레미스 관리 콘솔 활성화 파일이 다운로드됩니다. 이 파일에는 온보딩 프로세스 중에 정의된 집계 커밋된 디바이스가 포함되어 있습니다. 목록에는 여러 구독에 연결된 센서가 포함되어 있습니다.

초기 활성화 후 모니터링되는 디바이스 수가 온보딩 중에 정의된 커밋된 디바이스 수를 초과할 수 있습니다. 예를 들어 더 많은 센서를 관리 콘솔에 연결하는 경우 이 이벤트가 발생할 수 있습니다. 모니터링된 디바이스 수와 커밋된 디바이스 수가 일치하지 않으면 관리 콘솔에 경고가 표시됩니다. 이 이벤트가 발생하면 새 활성화 파일을 업로드해야 합니다.

활성화 파일을 업로드하려면:

1. Azure Defender for IoT **가격 책정** 페이지로 이동합니다.
1. **관리 콘솔 탭의 활성화 파일 다운로드** 를 선택하면 활성화 파일이 다운로드됩니다.

   :::image type="content" source="media/how-to-manage-sensors-from-the-on-premises-management-console/cloud_download_opm_activation_file.png" alt-text="활성화 파일 다운로드.":::

1. 관리 콘솔에서 **시스템 설정** 을 선택합니다.
1. **활성화** 를 선택합니다.
1. **파일 선택** 을 선택하고 저장한 파일을 선택합니다.

## <a name="manage-certificates"></a>인증서 관리

온-프레미스 관리 콘솔을 설치한 후 로컬 자체 서명된 인증서가 생성되어 관리 콘솔의 웹 애플리케이션에 액세스하는 데 사용됩니다. 관리자가 관리 콘솔에 처음으로 로그인하면 SSL/TLS 인증서를 제공하라는 메시지가 표시됩니다. 최초 설정에 대한 자세한 내용은 [온-프레미스 관리 콘솔 활성화 및 설정](how-to-activate-and-set-up-your-on-premises-management-console.md)을 참조하세요.

다음 섹션에서는 인증서 업데이트, 인증서 CLI 명령 사용, 지원되는 인증서 및 인증서 매개 변수에 대한 정보를 제공합니다.

### <a name="about-certificates"></a>인증서 정보

Azure Defender for IoT는 SSL 및 TLS 인증서를 사용하여 다음을 수행합니다.

- CA 서명 인증서를 업로드하여 조직에서 요청하는 특정 인증서 및 암호화 요구 사항을 충족합니다.

- 관리 콘솔과 연결된 센서 간, 관리 콘솔과 고가용성 관리 콘솔 간의 유효성 검사를 허용합니다. 유효성 검사는 인증서 해지 목록 및 인증서 만료 날짜에 대해 평가됩니다. 유효성 검사에 실패하면 관리 콘솔과 센서 간의 통신이 중단되고 콘솔에 유효성 검사 오류가 표시됩니다. 이 옵션은 설치 후 기본적으로 사용하도록 설정됩니다.

   유효성 검사가 `ON`이면 어플라이언스는 인증서에 정의된 CRL 서버에 대한 연결을 설정할 수 있어야 합니다.

타사 전달 규칙의 유효성은 검사되지 않습니다. 예제는 SYSLOG, Splunk 또는 ServiceNow로 전송되는 경고 정보 및 Active Directory와의 통신입니다.

#### <a name="ssl-certificates"></a>SSL 인증서

Defender for IoT 센서 및 온-프레미스 관리 콘솔은 다음 기능에 SSL 및 TLS 인증서를 사용합니다. 

 - 사용자와 어플라이언스의 웹 콘솔 간 보안 통신. 
 
 - 센서와 온-프레미스 관리 콘솔의 REST API에 대한 보안 통신.
 
 - 센서와 온-프레미스 관리 콘솔 간의 보안 통신. 

설치가 완료되면 어플라이언스는 웹 콘솔에 대한 사전 액세스를 허용하는 로컬 자체 서명된 인증서를 생성합니다. [`cyberx-xsense-certificate-import`](#cli-commands) 명령줄 도구를 사용하여 엔터프라이즈 SSL 및 TLS 인증서를 설치할 수 있습니다. 

 > [!NOTE]
 > 어플라이언스가 세션의 클라이언트이자 개시 장치인 통합 및 전달 규칙의 경우 특정 인증서가 사용되며 시스템 인증서와 관련이 없습니다.  
 >
 >이러한 경우 인증서는 일반적으로 서버에서 수신되거나, 통합 설정을 위해 특정 인증서가 제공되는 비대칭 암호화를 사용합니다. 

### <a name="update-certificates"></a>인증서 업데이트

온-프레미스 관리 콘솔의 관리자가 인증서를 업데이트할 수 있습니다.

인증서를 업데이트하려면 다음을 수행합니다.  

1. **시스템 설정** 을 선택합니다.

1. **SSL/TLS 인증서** 를 선택합니다.
1. 인증서를 삭제하거나 편집하고 새 인증서를 추가합니다.
   
   - 인증서 이름을 추가합니다.
   
   - CRT 파일 및 키 파일을 업로드하고 암호를 입력합니다.
   - 필요한 경우 PEM 파일을 업로드합니다.

유효성 검사 설정을 변경하려면 다음을 수행합니다.

1. **인증서 유효성 검사 사용** 토글을 설정하거나 해제합니다.

1. **저장** 을 선택합니다.

이 옵션을 사용하도록 설정하고 유효성 검사에 실패하면 관리 콘솔과 센서 간의 통신이 중단되고 콘솔에 유효성 검사 오류가 표시됩니다.

### <a name="certificate-support"></a>인증서 지원

다음 인증서가 지원됩니다.

- 프라이빗 및 엔터프라이즈 키 인프라(프라이빗 PKI)
 
- 공개 키 인프라(공개 PKI) 

- 어플라이언스에서 로컬로 생성됨(로컬로 자체 서명됨) 

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
- 주체 CN = URL은 예를 들면, Sensor.contoso.<span>com 또는 *.contoso.<span>com과 같은 와일드카드 인증서일 수 있습니다.
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

- 서버의 인증서를 발급한 CA 인증서가 파일의 첫 번째에 있어야 하며 그 다음으로 루트까지 다른 인증서가 있어야 합니다. 
- 체인은 모음 특성을 포함할 수 있습니다.

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

OpenSSL을 사용하여 이 파일을 공개 키와 프라이빗 키가 모두 포함된 `.pem`  파일로 전환할 수 있습니다. `openssl pkcs12 -in file-to-convert.p12 -out converted-file.pem -nodes`  

**.der – 이진 인코딩 PEM**.

ASN.1 구문은 Base64 인코딩 `.der` 파일인 `.pem`  파일을 통해 이진으로 인코딩합니다. 

OpenSSL은 이러한 파일을 `.pem`으로 변환할 수 있습니다. `openssl x509 -inform der -in to-convert.der -out converted.pem`  

Windows에서 해당 파일을 인증서 파일로 인식합니다. 기본적으로 Windows는 인증서를 다른 확장명으로 포맷된 `.der` 파일로 내보냅니다.

**.crl - 인증서 해지 목록**.  

인증 기관은 인증서가 만료되기 전에 인증서의 권한을 해제하는 방법으로 이러한 파일을 생성합니다. 

#### <a name="cli-commands"></a>CLI 명령

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

## <a name="define-backup-and-restore-settings"></a>백업 및 복원 설정 정의

온-프레미스 관리 콘솔 시스템 백업은 매일 자동으로 수행됩니다. 데이터는 다른 디스크에 저장됩니다. 기본 위치는 `/var/cyberx/backups`입니다. 

이 파일을 내부 네트워크에 자동으로 전송할 수 있습니다. 

> [!NOTE]
> 백업 및 복원 절차는 동일한 버전에서만 수행할 수 있습니다. 

온-프레미스 관리 콘솔 컴퓨터를 백업하려면 다음을 수행합니다. 

- 관리 계정에 로그인하고 `sudo cyberx-management-backup -full`을 입력합니다.

최신 백업 파일을 복원하려면 다음을 수행합니다.

- 관리 계정에 로그인하고 `$ sudo cyberx-management-system-restore`를 입력합니다.

백업을 외부 SMB 서버에 저장하려면 다음을 수행합니다.

1. 외부 SMB 서버에 공유 폴더를 만듭니다.  

   SMB 서버에 액세스하는 데 필요한 폴더 경로, 사용자 이름 및 암호를 가져옵니다. 

2. Defender for IoT에서 백업용 디렉터리를 만듭니다.

   - `sudo mkdir /<backup_folder_name_on_ server>` 

   - `sudo chmod 777 /<backup_folder_name_on_c_server>/` 

3. fstab 편집:  

   - `sudo nano /etc/fstab` 

   - `add - //<server_IP>/<folder_path> /<backup_folder_name_on_server> cifs rw,credentials=/etc/samba/user,vers=3.0,uid=cyberx,gid=cyberx,file_mode=0777,dir_mode=0777 0 0` 

4. SMB 서버가 공유할 자격 증명을 편집하거나 만듭니다. 

   - `sudo nano /etc/samba/user` 

5. 추가: 

   - `username=<user name>`

   - `password=<password>` 

6. 디렉터리를 탑재합니다. 

   - `sudo mount -a` 

7. Defender for IoT 온-프레미스 관리 콘솔의 공유 폴더에 백업 디렉터리를 구성합니다.  

   - `sudo nano /var/cyberx/properties/backup.properties` 

   - `set Backup.shared_location to <backup_folder_name_on_server>`

## <a name="edit-the-host-name"></a>호스트 이름 편집

조직 DNS 서버에 구성된 관리 콘솔의 호스트 이름을 편집하려면 다음을 수행합니다.

1. 관리 콘솔의 왼쪽 창에서 **시스템 설정** 을 선택합니다.  

2. 콘솔의 네트워킹 섹션에서 **네트워크** 를 선택합니다. 

3. 조직 DNS 서버에 구성된 호스트 이름을 입력합니다. 

4. **저장** 을 선택합니다.

## <a name="define-vlan-names"></a>VLAN 이름 정의

VLAN 이름은 센서와 관리 콘솔 간에 동기화되지 않습니다. 구성 요소에 동일한 이름을 정의합니다.

네트워킹 영역에서 **VLAN** 을 선택하고 검색된 VLAN ID에 이름을 추가합니다. 그런 다음 **저장** 을 선택합니다.

## <a name="define-a-proxy-to-sensors"></a>센서에 프록시 정의

사용자가 센서에 직접 로그인하지 못하도록 하여 시스템 보안을 강화합니다. 대신 단일 방화벽 규칙을 사용하여 사용자가 온-프레미스 관리 콘솔에서 센서에 액세스할 수 있도록 프록시 터널링을 사용합니다. 이 향상된 기능은 센서 너머의 네트워크 환경에 대한 무단 액세스 가능성을 줄입니다.

프록시는 센서에 직접 연결되지 않는 환경에서 사용하세요.

:::image type="content" source="media/how-to-access-sensors-using-a-proxy/proxy-diagram.png" alt-text="사용자.":::

다음 절차에서는 온-프레미스 관리 콘솔에 센서를 연결하고 해당 콘솔에서 터널링을 사용하도록 설정합니다.

1. 관리 자격 증명을 사용하여 온-프레미스 관리 콘솔 어플라이언스 CLI에 로그인합니다.

2. `sudo cyberx-management-tunnel-enable`을 입력하고 **Enter** 키를 선택합니다.

4. `--port 10000`을 입력하고 **Enter** 키를 선택합니다.

## <a name="adjust-system-properties"></a>시스템 속성 조정

시스템 속성은 관리 콘솔의 다양한 작업 및 설정을 제어합니다. 이를 편집하거나 수정하면 관리 콘솔의 작업이 손상될 수 있습니다. 설정을 변경하기 전에 [Microsoft 지원](https://support.microsoft.com)에 문의하세요.

시스템 속성에 액세스하려면 다음을 수행합니다. 

1. 온-프레미스 관리 콘솔 또는 센서에 로그인합니다.

2. **시스템 설정** 을 선택합니다.

3. **일반** 섹션에서 **시스템 속성** 을 선택합니다.

## <a name="change-the-name-of-the-on-premises-management-console"></a>온-프레미스 관리 콘솔의 이름 변경

온-프레미스 관리 콘솔의 이름을 변경할 수 있습니다. 새 이름은 콘솔 웹 브라우저, 다양한 콘솔 창 및 문제 해결 로그에 표시됩니다. 기본 이름은 **management console** 입니다.

이름을 변경하는 방법은 다음과 같습니다.

1. 왼쪽 창의 맨 아래에서 현재 이름을 선택합니다.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/console-name.png" alt-text="온-프레미스 관리 콘솔 버전의 스크린샷.":::

2. **관리 콘솔 구성 편집** 대화 상자에서 새 이름을 입력합니다. 이름은 25자를 초과할 수 없습니다.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/edit-management-console-configuration.png" alt-text="Defender for IoT 플랫폼 구성을 편집하는 스크린샷.":::

3. **저장** 을 선택합니다. 새 이름이 적용됩니다.

   :::image type="content" source="media/how-to-change-the-name-of-your-azure-consoles/name-changed.png" alt-text="변경된 콘솔 이름을 보여 주는 스크린샷.":::

## <a name="password-recovery"></a>암호 복구

온-프레미스 관리 콘솔 암호 복구는 디바이스가 연결된 구독과 연결됩니다. 디바이스가 연결된 구독을 모르면 암호를 복구할 수 없습니다.

암호를 다시 설정하려면:

1. 온-프레미스 관리 콘솔의 로그인 페이지로 이동합니다.
1. **암호 복구** 를 선택합니다.
1. 고유 식별자를 복사합니다.
1. Defender for IoT **사이트 및 센서** 페이지로 이동하여 **내 암호 복구** 탭을 선택합니다.
1. 고유 식별자를 입력하고 **복구** 를 선택합니다. 활성화 파일이 다운로드됩니다.
1. **암호 복구** 페이지로 이동하여 활성화 파일을 업로드합니다.
1. **다음** 을 선택합니다.
 
   이제 사용자 이름과 새 시스템 생성 암호가 제공됩니다.

> [!NOTE]
> 센서는 원래 연결됐던 구독에 연결됩니다. 암호가 연결된 구독과 동일한 구독을 사용해야만 암호를 복구할 수 있습니다.

## <a name="update-the-software-version"></a>소프트웨어 버전 업데이트

다음 절차에서는 온-프레미스 관리 콘솔 소프트웨어 버전을 업데이트하는 방법을 설명합니다. 업데이트 프로세스는 30분 정도 걸립니다.

온-프레미스 관리 콘솔 및 관리형 센서로 작업하는 경우 **먼저 관리 콘솔을 업데이트** 합니다.

1. [Azure Portal](https://portal.azure.com/)로 이동합니다.

1. Defender for IoT로 이동합니다.

1. **업데이트** 페이지로 이동합니다.

1. 온-프레미스 관리 콘솔 섹션에서 버전을 선택합니다.

1. **다운로드** 를 선택하고 파일을 저장합니다.

1. 온-프레미스 관리 콘솔에 로그인하고 측면 메뉴에서 **시스템 설정** 을 선택합니다.

1. **버전 업데이트** 창에서 **업데이트** 를 선택합니다.

1. Defender for IoT **업데이트** 페이지에서 다운로드한 파일을 선택합니다.

## <a name="mail-server-settings"></a>메일 서버 설정

온-프레미스 관리 콘솔의 SMTP 메일 서버 설정을 정의합니다.

정의하려면 다음을 수행합니다.

1. 관리 자격 증명을 사용하여 온-프레미스 관리의 CLI에 로그인합니다.
1. ```nano /var/cyberx/properties/remote-interfaces.properties```.
1. Enter 키를 선택합니다. 다음과 같은 메시지가 표시됩니다.
```mail.smtp_server= ```
```mail.port=25 ```
```mail.sender=```
1. SMTP 서버 이름 및 발신자를 입력하고 enter 키를 선택합니다.

## <a name="see-also"></a>참고 항목

[관리 콘솔에서 센서 관리](how-to-manage-sensors-from-the-on-premises-management-console.md)

[개별 센서 관리](how-to-manage-individual-sensors.md)
