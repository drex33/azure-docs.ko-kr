---
title: Azure Site Recovery에서 Azure VM 복제 문제 해결
description: 재해 복구를 위해 Azure 가상 머신을 복제할 때 오류 문제를 해결합니다.
author: rochakm
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/07/2020
ms.author: rochakm
ms.openlocfilehash: faee5c872b70f55011c6213a04e14c6f69bcc8df
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730461"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-errors"></a>Azure 간 VM 복제 오류 문제 해결

이 문서에서는 한 지역에서 다른 지역으로 Azure VM(가상 머신)을 복제 및 복구하는 동안 Azure Site Recovery에서 발생하는 일반적인 오류를 해결하는 방법을 설명합니다. 지원되는 구성에 대한 자세한 내용은 [Azure VM을 복제하기 위한 지원 매트릭스](azure-to-azure-support-matrix.md)를 참조하세요.

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure 리소스 할당량 문제(오류 코드 150097)

재해 복구(DR) 지역으로 사용할 대상 위치에 Azure VM을 만들려면 구독이 사용되도록 설정되어 있어야 합니다. 필요한 크기의 VM을 만들려면 구독에 충분한 할당량이 있어야 합니다. 기본값으로 Site Recovery는 원본 VM과 크기가 같은 대상 VM 크기를 선택합니다. 일치하는 크기를 사용할 수 없는 경우 Site Recovery에서 사용 가능하고 가장 근사한 크기를 자동으로 선택합니다.

원본 VM 구성을 지원하는 크기가 없는 경우 다음 메시지가 표시됩니다.

```Output
Replication couldn't be enabled for the virtual machine <VmName>.
```

### <a name="possible-causes"></a>가능한 원인

- 대상 지역 위치에 VM을 만드는 구독 ID가 사용하도록 설정되어 있지 않을 수 있습니다.
- 구독 ID가 사용하도록 설정되어 있지 않거나 대상 지역 위치의 특정 VM 크기를 생성하기에 할당량이 충분하지 않습니다.
- 대상 지역 위치의 구독 ID에 대해 원본 VM의 NIC(네트워크 인터페이스 카드) 수(2)와 일치하는 적절한 대상 VM 크기를 찾을 수 없습니다.

### <a name="fix-the-problem"></a>문제 해결

[Azure 청구 지원](../azure-portal/supportability/regional-quota-requests.md)에 문의하여 구독을 사용하도록 설정하면 대상 위치에 필요한 크기의 VM을 만드십시오. 그 다음 실패한 작업을 다시 시도하십시오.

대상 위치에 용량 제한이 있는 경우 해당 위치에 복제를 사용하지 않도록 설정하십시오. 그 다음 구독에 필요한 크기의 VM을 만들기에 충분한 할당량이 있는 다른 위치로 복제를 사용하도록 설정합니다.

## <a name="trusted-root-certificates-error-code-151066"></a>신뢰할 수 있는 루트 인증서(오류 코드 151066)

VM에 신뢰할 수 있는 최신 루트 인증서가 모두 있지 않은 경우 Site Recovery에 대해 복제를 사용하도록 설정하는 작업이 실패할 수 있습니다. 이러한 인증서가 없는 경우 VM의 Site Recovery 서비스호출 인증과 권한 부여가 실패합니다.

복제를 사용하도록 설정하는 작업이 실패하면 다음과 같은 메시지가 표시됩니다.

```Output
Site Recovery configuration failed.
```

### <a name="possible-cause"></a>가능한 원인

권한 부여 및 인증에 필요한 신뢰할 수 있는 필요한 루트 인증서가 가상 머신에 없습니다.

### <a name="fix-the-problem"></a>문제 해결

#### <a name="windows"></a>Windows

Windows 운영 체제를 실행하는 VM의 경우 모든 신뢰할 수 있는 루트 인증서가 VM에 표시되도록 최신 Windows 업데이트를 사용하십시오. 조직의 일반적인 Windows 업데이트 관리 또는 인증서 업데이트 관리 프로세스에 따라 VM의 최신 루트 인증서와 업데이트된 인증서 해지 목록을 가져옵니다.

- 연결이 끊어진 환경에 있는 경우 인증서를 받는 조직에서 표준 Windows 업데이트 프로세스를 따릅니다.
- 필요한 인증서가 VM에 없는 경우 보안상의 이유로 Site Recovery 서비스에 대한 호출이 실패합니다.

문제가 해결되었는지 확인하려면 VM의 브라우저에서 `login.microsoftonline.com`로 이동합니다.

자세한 내용은 [신뢰할 수 있는 루트 및 허용되지 않는 인증서 구성](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn265983(v=ws.11))을 참조하세요.

