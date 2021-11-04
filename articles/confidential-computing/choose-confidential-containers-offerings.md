---
title: 기밀 컴퓨팅을 위한 컨테이너 제공 선택
description: 보안, 격리 및 개발자 요구를 충족 하기 위해 적절 한 기밀 컨테이너 제품을 선택 하는 방법입니다.
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: amgowda
ms.custom: ignite-fall-2021
ms.openlocfilehash: 158e3e5a8191bc8e193ff9dab803feed7da2cc8a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052863"
---
# <a name="choosing-confidential-container-offerings"></a>기밀 컨테이너 제공 선택

Azure 기밀 컴퓨팅은 여러 유형의 기밀 컨테이너를 제공 합니다. 이러한 컨테이너를 사용 하 여 데이터 무결성 및 기밀성을 지원 하 고 코드 무결성을 지원할 수 있습니다.

기밀 컨테이너는 암호화를 통해 코드를 보호 하는 데도 도움이 됩니다. 하드웨어 기반 보증 및 신뢰의 하드웨어 루트를 만들 수 있습니다. 기밀 컨테이너를 사용 하 여 공격 노출 영역을 낮출 수도 있습니다.

## <a name="enclaves-confidential-containers"></a>Enclaves 기밀 컨테이너

Enclaves를 사용 하 여 기밀 컨테이너를 배포할 수 있습니다. 이 컨테이너 배포 방법에는 더 낮은 신뢰 컴퓨팅 기반 (TCB)이 있는 가장 강력한 보안 및 계산 격리가 있습니다. 하드웨어 기반 티 (신뢰할 수 있는 실행 환경)에서 실행 되는 인텔 SGX (Software Guard Extensions)를 기반으로 하는 기밀 컨테이너를 사용할 수 있습니다. 이러한 컨테이너는 기존 컨테이너 앱의 리프트 및 시프트를 지원 합니다. 또 다른 옵션은 enclave 인식으로 사용자 지정 앱 빌드를 허용 하는 것입니다.

Azure Kubernetes 서비스 (AKS)에는 두 가지 프로그래밍 및 배포 모델이 있습니다. 

**수정** 되지 않은 컨테이너는 OSS 프로젝트의 Azure 파트너 에코 시스템을 통해 Intel SGX에서 더 높은 프로그래밍 언어를 지원 합니다. 자세한 내용은 수정 되지 않은 [컨테이너 배포 흐름 및 샘플](./confidential-containers.md)을 참조 하세요.

**Enclave 인식 컨테이너** 는 사용자 지정 Intel SGX 프로그래밍 모델을 사용 합니다. 자세한 내용은 [enclave 인식 컨테이너 배포 흐름 및 샘플](./enclave-aware-containers.md)을 참조 하세요. 

![격리 및 보안 경계를 보여 주는 Intel SGX를 사용 하는 enclave 기밀 컨테이너의 다이어그램입니다.](./media/confidential-containers/confidential-container-intel-sgx.png)

## <a name="learn-more"></a>자세한 정보

- [Azure의 Intel SGX 기밀 Virtual Machines](./virtual-machine-solutions-sgx.md)
- [Azure의 기밀 컨테이너](./confidential-containers.md)
