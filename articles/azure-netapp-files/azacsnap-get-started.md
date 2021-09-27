---
title: Azure NetApp Files용 Azure Application Consistent Snapshot 도구 시작 | Microsoft Docs
description: Azure NetApp Files에서 사용할 수 있는 Azure Application Consistent Snapshot 도구를 설치하는 안내를 제공합니다.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: 754a4540bc7164e672abd4fc81d8ecb86e2359a9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124737355"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool"></a>Azure Application Consistent Snapshot 도구 시작

이 문서에서는 Azure NetApp Files에서 사용할 수 있는 Azure Application Consistent Snapshot 도구를 설치하는 안내를 제공합니다.

## <a name="getting-the-snapshot-tools"></a>스냅샷 도구 가져오기

Microsoft에서 최신 버전의 [AzAcSnap 설치 프로그램](https://aka.ms/azacsnapdownload)을 받는 것이 좋습니다.

자동 설치 파일에는 다운로드한 설치 프로그램의 GPG 확인이 가능하도록 Microsoft의 공개 키로 서명되어 있는 연결된 [AzAcSnap 설치 프로그램 서명 파일](https://aka.ms/azacsnapdownloadsignature)이 있습니다.

다운로드가 완료되면 이 가이드의 단계에 따라 설치하세요.

### <a name="verifying-the-download"></a>다운로드 확인

설치 프로그램(위의 내용에 따라 다운로드 가능)에는 연결된 PGP 서명 파일이 있으며 파일 이름 확장명은 `.asc`입니다. 이 파일은 다운로드한 설치 프로그램이 확인된 Microsoft 제공 파일인지 확인하는 데 사용할 수 있습니다. Linux 패키지 서명에 사용되는 Microsoft PGP 공개 키는 여기(<https://packages.microsoft.com/keys/microsoft.asc>)에 있으며 서명 파일에 서명하는 데 사용됩니다.

Microsoft PGP 공개 키는 다음과 같이 사용자의 로컬로 가져올 수 있습니다.

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

다음 명령은 Microsoft PGP 공개 키를 신뢰합니다.

1. 저장소의 키를 나열합니다.
2. Microsoft 키를 편집합니다.
3. `fpr`로 지문을 확인합니다.
4. 키에 서명하여 신뢰합니다.

```bash
gpg --list-keys
```

나열된 키:
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

Microsoft 공개 키에 서명하는 대화형 `gpg` 세션의 출력:
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

설치 프로그램의 PGP 서명 파일은 다음과 같이 확인할 수 있습니다.

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

GPG 사용에 대한 자세한 내용은 [GNU 프라이버시 핸드북](https://www.gnupg.org/gph/en/manual/book1.html)을 참조하세요.

## <a name="supported-scenarios"></a>지원되는 시나리오

스냅샷 도구는 다음과 같은 시나리오에서 사용할 수 있습니다.

- 단일 SID
- 다중 SID
- HSR
- 확장
- MDC(단일 테넌트만 지원됨)
- 단일 컨테이너
- SUSE 운영 체제
- RHEL 운영 체제
- SKU 형식 I
- SKU 형식 II

[HANA(대규모 인스턴스)에 지원되는 시나리오](../virtual-machines/workloads/sap/hana-supported-scenario.md)를 참조하세요.

## <a name="snapshot-support-matrix-from-sap"></a>SAP의 스냅샷 지원 매트릭스

다음 매트릭스는 스토리지 스냅샷 백업용으로 SAP에서 지원하는 SAP HANA 버전에 대한 지침으로 제공됩니다.

| 데이터베이스 버전       |1.0 SPS12|2.0 SPS0|2.0 SPS1|2.0 SPS2|2.0 SPS3|2.0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|단일 컨테이너 데이터베이스| √       | √      | -      | -      | -      | -      |
|MDC 단일 테넌트        | -       | -      | √      | √      | √      | √      |
|MDC 다중 테넌트     | -       | -      | -      | -      | -      | √      |
> √ = <small>스토리지 스냅샷용으로 SAP에서 지원</small>

## <a name="important-things-to-remember"></a>유의해야 할 중요한 사항

- 스냅샷 도구를 설치한 후에는 사용 가능한 스토리지 공간을 지속적으로 모니터링하고 저장 공간이 가득 차지 않도록 필요한 경우 이전 스냅샷을 정기적으로 삭제합니다.
- 항상 최신 스냅샷 도구를 사용합니다.
- 전체적으로 동일한 버전의 스냅샷 도구를 사용합니다.
- 프로덕션 시스템에서 사용하기 전에 스냅샷 도구를 테스트하고 필요한 매개 변수와 명령 출력을 숙지합니다.
- 백업용 HANA 사용자를 설정하는 경우(자세한 내용은 이 문서 아래 참조), 각 HANA 인스턴스에 대해 사용자를 설정해야 합니다. MDC용 SYSTEMDB(SID 데이터베이스가 아님)에서 HANA 인스턴스에 액세스하는 SAP HANA 사용자 계정을 만듭니다. 단일 컨테이너 환경에서는 테넌트 데이터베이스에서 설정할 수 있습니다.
- 고객은 스토리지 액세스를 위해 SSH 공개 키를 제공해야 합니다. 이 작업은 노드당 한 번, 그리고 명령을 실행하는 각 사용자에 대해 수행되어야 합니다.
- 볼륨당 스냅샷 수는 250개로 제한됩니다.
- 구성 파일을 수동으로 편집하는 경우 메모장 등의 Windows 편집기가 아닌 "vi"와 같은 Linux 텍스트 편집기를 항상 사용합니다. Windows 편집기를 사용하면 파일 형식이 손상될 수 있습니다.
  - SAP HANA 사용자가 SAP HANA와 통신할 수 있도록 `hdbuserstore`를 설정합니다.
- DR의 경우: DR을 설정하기 전에 DR 노드에서 스냅샷 도구를 테스트해야 합니다.
- 디스크 공간을 정기적으로 모니터링합니다. 자동화된 로그 삭제는 SAP HANA 2 이상 릴리스의 경우 `azacsnap -c backup`의 `--trim` 옵션으로 관리됩니다.
- **스냅샷이 생성되지 않을 위험** - 스냅샷 도구는 구성 파일에 지정된 SAP HANA 시스템의 노드와만 상호 작용합니다.  이 노드를 사용할 수 없게 되면 다른 노드와 자동으로 통신을 시작하는 메커니즘이 없습니다.  
  - **대기 상태로 SAP HANA 스케일 아웃** 시나리오의 경우 일반적으로 마스터 노드에 스냅샷 도구를 설치하고 구성합니다. 하지만 마스터 노드를 사용할 수 없게 되면 대기 노드가 마스터 노드 역할을 인계받습니다. 이 경우 구현 팀은 스냅샷 누락을 방지하기 위해 두 노드(마스터 및 대기) 모두에 스냅샷 도구를 구성해야 합니다. 정상 상태에서는 마스터 노드가 crontab에서 시작된 HANA 스냅샷을 생성하지만 마스터 노드 장애 조치(failover) 후에는 새 마스터 노드(이전 대기 노드)와 같은 다른 노드에서 이러한 스냅샷 작업이 실행되어야 합니다. 이런 결과를 얻으려면 대기 노드에 스냅샷 도구를 설치하고, 스토리지 통신이 가능하도록 설정하고, hdbuserstore를 구성하고, `azacsnap.json`을 구성하고 장애 조치(failover) 전에 crontab 명령을 준비해야 합니다.
  - **SAP HANA HSR HA** 시나리오의 경우 두 노드(주 및 보조) 모두에 스냅샷 도구를 설치, 구성 및 예약하는 것이 좋습니다. 그런 다음, 주 노드를 사용할 수 없게 되면 보조 노드가 인계 받아 보조 노드에서 스냅샷을 생성합니다. 정상 상태에서는 주 노드가 crontab에 의해 시작된 HANA 스냅샷을 생성하며 보조 노드가 스냅샷 생성을 시도해도 주 노드가 제대로 작동하기 때문에 실패합니다.  하지만 주 노드 장애 조치(failover) 후에는 해당 스냅샷이 보조 노드에서 실행됩니다. 이런 결과를 얻으려면 보조 노드에 스냅샷 도구를 설치하고, 스토리지 통신이 가능하도록 설정하고, `hdbuserstore`를 구성하고, azacsnap.json을 구성하고, 장애 조치(failover) 전에 crontab 명령을 사용하도록 설정해야 합니다.

## <a name="guidance-provided-in-this-document"></a>이 문서에 제공된 지침

스냅샷 도구를 사용하는 방법을 설명하기 위해 다음 지침이 제공됩니다.

### <a name="taking-snapshot-backups"></a>스냅샷 백업 생성

- [스토리지 스냅샷의 필수 구성 조건은 무엇인가요?](azacsnap-installation.md#prerequisites-for-installation)
  - [스토리지와의 통신을 사용하도록 설정](azacsnap-installation.md#enable-communication-with-storage)
  - [데이터베이스와의 통신 사용](azacsnap-installation.md#enable-communication-with-database)
- [스냅샷을 수동으로 생성하는 방법](azacsnap-tips.md#take-snapshots-manually)
- [자동 스냅샷 백업을 설정하는 방법](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [스냅샷을 모니터링하는 방법](azacsnap-tips.md#monitor-the-snapshots)
- [스냅샷을 삭제하는 방법](azacsnap-tips.md#delete-a-snapshot)
- [스냅샷을 복원하는 방법](azacsnap-tips.md#restore-a-snapshot)
- [`boot` 스냅샷을 복원하는 방법](azacsnap-tips.md#restore-a-boot-snapshot)
- [스냅샷에 대해 알아야 할 주요 팩트](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> 스냅샷은 단일 SID와 다중 SID 모두에 대해 테스트됩니다.

### <a name="performing-disaster-recovery"></a>재해 복구 수행

- [DR 설치를 위한 필수 구성 조건은 무엇인가요?](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [재해 복구를 설정하는 방법](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [기본 사이트에서 DR 사이트로의 데이터 복제를 모니터링하는 방법](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [DR 사이트로 장애 조치(failover)를 수행하는 방법](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>다음 단계

- [Azure Application Consistent Snapshot 도구 설치](azacsnap-installation.md)
