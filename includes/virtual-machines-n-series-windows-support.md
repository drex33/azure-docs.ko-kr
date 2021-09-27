---
title: 파일 포함
description: 포함 파일
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a188812751552532ae1536529b12e54d55a24a2b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "126056634"
---
## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla(CUDA) 드라이버

NC, NCv2, NCv3, NCasT4_v3, ND 및 NDv2 시리즈 VM(NV 시리즈의 경우는 선택적임)용 NVIDIA Tesla(CUDA) 드라이버는 다음 표에 나와 있는 운영 체제에서만 사용할 수 있습니다. 드라이버 다운로드 링크는 발표 시점에 제공된 링크입니다. 최신 드라이버에 대해서는 [NVIDIA](https://www.nvidia.com/) 웹 사이트를 참조하세요.

> [!TIP]
> Windows Server VM에서 수동 CUDA 드라이버를 설치하는 대신, Azure [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) 이미지를 배포할 수 있습니다. Windows Server 2016용 DSVM 에디션은 NVIDIA CUDA 드라이버, CUDA 심층 신경망 네트워크 라이브러리 및 기타 도구를 사전 설치합니다.


| OS | 드라이버 |
| -------- |------------- |
| Windows Server 2019 | [451.82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe)(.exe) |
| Windows Server 2016 | [451.82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe)(.exe) |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 드라이버

Microsoft는 가상 워크스테이션 또는 가상 애플리케이션으로 사용되는 NV 및 NVv3 시리즈 VM용 NVIDIA GRID 드라이버 설치 관리자를 재배포합니다. Azure NV 시리즈 VM에서 다음 표에 나열된 운영 체제에만 이러한 GRID 드라이버를 설치합니다. 이 드라이버에는 Azure의 GRID 가상 GPU 소프트웨어에 대한 라이선스가 포함됩니다. NVIDIA vGPU 소프트웨어 라이선스 서버를 설정할 필요가 없습니다.

Azure에서 재배포한 GRID 드라이버는 NCv2, NCv3, ND 및 NDv2 시리즈 VM과 같은 NV 이외의 시리즈 VM에서는 작동하지 않습니다. 한 가지 예외는 GRID 드라이버가 NV 시리즈와 유사한 그래픽 기능을 활성화하는 NCas_T4_V3 VM 시리즈입니다.

Nvidia K80 GPU가 설치된 NC 시리즈는 GRID/그래픽 애플리케이션을 지원하지 않습니다.  

Nvidia 확장은 항상 최신 드라이버를 설치합니다. 이전 버전을 사용하는 고객을 위해 여기에서 이전 버전에 대한 링크를 제공합니다.

Windows Server 2019, 1607, 1709 및 Windows 10 Windows Server 2016 (최대 빌드: 21h1):
- [그리드 13 (471.68)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe)
- [GRID 12.2(462.31)](https://download.microsoft.com/download/0/0/1/001f0edf-d852-4297-9cb7-10b31b1abf45/462.31_grid_win10_server2016_server2019_64bit_azure_swl.exe)(.exe) 

Windows Server 2012 R2의 경우: 
- [그리드 13 (471.68)](https://download.microsoft.com/download/9/b/4/9b4d4f8d-7962-4a67-839b-37cc95756759/471.68_grid_winserver2012R2_64bit_azure_swl.exe) (.exe)
- [GRID 12.2(462.31)](https://download.microsoft.com/download/1/2/0/120551f5-cc05-4911-bd29-88fb2747213c/462.31_grid_server2012R2_64bit_azure_swl.exe)(.exe) 


모든 이전 Nvidia GRID 드라이버 링크의 전체 목록을 보려면 [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json)를 방문하세요.
