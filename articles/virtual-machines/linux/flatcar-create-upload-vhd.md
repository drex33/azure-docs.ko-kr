---
title: Azure에서 사용하도록 Flatcar Container Linux VHD 만들기 및 업로드
description: Flatcar Container Linux 운영 체제가 포함된 VHD를 만들고 업로드하는 방법에 대해 알아봅니다.
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 7df838cdc036e482bc97d9ca0bb76dd648eb0a46
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695575"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Azure에 미리 빌드된 Flatcar 이미지 사용

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Flatcar가 지원하는 각 채널에 대해 Flatcar Container Linux의 미리 빌드된 Azure 가상 하드 디스크 이미지를 다운로드할 수 있습니다.

- [안정](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [베타](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [알파](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [Edge](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

이 이미지는 이미 Azure에서 실행되도록 완전하게 설정되고 최적화되었습니다. 압축만 풀면 됩니다.

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>사용자 고유의 Azure용 Flatcar 기반 가상 머신 빌드

또는 사용자 고유의 Flatcar Container Linux 이미지를 빌드하도록 선택할 수도 있습니다.

Linux 기반 컴퓨터에서 [Flatcar Container linux 개발자 SDK 가이드](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/)에 설명된 지침을 따릅니다. `image_to_vm.sh` 스크립트를 실행할 때는 `--format=azure`를 전달하여 Azure 가상 하드 디스크를 만들어야 합니다.

## <a name="next-steps"></a>다음 단계

.vhd 파일이 있으면 결과 파일을 사용하여 Azure에서 새 가상 머신을 만들 수 있습니다. .vhd 파일을 Azure에 처음 업로드하는 경우 [사용자 지정 디스크에서 Linux VM 만들기](upload-vhd.md#option-1-upload-a-vhd)를 참조하세요.
