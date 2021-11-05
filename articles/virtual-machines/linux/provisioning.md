---
title: Linux 프로비저닝 개요
description: Linux VM 이미지를 가져오거나 Azure에서 사용할 새 이미지를 만드는 방법에 대한 개요입니다.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 70c3eaf554d1752b6aaa2a4ed1bfd05e9da67b5c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441156"
---
# <a name="azure-linux-vm-provisioning"></a>Azure Linux VM 프로비저닝

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

일반화된 이미지(Azure Compute Gallery 또는 관리형 이미지)로 VM을 만들 때 컨트롤 플레인을 사용하여 VM을 만들고 매개 변수와 설정을 VM에 전달할 수 있습니다. 이것을 VM *프로비저닝* 이라고 합니다. 프로비저닝하는 동안 플랫폼은 VM이 부팅될 때 사용할 수 있는 필수 VM 생성 매개 변수 값(호스트 이름, 사용자 이름, 암호, SSH 키, customData)을 만듭니다. 

이미지 속에 구워진 프로비저닝 에이전트는 플랫폼과 상호 작용하고, 여러 독립 프로비저닝 인터페이스에 연결하고, 속성을 설정하고, 플랫폼에 완료되었다는 신호를 보냅니다. 

프로비저닝 에이전트는 [Azure Linux 에이전트](../extensions/agent-linux.md)일 수도 있고 [cloud-init](./using-cloud-init.md)일 수도 있습니다. 두 에이전트는 일반화된 이미지를 만드는 데 꼭 필요한 [필수 구성 요소](create-upload-generic.md)입니다.

프로비저닝 에이전트는 보증 [Azure Linux 배포판](./endorsed-distros.md)을 모두 지원하며, 대부분의 경우 cloud-init와 Linux 에이전트가 함께 보증 배포판 이미지와 함께 제공됩니다. 따라서 cloud-init에서 프로비저닝을 처리한 다음, Linux 에이전트에서 [Azure 확장](../extensions/features-windows.md) 처리를 지원할 수 있습니다. 확장을 지원한다는 것은 VM에서 VM 암호 재설정, Azure 모니터링, Azure Backup, Azure 디스크 암호화 같은 추가 Azure 서비스를 지원할 수 있다는 뜻입니다.

프로비저닝이 완료되면 부팅할 때마다 cloud-init가 실행됩니다. cloud-init는 네트워킹 변경, 탑재, 임시 디스크 포맷, Linux 에이전트 시작 등의 VM 변경 내용을 모니터링합니다. Linux 에이전트는 지속적으로 서버에서 실행되면서 Azure 플랫폼으로부터 '목표 상태'(새 구성)를 검색하므로, 확장을 설치할 때마다 에이전트가 확장을 처리할 수 있습니다.

현재 두 개의 프로비저닝 에이전트가 있는데, cloud-init를 선택해야 하며, Linux 에이전트는 확장을 지원하기 위해 설치해야 합니다. 이렇게 하면 플랫폼을 최적화할 수 있으며, Linux 에이전트를 사용하지 않도록 설정/제거할 수 있습니다. 에이전트 없이 이미지를 만드는 방법과 에이전트를 제거하는 방법에 대한 자세한 내용은 이 [설명서](disable-provisioning.md)를 참조하세요.

보유한 Linux 커널이 두 에이전트 중 하나를 실행할 수 없지만 호스트 이름, customData, 사용자 이름, 암호, ssh 키와 같은 일부 VM 만들기 속성을 설정하려는 경우 이 문서의 [에이전트 없이 일반화된 이미지 만들기](no-agent.md)를 참조하고 플랫폼 요구 사항을 충족하는지 확인하세요.


## <a name="provisioning-agent-responsibilities"></a>프로비저닝 에이전트 책임

**이미지 프로비저닝**
  
- 사용자 계정 만들기
- SSH 인증 유형 구성
- SSH 공개 키 및 키 쌍 배포
- 호스트 이름 설정
- 플랫폼 DNS에 호스트 이름 게시
- 플랫폼에 SSH 호스트 키 지문 보고
- 리소스 디스크 관리
- 리소스 디스크 포맷 및 탑재
- `customData` 사용 및 처리
 
**네트워킹**
  
- 플랫폼 DHCP 서버와의 호환성을 개선하기 위한 경로 관리
- 네트워크 인터페이스 이름의 안정성 확인

**커널**
  
- 가상 NUMA 구성(`2.6.37` 미만 커널에는 사용 안 함)
- `/dev/random`의 Hyper-V 엔트로피 이용
- 루트 디바이스(원격일 수 있음)에 대한 SCSI 시간 제한 구성

**진단**
  
- 직렬 포트로 콘솔 리디렉션

## <a name="communication"></a>통신
플랫폼에서 에이전트로의 정보 흐름은 다음 두 채널을 통해 진행됩니다.

- IaaS 배포를 위해 부팅 시 연결된 DVD. 이 DVD에는 실제 SSH 키 쌍이 아닌 모든 프로비저닝 정보가 포함된 OVF 규격 구성 파일이 포함되어 있습니다.
- 배포 및 토폴로지 구성을 가져오는 데 사용된 REST API를 공개하는 TCP 엔드포인트


## <a name="azure-provisioning-agent-requirements"></a>Azure 프로비저닝 에이전트 요구 사항
Linux 에이전트 및 cloud-init가 제대로 작동하려면 다음과 같은 시스템 패키지가 필요합니다.
- Python 2.6 이상
- OpenSSL 1.0 이상
- OpenSSH 5.3 이상
- 파일 시스템 유틸리티: `sfdisk`, `fdisk`, `mkfs`, `parted`
- 암호 도구: chpasswd, sudo
- 텍스트 처리 도구: sed, grep
- 네트워크 도구: ip-route
- UDF 파일 시스템 탑재에 대한 커널 지원

## <a name="next-steps"></a>다음 단계

필요한 경우 [프로비저닝을 사용하지 않도록 설정하고 Linux 에이전트를 제거](disable-provisioning.md)할 수 있습니다.