#### <a name="linux"></a>Linux

Linux 운영 체제 버전 배포자가 제공한 지침에 따라 신뢰할 수 있는 최신 루트 인증서 및 최신 인증서 해지 목록을 VM에 가져옵니다.

SUSE Linux는 바로 가기 링크 또는 symlink를 사용하기 때문에 인증서 목록을 유지 관리하려면 다음 단계를 수행합니다.

1. **루트** 사용자로 로그인합니다. 해시 기호(`#`)는 기본 명령 프롬프트입니다.

1. 디렉터리를 변경하려면 이 명령을 실행합니다.

   `cd /etc/ssl/certs`

1. Symantec 루트 CA 인증서가 있는지 확인합니다.

   `ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

   - Symantec 루트 CA 인증서가 발견되지 않으면 다음 명령을 실행하여 파일을 다운로드합니다. 모든 오류를 확인하고 네트워크 오류에 대한 권장 조치를 수행합니다.

     `wget https://docs.broadcom.com/docs-and-downloads/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem`

1. Baltimore 루트 CA 인증서가 있는지 확인합니다.

   `ls Baltimore_CyberTrust_Root.pem`

   - Baltimore 루트 CA 인증서가 발견되지 않으면 다음 명령을 실행하여 인증서를 다운로드합니다.

     `wget https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem`

1. DigiCert_Global_Root_CA 인증서가 있는지 확인합니다.

   `ls DigiCert_Global_Root_CA.pem`

    - DigiCert_Global_Root_CA 인증서를 찾을 수 없으면 다음 명령을 실행하여 인증서를 다운로드합니다.

      ```shell
      wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt

      openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem
      ```

1. 다시 해시 스크립트를 실행하여 새로 다운로드한 인증서에 대한 인증서 주체 해시를 업데이트합니다.

   `c_rehash`

1. symlink로 주체 해시가 생성되었는지 확인하려면 다음 명령을 실행합니다.

   ```shell
   ls -l | grep Baltimore
   ```

   ```Output
   lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem

   -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem
   ```

   ```shell
   ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem

   lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
   ```

   ```shell
   ls -l | grep DigiCert_Global_Root
   ```

   ```Output
   lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem

   -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem
   ```

1. 파일 이름을 _b204d74a.0_ 으로 하여 _VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem_ 파일 복사본 만들기:

   `cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0`

1. 파일 이름을 _653b494a.0_ 으로 하여 _Baltimore_CyberTrust_Root.pem_ 파일 복사본 만들기:

   `cp Baltimore_CyberTrust_Root.pem 653b494a.0`

1. 파일 이름을 _3513523f.0_ 으로 하여 _DigiCert_Global_Root_CA.pem_ 파일 복사본 만들기:

   `cp DigiCert_Global_Root_CA.pem 3513523f.0`

1. 파일이 있는지 확인합니다.

   ```shell
   ls -l 653b494a.0 b204d74a.0 3513523f.0
   ```

   ```Output
   -rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0

   -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0

   -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0
   ```

## <a name="outbound-urls-or-ip-ranges-error-code-151037-or-151072"></a>아웃바운드 URL 또는 IP 범위(오류 코드 151037 또는 151072)

Site Recovery 복제가 작동하려면 VM에서 특정 URL에 대한 아웃바운드 연결이 필요합니다. VM이 방화벽 뒤에 있거나 NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 이러한 문제 중 하나가 발생할 수 있습니다. URL을 통한 아웃바운드 액세스는 계속 지원하지만 IP 범위 허용 목록을 사용하는 것은 더 이상 지원되지 않습니다.

#### <a name="possible-causes"></a>가능한 원인

- DNS(Domain Name System) 해결 실패로 인해 사이트 복구 엔드포인트에 연결을 설정할 수 없습니다.
- 이 문제는 가상 머신을 장애 조치했지만 DR(재해 복구) 지역에서 DNS 서버에 도달할 수 없는 경우 다시 보호하는 동안 더 일반적입니다.

#### <a name="fix-the-problem"></a>문제 해결

사용자 지정 DNS를 사용하는 경우 재해 복구 지역에서 DNS 서버에 액세스할 수 있는지 확인합니다.

VM에서 사용자 지정 DNS 설정을 사용하는지 확인하는 방법:

1. **가상 머신** 을 열고 VM을 선택합니다.
1. VM **설정** 으로 이동하여 **네트워킹** 을 선택합니다.
1. **가상 네트워크/서브넷** 에서 가상 네트워크의 리소스 페이지가 열리는 링크를 선택합니다.
1. **설정** 으로 이동하여 **DNS 서버** 를 선택합니다.

