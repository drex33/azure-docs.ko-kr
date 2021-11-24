---
title: '빠른 시작: Azure Network Function Manager에 대한 디바이스 리소스 만들기'
description: 이 빠른 시작에서는 Azure Network Function Manager에 대한 디바이스 리소스를 만드는 방법에 대해 알아봅니다.
author: prmitt
ms.service: network-function-manager
ms.topic: quickstart
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021, mode-other
ms.openlocfilehash: 99ee9c6e6c80cdf06742c5342ce5f390ca3c351b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133073962"
---
# <a name="quickstart-create-a-network-function-manager-device-resource"></a>빠른 시작: 네트워크 함수 관리자 디바이스 리소스 만들기

이 자습서에서는 Azure NFM(Network Function Manager)용 **디바이스** 리소스를 만듭니다. Network Function Manager 디바이스 리소스는 Azure Stack Edge 리소스에 연결됩니다. 디바이스 리소스는 Azure Stack Edge 디바이스에 배포된 모든 네트워크 함수를 집계하고 Azure Stack Edge에 배포된 모든 네트워크 함수에 대한 배포, 모니터링, 문제 해결, 일관된 관리 작업을 위해 공통 서비스를 제공합니다. Network Function Manager 디바이스 리소스를 먼저 만들어야 Azure Stack Edge 디바이스에 네트워크 함수를 배포할 수 있습니다.

## <a name="prerequisites"></a><a name="pre"></a>필수 조건

다음 필수 조건을 확인합니다.

* 활성 구독이 있는 Azure 계정이 있는지 확인 합니다. 계정이 필요한 경우 [무료로 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [필수 구성 요소 및 요구 사항](requirements.md) 문서에 나열된 모든 필수 조건을 충족했습니다.
* 적절한 권한이 할당되었습니다. 자세한 내용은 [리소스 공급자 등록 및 권한](resources-permissions.md)을 참조하세요.
* 디바이스 리소스를 만들기 전에  [지역 가용성](overview.md#regions)  섹션을 검토합니다.
* PowerShell을 통해 원격으로 Windows 클라이언트에서 Azure Stack Edge Pro GPU 디바이스로 연결할 수 있는지 확인합니다. 자세한 내용은 [PowerShell 인터페이스에 연결](../databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)을 참조하세요.

## <a name="create-a-device-resource"></a><a name="create"></a>디바이스 리소스 만들기

기존 Azure Network Function Manager 디바이스 리소스가 있으면 새로 만들지 않아도 됩니다. 이 섹션을 건너뛰고 [등록 키](#key) 섹션으로 이동합니다.

**디바이스** 리소스를 만들려면 다음 단계를 수행합니다.

1. Microsoft Azure 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **기본 사항** 탭에서 디바이스 설정을 사용하여 **프로젝트 세부 정보** 와 **인스턴스 세부 정보** 를 구성합니다.
   :::image type="content" source="./media/create-device/device-settings.png" alt-text="디바이스 설정 스크린샷":::

   필드에 입력할 때 추가한 문자의 유효성이 검사되면 녹색 확인 표시가 나타납니다. 일부 세부 정보는 자동으로 채워지고 다른 세부 정보는 사용자 지정 가능한 필드입니다.

   * **구독:** 나열된 구독이 올바른지 확인합니다. 드롭다운을 사용하여 구독을 변경할 수 있습니다.
   * **리소스 그룹:** 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 클릭하여 새로 만듭니다.
   * **지역:** 디바이스의 위치를 선택합니다. 지원되는 지역이어야 합니다. 자세한 내용은 [지역 가용성](overview.md#regions)을 참조하세요.
   * **이름:** 디바이스 이름을 입력합니다.
   * **Azure Stack Edge:** Azure Network Function Manager용 디바이스로 등록하려는 Azure Stack Edge 리소스를 선택합니다. 디바이스 리소스를 성공적으로 만들려면 ASE 리소스가 **온라인** 상태여야 합니다. Azure Stack Edge 리소스 페이지의 **속성** 섹션으로 이동하면 ASE 리소스의 상태를 확인할 수 있습니다.
1. **검토 + 만들기** 를 선택하여 디바이스 설정의 유효성을 검사합니다.
1. 모든 설정의 유효성을 검사한 후 **만들기** 를 선택합니다.
   
   >[!NOTE]
   >Network Function Manager 디바이스 리소스는 하나의 Azure Stack Edge 리소스에만 연결할 수 있습니다. 각 Azure Stack Edge 리소스에 대해 별도의 NFM 디바이스 리소스를 만들어야 합니다.
   >

## <a name="get-the-registration-key"></a><a name="key"></a>등록 키 가져오기

1. 디바이스가 성공적으로 프로비저닝되면 디바이스 리소스가 배포된 리소스 그룹으로 이동합니다.
1. **디바이스** 리소스를 클릭합니다. 등록 키를 가져오려면 **등록 키 가져오기** 를 클릭합니다. 등록 키를 생성할 수 있는 적절한 권한이 있는지 확인합니다. 자세한 내용은 [리소스 공급자 등록 및 권한](resources-permissions.md) 문서를 참조하세요.

   :::image type="content" source="./media/create-device/register-device.png" alt-text="등록 키 스크린샷" lightbox="./media/create-device/register-device.png":::
1. 다음 단계에서 사용할 디바이스 등록 키를 기록해 둡니다.

   > [!IMPORTANT]
   > 등록 키는 생성된 후 24시간이 지나면 만료됩니다. 등록 키가 더 이상 활성 상태가 아니면 이 섹션의 단계를 반복하여 새 등록 키를 생성할 수 있습니다.
   >

## <a name="register-the-device"></a><a name="registration"></a>디바이스 등록

Azure Stack Edge에 디바이스 리소스를 등록하려면 다음 단계를 수행합니다.

1. 이전 단계에서 얻은 등록 키를 사용하여 디바이스를 등록하려면 [Windows PowerShell을 통해 Azure Stack Edge 디바이스에 연결](../databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)합니다.

1. 어플라이언스에 대한 PowerShell(minishell) 연결이 있는 경우 이전 단계에서 얻은 디바이스 등록 키를 매개 변수로 사용하여 다음 명령을 입력합니다.
   ```powershell
   Invoke-MecRegister <device-registration-key>
   ```

1. 디바이스 리소스에 **디바이스 상태 = 등록됨** 이 있는지 확인합니다.

   :::image type="content" source="./media/create-device/device-registered.png" alt-text="등록된 디바이스 스크린샷" lightbox="./media/create-device/device-registered.png":::
 
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [네트워크 기능 배포](deploy-functions.md)
