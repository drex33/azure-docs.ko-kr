---
title: Linux에서 SMB 1을 제거하여 Azure 및 온-프레미스 환경 보호 | Microsoft Docs
description: Azure Files는 SMB 1과 같은 안전하지 않은 레거시 버전의 SMB가 아니라 SMB 3.x 및 SMB 2.1을 지원합니다. Azure 파일 공유에 연결하기 전에 SMB 1과 같은 이전 버전의 SMB를 사용하지 않도록 설정할 수 있습니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8f0e75ebf45839eb75f22a8fb46e76bdebec4688
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112124786"
---
# <a name="remove-smb-1-on-linux"></a>Linux에서 SMB 1 제거
많은 조직 및 ISP(인터넷 서비스 공급자)에서 SMB를 통해 통신하는 포트인 445 포트를 차단입니다. 이 방법은 SMB 프로토콜의 레거시 버전과 더 이상 사용되지 않는 버전에 대한 보안 지침에서 제공됩니다. SMB 3.x는 인터넷 안전 프로토콜이지만 이전 버전의 SMB, 특히 SMB 1은 그렇지 않습니다. CIFS(Common Internet File System)라고도 하는 SMB 1은 많은 Linux 배포판에 포함되어 있습니다. 

SMB 1은 오래되고, 비효율적이며, 안전하지 않은 프로토콜입니다. 좋은 소식은 Azure Files가 SMB 1을 지원하지 않으며, Linux 커널 버전 4.18부터는 Linux를 통해 SMB 1을 비활성화할 수 있다는 것입니다. 프로덕션 환경에서 SMB 파일 공유를 사용하기 전에 Linux 클라이언트에서 SMB 1을 사용하지 않도록 설정하는 것이 [좋습니다](https://aka.ms/stopusingsmb1).

## <a name="linux-distribution-status"></a>Linux 배포 상태
Linux 커널 4.18부터 레거시 용도로 `cifs`라는 SMB 커널 모듈이 `disable_legacy_dialects`라는 새 모듈 매개 변수(다양한 외부 문서에서는 *parm* 이라고도 함)를 노출합니다. Linux 커널 4.18에 도입되었지만 일부 공급업체는 이 변경 내용을 지원되는 이전 커널로 백포팅했습니다. 편의를 위해 다음 표에서는 일반적인 Linux 배포판에서 이 모듈 매개 변수의 가용성을 자세히 설명합니다.

| 배포 | SMB 1 해제 가능 여부 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | 예 |
| Ubuntu 18.04 | 예 |
| Ubuntu 19.04 이상 | 예 |
| Debian 8-9 | 예 |
| Debian 10 이상 | 예 |
| Fedora 31 이상 | 예 |
| CentOS 7 | 예 | 
| CentOS 8 이상 | 예 |
| Red Hat Enterprise Linux 6.x-7.x | 예 |
| Red Hat Enterprise Linux 8 이상 | 예 |
| openSUSE Leap 15.0 | 예 |
| openSUSE Leap 15.1 이상 | 예 |
| openSUSE Tumbleweed | 예 |
| SUSE Linux Enterprise 11.x-12.x | 예 |
| SUSE Linux Enterprise 15 | 예 |
| SUSE Linux Enterprise 15.1 | 예 |

Linux 배포판에서 다음 명령을 통해 `disable_legacy_dialects` 모듈 매개 변수를 지원하는지 확인할 수 있습니다.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

이 명령은 다음 메시지를 출력해야 합니다.

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

## <a name="remove-smb-1"></a>SMB 1 제거
SMB 1을 사용하지 않도록 설정하기 전에 SMB 모듈이 현재 시스템에 로드되어 있지 않은지 확인합니다(SMB 공유를 탑재한 경우 자동으로 발생). 다음 명령을 사용하여 이 작업을 수행할 수 있습니다. SMB가 로드되지 않은 경우 아무 것도 출력되지 않습니다.

```bash
lsmod | grep cifs
```

모듈을 언로드하려면 먼저 모든 SMB 공유(위에 설명된 대로 `umount` 명령 사용)를 탑재 해제합니다. 다음 명령을 사용하여 사용하는 시스템에서 탑재된 모든 SMB 공유를 식별할 수 있습니다.

```bash
mount | grep cifs
```

모든 SMB 파일 공유를 탑재 해제한 후에는 모듈을 언로드하는 것이 안전합니다. 이렇게 하려면 `modprobe` 명령을 사용합니다.

```bash
sudo modprobe -r cifs
```

`modprobe` 명령을 사용하여 SMB 1이 언로드된 모듈을 수동으로 로드할 수 있습니다.

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

마지막으로 `/sys/module/cifs/parameters`에서 로드된 매개 변수를 살펴보면 SMB 모듈이 매개 변수와 함께 로드되었는지 확인할 수 있습니다.

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Ubuntu 및 Debian 기반 배포판에서 SMB 1을 영구적으로 사용하지 않도록 설정하려면 설정으로 `/etc/modprobe.d/local.conf`라는 새 파일(다른 모듈에 대한 사용자 지정 옵션이 없는 경우)을 만들어야 합니다. 다음 명령을 사용하여 수행할 수 있습니다.

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

SMB 모듈을 로드하여 이 작업의 수행 여부를 확인할 수 있습니다.

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>다음 단계
Azure Files에 대한 자세한 내용은 다음 링크를 참조하세요.

- [Azure 파일 배포에 대한 계획](storage-files-planning.md)
- [Linux에서 Azure Files 사용](storage-how-to-use-files-linux.md)
- [문제 해결](storage-troubleshoot-linux-file-connection-problems.md)