가상 머신에서 DNS 서버에 액세스를 시도합니다. DNS 서버에 액세스할 수 없는 경우 DNS 서버를 장애 조치하거나 DR 네트워크 및 DNS 사이를 연결하여 액세스할 수 있도록 합니다.

> [!NOTE]
> 프라이빗 엔드포인트를 사용하는 경우 VM이 개인 DNS 레코드를 확인할 수 있어야 합니다.

:::image type="content" source="./media/azure-to-azure-troubleshoot-errors/custom_dns.png" alt-text="com-error.":::

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>문제 2: Site Recovery 구성이 실패했습니다(151196).

#### <a name="possible-cause"></a>가능한 원인

Microsoft 365 인증에 연결하여 IP4 엔드포인트를 식별할 수 없습니다.

#### <a name="fix-the-problem"></a>문제 해결

Azure Site Recovery가 인증을 위해 Microsoft 365 IP 범위에 액세스할 수 있어야 합니다.
Azure NSG (네트워크 보안 그룹) 규칙/방화벽 프록시를 사용하여 VM에서 아웃바운드 네트워크 연결을 제어하는 경우 AAD에 대한 액세스를 허용하기 위해 [AAD(Azure Active Directory) 서비스 태그](../virtual-network/network-security-groups-overview.md#service-tags) 기반 NSG 규칙을 사용해야 합니다. IP 주소 기반 NSG 규칙은 더 이상 지원하지 않습니다.

### <a name="issue-3-site-recovery-configuration-failed-151197"></a>문제 3: Site Recovery 구성이 실패했습니다(151197).

#### <a name="possible-cause"></a>가능한 원인

Azure Site Recovery 서비스 엔드포인트에 연결할 수 없습니다.

#### <a name="fix-the-problem"></a>문제 해결

Azure NSG(네트워크 보안 그룹) 규칙/방화벽 프록시를 사용하여 VM의 아웃바운드 네트워크 연결을 제어하는 경우 서비스 태그를 사용해야 합니다. Azure Site Recovery에 대해 NSG를 통한 IP 주소 허용 목록 사용은 더 이상 지원하지 않습니다.

### <a name="issue-4-replication-fails-when-network-traffic-uses-on-premises-proxy-server-151072"></a>문제 4: 네트워크 트래픽이 온-프레미스 프록시 서버를 사용하는 경우 복제 실패(151072)

#### <a name="possible-cause"></a>가능한 원인

사용자 지정 프록시 설정이 잘못되어 Mobility Service 에이전트가 IE(Internet Explorer)에서 프록시 설정을 자동으로 검색하지 않았습니다.

#### <a name="fix-the-problem"></a>문제 해결

1. Mobility Service 에이전트는 Windows의 경우 IE에서, Linux의 경우 `/etc/environment`에서 프록시 설정을 검색합니다.
1. Mobility Service에 대해서만 프록시를 설정하려는 경우 다음 위치에 있는 _ProxyInfo.conf_ 에서 프록시 세부 정보를 제공할 수 있습니다.

   - **Linux**: `/usr/local/InMage/config/`
   - **Windows**: `C:\ProgramData\Microsoft Azure Site Recovery\Config`

1. _ProxyInfo.conf_ 에는 다음 _INI_ 형식의 프록시 설정이 있어야 합니다.

   ```plaintext
   [proxy]
   Address=http://1.2.3.4
   Port=567
   ```

> [!NOTE]
> Mobility Service 에이전트는 **인증되지 않은 프록시** 만 지원합니다.

### <a name="more-information"></a>자세한 정보

[필수 URL](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) 또는 [필수 IP 범위](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)를 지정하려면 [Azure 간 복제의 네트워킹 정보](azure-to-azure-about-networking.md)의 지침을 따릅니다.

## <a name="disk-not-found-in-vm-error-code-150039"></a>VM에서 디스크를 찾을 수 없음(오류 코드 150039)

VM에 연결된 새 디스크는 초기화되어야 합니다. 디스크를 찾을 수 없는 경우 다음 메시지가 표시됩니다.

```Output
Azure data disk <DiskName> <DiskURI> with logical unit number <LUN> <LUNValue> was not mapped to a corresponding disk being reported from within the VM that has the same LUN value.
```

### <a name="possible-causes"></a>가능한 원인

- 새 데이터 디스크가 VM에 연결되었지만 초기화되지 않았습니다.
- VM 내의 데이터 디스크는 디스크가 VM에 연결된 LUN(논리 단위 번호) 값을 올바르게 보고하지 않습니다.

### <a name="fix-the-problem"></a>문제 해결

데이터 디스크가 초기화되었는지 확인하고 작업을 다시 시도합니다.

- **Windows**: [새 디스크를 연결하고 초기화합니다](../virtual-machines/windows/attach-managed-disk-portal.md).
- **Linux**: [Linux에서 새 데이터 디스크를 초기화합니다](../virtual-machines/linux/add-disk.md).

문제가 계속되면 지원에 문의하세요.

## <a name="multiple-disks-available-for-protection-error-code-153039"></a>여러 개의 디스크 보호 가능(오류 코드 153039)

### <a name="possible-causes"></a>가능한 원인

- 보호 후 하나 최근에 이상의 디스크가 가상 머신에 추가되었습니다.
- 가상 머신을 보호한 후 하나 이상의 디스크가 초기화되었습니다.

### <a name="fix-the-problem"></a>문제 해결

VM의 복제 상태를 다시 정상으로 만들려면 디스크를 보호하거나 경고를 해제하도록 선택할 수 있습니다.

#### <a name="to-protect-the-disks"></a>디스크 보호 방법

1. **복제된 항목** > _VM 이름_ > **디스크** 로 이동합니다.
1. 보호되지 않은 디스크를 선택한 다음 **복제 사용** 을 선택합니다.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/add-disk.png" alt-text="VM 디스크에서 복제를 사용하도록 설정합니다.":::

#### <a name="to-dismiss-the-warning"></a>경고 무시 방법

1. **복제된 항목** > _VM 이름_ 으로 이동합니다.
1. **개요** 섹션에서 경고를 선택하고 **확인** 을 선택합니다.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/dismiss-warning.png" alt-text="새 디스크 경고를 해제합니다.":::

## <a name="vm-removed-from-vault-completed-with-information-error-code-150225"></a>자격 증명 모음에서 제거된 VM이 정보와 함께 완료됨(오류 코드 150225)

Site Recovery에서 가상 머신을 보호할 때 원본 가상 머신의 링크가 생성됩니다. 보호를 제거하거나 복제를 사용하지 않도록 설정하면 Site Recovery가 정리 작업의 일부로 링크를 제거합니다. 가상 머신에 리소스 잠금이 있는 경우 정리 작업은 정보를 사용하여 완료됩니다. 정보에서는 가상 머신이 Recovery Services 자격 증명 모음에서 제거되었다고 표시되나 원본 컴퓨터에서 일부 부실 링크가 정리되지 않았을 수 있습니다.

이 가상 머신을 다시 보호하지 않으려는 경우에는 이 경고를 무시해도 됩니다. 그러나 나중에 이 가상 머신을 보호해야 하는 경우 이 섹션의 단계에 따라 링크를 정리하십시오.

> [!WARNING]
> 정리하지 않을 경우 다음과 같은 문제가 생길 수 있습니다.
>
> - Recovery Services 자격 증명 모음을 통해 복제를 사용하도록 설정하면 가상 머신이 나열되지 않습니다.
> - **가상 머신** > **설정** > **재해 복구** 를 사용하여 VM을 보호하고자 하는 경우 **VM의 기존 부실 리소스 링크로 인해 복제를 사용하도록 설정할 수 없습니다** 라는 메시지가 표시되며 작업이 실패합니다.

### <a name="fix-the-problem"></a>문제 해결

> [!NOTE]
> Site Recovery는 이러한 단계를 수행하는 중 원본 가상 머신을 삭제하거나 어떤 방식으로든 영향을 미치지 않습니다.

1. VM이나 VM 리소스 그룹에서 잠금을 제거합니다. 예를 들어 다음 이미지에서 이름이 `MoveDemo`인 VM의 리소스 잠금은 삭제해야 합니다.

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="VM에서 잠금을 제거합니다.":::

1. 스크립트를 다운로드하여 [부실 Site Recovery 구성을 제거합니다](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. 스크립트 _Cleanup-부실-asr-구성-Azure-VM.ps1_ 을 실행합니다. **구독 ID**, **VM 리소스 그룹**, **VM 이름** 을 매개 변수로 제공합니다.
1. Azure 자격 증명 메시지가 표시되면 자격 증명을 제공합니다. 그 다음 스크립트가 아무런 오류 없이 실행되는지 확인합니다.

## <a name="replication-not-enabled-on-vm-with-stale-resources-error-code-150226"></a>부실 리소스가 있는 VM에서 복제가 사용하도록 설정되지 않음(오류 코드 150226)

### <a name="possible-causes"></a>가능한 원인

가상 머신에 이전 Site Recovery 보호로 인한 부실 구성이 있습니다.

Azure VM의 부실 구성은 Site Recovery를 사용하여 Azure VM에 대해 복제를 사용하도록 설정한 후 다음과 같이 한 경우에 발생할 수 있습니다.

- 복제를 사용하지 않도록 설정했지만 원본 VM에 리소스 잠금이 있습니다.
- VM에서 명확하게 복제를 사용하지 않도록 설정하지 않고 Site Recovery 자격 증명 모음을 삭제했습니다.
- VM에서 명확하게 복제를 사용하지 않도록 설정하지 않고 Site Recovery 자격 증명 모음이 포함된 리소스 그룹을 삭제했습니다.

### <a name="fix-the-problem"></a>문제 해결

> [!NOTE]
> Site Recovery는 이러한 단계를 수행하는 중 원본 가상 머신을 삭제하거나 어떤 방식으로든 영향을 미치지 않습니다.

1. VM이나 VM 리소스 그룹에서 잠금을 제거합니다. 예를 들어 다음 이미지에서 이름이 `MoveDemo`인 VM의 리소스 잠금은 삭제해야 합니다.

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="VM에서 잠금을 제거합니다.":::

1. 스크립트를 다운로드하여 [부실 Site Recovery 구성을 제거합니다](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. 스크립트 _Cleanup-부실-asr-구성-Azure-VM.ps1_ 을 실행합니다. **구독 ID**, **VM 리소스 그룹**, **VM 이름** 을 매개 변수로 제공합니다.
1. Azure 자격 증명 메시지가 표시되면 자격 증명을 제공합니다. 그 다음 스크립트가 아무런 오류 없이 실행되는지 확인합니다.

## <a name="cant-select-vm-or-resource-group-in-enable-replication-job"></a>복제 작업을 사용하도록 설정하는 데 VM이나 리소스 그룹을 선택할 수 없음

### <a name="issue-1-the-resource-group-and-source-vm-are-in-different-locations"></a>문제 1: 리소스 그룹과 원본 VM이 다른 위치에 있음

현재 Site Recovery에는 원본 지역 리소스 그룹과 가상 머신이 동일한 위치에 있어야 합니다. 그렇지 않으면 보호를 적용하려 할 때 가상 머신이나 리소스 그룹을 찾을 수 없습니다.

이 문제를 해결하려면 Recovery Services 자격 증명 모음 대신 VM에서 복제를 사용하도록 설정할 수 있습니다. **원본 VM** > **속성** > **재해 복구** 로 이동하여 복제를 사용하도록 설정합니다.

### <a name="issue-2-the-resource-group-isnt-part-of-the-selected-subscription"></a>문제 2: 리소스 그룹이 선택한 구독의 일부가 아님

리소스 그룹이 선택한 구독의 일부가 아닐 경우 보호 시점에 리소스 그룹을 찾을 수 없을 수 있습니다. 리소스 그룹이 사용 중인 구독에 포함되어 있어야 합니다.

### <a name="issue-3-stale-configuration"></a>문제 3: 부실 구성

Azure VM에 부실 Site Recovery 구성이 있는 경우 복제에 사용하도록 설정하고자 하는 VM을 찾을 수 없을 수 있습니다. 이러한 조건은 Site Recovery를 사용하여 Azure VM에 대해 복제를 사용하도록 설정한 경우 다음과 같이 했을 때 발생할 수 있습니다.

- VM에서 명확하게 복제를 사용하지 않도록 설정하지 않고 Site Recovery 자격 증명 모음을 삭제했습니다.
- VM에서 명확하게 복제를 사용하지 않도록 설정하지 않고 Site Recovery 자격 증명 모음이 포함된 리소스 그룹을 삭제했습니다.
- 복제를 사용하지 않도록 설정했지만 원본 VM에 리소스 잠금이 있습니다.

### <a name="fix-the-problem"></a>문제 해결

> [!NOTE]
> 이 섹션에 언급된 스크립트를 사용하기 전에 `AzureRM.Resources` 모듈을 업데이트해야 합니다. Site Recovery는 이러한 단계를 수행하는 중 원본 가상 머신을 삭제하거나 어떤 방식으로든 영향을 미치지 않습니다.

1. 잠금이 있는 경우 VM이나 VM 리소스 그룹에서 잠금을 제거합니다. 예를 들어 다음 이미지에서 이름이 `MoveDemo`인 VM의 리소스 잠금은 삭제해야 합니다.

   :::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/vm-locks.png" alt-text="VM에서 잠금을 제거합니다.":::

1. 스크립트를 다운로드하여 [부실 Site Recovery 구성을 제거합니다](https://github.com/AsrOneSdk/published-scripts/blob/master/Cleanup-Stale-ASR-Config-Azure-VM.ps1).
1. 스크립트 _Cleanup-부실-asr-구성-Azure-VM.ps1_ 을 실행합니다. **구독 ID**, **VM 리소스 그룹**, **VM 이름** 을 매개 변수로 제공합니다.
1. Azure 자격 증명 메시지가 표시되면 자격 증명을 제공합니다. 그 다음 스크립트가 아무런 오류 없이 실행되는지 확인합니다.

## <a name="unable-to-select-a-vm-for-protection"></a>보호할 VM을 선택할 수 없음

### <a name="possible-cause"></a>가능한 원인

가상 머신의 확장이 실패하거나 응답하지 않는 상태로 설치됨

### <a name="fix-the-problem"></a>문제 해결

**가상 머신** > **설정** > **확장** 으로 이동하고 실패 상태인 확장이 있는지 확인합니다. 실패한 확장을 모두 제거하고 가상 머신을 다시 보호해 봅니다.

## <a name="vm-provisioning-state-isnt-valid-error-code-150019"></a>VM의 프로비전 상태가 잘못되었음(오류 코드 150019)

VM에서 복제를 사용하도록 설정하려면 프로비전 상태가 **성공** 이어야 합니다. 다음 단계를 수행하여 프로비전 상태를 확인합니다.

1. Azure Portal의 **모든 서비스** 에서 **리소스 탐색기** 를 선택합니다.
1. **구독** 목록을 확장하고 본인의 구독을 선택합니다.
1. **리소스 그룹** 목록을 확장하고 VM의 리소스 그룹을 선택합니다.
1. **리소스** 목록을 확장하고 사용자의 VM을 선택합니다.
1. 오른쪽의 인스턴스 보기에서 **provisioningState** 필드를 선택합니다.

### <a name="fix-the-problem"></a>문제 해결

- **provisioningState** 가 **실패** 이면 지원 서비스에 문제 해결을 위한 세부 정보를 문의합니다.
- **provisioningState** 가 **업데이트 중** 이면 또 다른 확장을 배포하는 중일 수 있습니다. VM에서 진행 중인 작업이 있는지 확인한 후, 해당 작업이 완료될 때까지 기다린 다음 실패한 Site Recovery 작업에서 복제를 사용하도록 설정해 봅니다.

## <a name="unable-to-select-target-vm"></a>대상 VM을 선택할 수 없음

### <a name="issue-1-vm-is-attached-to-a-network-thats-already-mapped-to-a-target-network"></a>문제 1: VM이 대상 네트워크에 이미 매핑된 네트워크에 연결된 경우

재해 복구 구성 중 원본 VM이 가상 네트워크의 일부이며 동일한 가상 네트워크의 다른 VM이 이미 대상 리소스 그룹의 네트워크에 매핑된 경우 기본값으로 네트워크 선택 드롭다운 목록 상자를 이용할 수 없습니다(흐리게 표시).

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png" alt-text="네트워크 선택 목록을 사용할 수 없습니다.":::

### <a name="issue-2-you-previously-protected-the-vm-and-then-you-disabled-the-replication"></a>문제 2: 이전에 VM을 보호했으며 이후 복제를 사용하지 않도록 설정

VM의 복제를 사용하지 않도록 설정해도 네트워크 매핑은 삭제되지 않습니다. 매핑은 VM을 보호하던 Recovery Services 자격 증명 모음에서 선택해야 합니다. **Recovery Services 자격 증명 모음** 을 선택한 다음 **관리** > **Site Recovery 인프라** > **Azure 가상 머신용** > **네트워크 매핑** 으로 이동합니다.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png" alt-text="네트워크 매핑을 삭제합니다.":::

재해 복구 설정 중에 구성된 대상 네트워크는 초기 설정 후 및 VM이 보호된 후에 변경할 수 있습니다. **네트워크 매핑을 수정** 하려면 네트워크 이름을 선택합니다.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png" alt-text="네트워크 매핑을 수정합니다.":::


## <a name="com-or-vss-error-code-151025"></a>COM+ 또는 VSS(오류 코드 151025)

COM+나 볼륨 섀도 복사본 서비스(VSS) 오류가 발생하면 다음과 같은 메시지가 표시됩니다.

```Output
Site Recovery extension failed to install.
```

### <a name="possible-causes"></a>가능한 원인

- COM+ 시스템 애플리케이션 서비스가 사용하지 않도록 설정되었습니다.
- 볼륨 섀도 복사본 서비스를 사용하지 않도록 설정되었습니다.

### <a name="fix-the-problem"></a>문제 해결

COM+ 시스템 애플리케이션 및 볼륨 섀도 복사본 서비스를 자동 또는 수동 시작 모드로 설정합니다.

1. Windows에서 서비스 콘솔을 엽니다.
1. COM+ 시스템 애플리케이션 및 볼륨 섀도 복사본 서비스의 **시작 유형** 이 **사용하지 않도록 설정** 되어 있지 않아야 합니다.

   :::image type="content" source="./media/azure-to-azure-troubleshoot-errors/com-error.png" alt-text="COM+ 시스템 애플리케이션과 볼륨 섀도 복사본 서비스의 시작 유형을 확인합니다.":::

## <a name="unsupported-managed-disk-size-error-code-150172"></a>지원되지 않는 관리 디스크 크기(오류 코드 150172)

이 오류가 발생하면 다음 메시지가 표시됩니다.

```Output
Protection couldn't be enabled for the virtual machine as it has <DiskName> with size <DiskSize> that is lesser than the minimum supported size 1024 MB.
```

### <a name="possible-cause"></a>가능한 원인

디스크가 지원되는 크기(1024MB)보다 작습니다.

### <a name="fix-the-problem"></a>문제 해결

디스크 크기가 지원되는 크기 범위 내에 속하는지 확인한 다음 작업을 다시 시도합니다.

## <a name="protection-not-enabled-when-grub-uses-device-name-error-code-151126"></a>GRUB에서 디바이스 이름을 사용하는 경우 보호를 사용하도록 설정할 수 없음(오류 코드 151126)

### <a name="possible-causes"></a>가능한 원인

Linux Grand Unified Bootloader(GRUB) 구성 파일( _/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_ 또는 _/etc/default/grub_)은 `root` 및 `resume` 매개 변수로 UUID(Universally Unique Identifier) 값 대신 실제 디바이스 이름을 지정할 수도 있습니다. Site Recovery에는 UUID가 필요한데 이는 디바이스 이름이 변경될 수 있기 때문입니다. 다시 시작할 때 VM 장애 조치 시 동일한 이름이 사용되지 않아 문제가 생길 수 있습니다.

다음 예제는 필요한 UUID 대신 디바이스 이름이 표시된 GRUB 파일의 일부 줄입니다.

- 파일 _/boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- 파일: _/boot/grub/menu.lst_

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

### <a name="fix-the-problem"></a>문제 해결

각 디바이스 이름을 해당하는 UUID로 바꿉니다.

1. `blkid <device name>` 명령을 실행하여 디바이스의 UUID를 찾습니다. 예를 들면 다음과 같습니다.

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. 디바이스 이름을 `root=UUID=<UUID>` 및 `resume=UUID=<UUID>` 형식의 UUID로 바꿉니다. 바꾼 다음 _/boot/grub/menu.lst_ 의 줄은 예를 들어 다음 줄과 같아야 합니다.

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. 보호를 다시 시도합니다.

## <a name="protection-failed-because-grub-device-doesnt-exist-error-code-151124"></a>GRUB 디바이스가 존재하지 않아 보호 실패(오류 코드 151124)

### <a name="possible-cause"></a>가능한 원인

GRUB 구성 파일 ( _/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/grub2/grub.cfg_ 또는 _/etc/default/grub_)에 `rd.lvm.lv` 또는 `rd_LVM_LV` 매개 변수가 포함되어 있을 수 있습니다. 이러한 매개 변수는 부팅 시간에 발견되는 LVM(Logical Volume Manager) 디바이스를 식별합니다. 이러한 LVM 디바이스가 존재하지 않는 경우, 보호되는 시스템은 부팅되지 않으며 부팅 프로세스에서 중단됩니다. 장애 조치 VM에서도 동일한 문제가 발견될 수 있습니다. 다음은 몇 가지 예입니다.

- 파일: RHEL7에서 _/boot/grub2/grub.cfg_:

  `linux16 /vmlinuz-3.10.0-957.el7.x86_64 root=/dev/mapper/rhel_mup--rhel7u6-root ro crashkernel=128M\@64M rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet LANG=en_US.UTF-8`

- 파일: RHEL7에서 _/etc/default/grub_:

  `GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/root rd.lvm.lv=rootvg/swap rhgb quiet`

- 파일: RHEL6에서 _/boot/grub/menu.lst_:

  `kernel /vmlinuz-2.6.32-754.el6.x86_64 ro root=UUID=36dd8b45-e90d-40d6-81ac-ad0d0725d69e rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=rootvg/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_LVM_LV=rootvg/lv_swap rd_NO_DM rhgb quiet`

각 예제에서 GRUB은 볼륨 그룹 `rootvg`에서 이름이 `root` 및 `swap`인 두 개의 LVM 디바이스를 검색해야 합니다.

### <a name="fix-the-problem"></a>문제 해결

LVM 디바이스가 없는 경우 디바이스를 만들거나 GRUB 구성 파일에서 해당하는 매개 변수를 제거합니다. 그런 다음 다시 보호를 사용하도록 설정해 봅니다.

## <a name="mobility-service-update-finished-with-warnings-error-code-151083"></a>Mobility Service 업데이트가 경고와 함께 완료됨(오류 코드 151083)

Site Recovery Mobility Service에는 다양한 구성 요소가 있는데 그 중 필터 드라이버라는 구성 요소가 있습니다. 필터 드라이버는 시스템을 다시 시작하는 동안에만 시스템 메모리에 로드됩니다. Mobility Service 업데이트에 필터 드라이버 변경 사항이 포함될 때마다 컴퓨터가 업데이트되지만 여전히 일부 픽스는 다시 시작해야 한다는 경고가 표시됩니다. 이 경고가 나타나는 이유는 새 필터 드라이버가 로드되어야 필터 드라이버 픽스가 적용되는데 필터 드라이버는 다시 시작할 때만 로드되기 때문입니다.

> [!NOTE]
> 단지 경고일 뿐입니다. 기존 복제는 새 에이전트 업데이트 이후에도 계속 작동합니다. 언제든 새로운 필터 드라이버의 이점을 이용하고자 할 때 다시 시작할 수도 있지만 다시 시작하지 않으면 이전 필터 드라이버가 계속 작동합니다.
>
> 필터 드라이버와 별도로 Mobility Service 업데이트의 기타 개선 사항과 픽스의 이점은 다시 시작하지 않아도 적용됩니다.

## <a name="protection-not-enabled-if-replica-managed-disk-exists"></a>복제본 관리 디스크가 있으면 보호는 사용하도록 설정되지 않음

이 오류는 대상 리소스 그룹에 복제본 관리 디스크가 이미 있으나 예상되는 태그가 없을 때 발생합니다.

### <a name="possible-cause"></a>가능한 원인

이 문제는 가상 머신이 이전에 보호되었으며 복제가 사용하지 않도록 설정되었고 복제본 디스크를 제거하지 않았을 때 발생할 수 있습니다.

### <a name="fix-the-problem"></a>문제 해결

오류 메시지의 복제본 디스크를 삭제하고 실패한 보호 작업을 다시 시도합니다.

## <a name="enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137"></a>설치 프로그램에서 루트 디스크를 찾을 수 없어 보호를 사용하도록 설정할 수 없음(오류 코드 151137)

이 오류는 Linux 컴퓨터에서 OS 디스크가 Azure Disk Encryption(ADE)을 사용하여 암호화되었을 때 발생합니다. 이 문제는 에이전트 버전 9.35에서만 유효합니다.

### <a name="possible-causes"></a>가능한 원인

설치 프로그램은 루트 파일-시스템을 호스팅하는 루트 디스크를 찾을 수 없습니다.

### <a name="fix-the-problem"></a>문제 해결

이 문제를 해결하려면 아래 단계를 따릅니다.

1. 아래 명령을 사용하여 RHEL 및 CentOS 컴퓨터에서 디렉터리 _/var/lib/waagent_ 아래의 에이전트 구성 요소를 찾습니다. <br>

    `# find /var/lib/ -name Micro\*.gz`

   예상 출력:

    `/var/lib/waagent/Microsoft.Azure.RecoveryServices.SiteRecovery.LinuxRHEL7-1.0.0.9139/UnifiedAgent/Microsoft-ASR_UA_9.35.0.0_RHEL7-64_GA_30Jun2020_release.tar.gz`

2. 새 디렉터리를 만들고 해당 디렉터리를 이 새로운 디렉터리로 변경합니다.
3. 아래 명령을 사용하여 첫 번째 단계에서 찾은 에이전트 파일을 여기에 추출합니다.

    `tar -xf <Tar Ball File>`

4. _prereq_check_installer.json_ 파일을 열고 다음 줄을 삭제합니다. 그 다음 파일을 저장합니다.

    ```
       {
          "CheckName": "SystemDiskAvailable",
          "CheckType": "MobilityService"
       },
    ```
5. 다음 명령을 사용하여 설치 프로그램을 호출합니다. <br>

    `./install -d /usr/local/ASR -r MS -q -v Azure`
6. 설치 프로그램이 성공하면 다시 복제 작업을 사용하도록 설정해 봅니다.

## <a name="next-steps"></a>다음 단계

[Azure VM을 다른 Azure 지역에 복제](azure-to-azure-how-to-enable-replication.md)