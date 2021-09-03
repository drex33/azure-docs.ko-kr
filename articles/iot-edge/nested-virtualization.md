---
title: Azure IoT Edge for Linux on Windows의 중첩된 가상화 | Microsoft Docs
description: Azure IoT Edge for Linux on Windows의 중첩된 가상화를 탐색하는 방법에 대해 알아봅니다.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: e583808b0be0ff4105abc438ace1b52b9d3317eb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566351"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Azure IoT Edge for Linux on Windows의 중첩된 가상화
Azure IoT Edge for Linux on Windows와 호환되는 중첩된 가상화에는 두 가지 형식이 있습니다. 사용자는 로컬 VM이나 Azure VM을 통한 배포를 선택할 수 있습니다. 이 문서에서는 사용자에게 시나리오에 가장 적합한 옵션을 명확하게 설명하고 구성 요구 사항에 대한 인사이트를 제공합니다.

> [!NOTE]
>
> 중첩된 가상화에 대해 하나의 [네트워킹 옵션](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options)을 사용하도록 설정해야 합니다. 그러지 못하면 EFLOW 설치 오류가 발생합니다. 

## <a name="deployment-on-local-vm"></a>로컬 VM에 배포

이는 Azure IoT Edge for Linux on Windows를 호스팅하는 모든 Windows VM에 대한 기본 방법입니다. 이 경우 배포를 시작하기 전에 중첩된 가상화를 사용하도록 설정해야 합니다. 이 시나리오를 구성하는 방법에 대한 자세한 내용은 [중첩된 가상화를 사용하는 가상 머신에서 Hyper-V 실행](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)을 참조하세요.

Windows Server를 사용하는 경우 [Hyper-V 역할을 설치](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)해야 합니다.

## <a name="deployment-on-azure-vms"></a>Azure VM에 배포

Azure IoT Edge for Linux on Windows는 기본 스위치를 표시하는 스크립트가 실행되지 않는 한 서버 SKU를 실행하는 Azure VM 에서 호환되지 않습니다. 기본 스위치를 불러오는 방법에 대한 자세한 내용은 [Windows에서 Linux용 가상 스위치 만들기](how-to-create-virtual-switch.md)를 참조하세요.

> [!NOTE]
>
> EFLOW를 호스트해야 하는 모든 Azure VM은 중첩된 가상화를 [지원하는](../virtual-machines/acu.md) VM이어야 합니다.
