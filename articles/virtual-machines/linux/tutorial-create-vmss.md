---
title: '자습서: Linux 가상 머신 확장 집합 만들기'
description: 가상 머신 확장 집합을 사용하여 고가용성 애플리케이션을 만들고 Linux VM에 배포하는 방법을 알아봅니다.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machines
ms.collection: linux
ms.date: 10/15/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 10f7202e2525920edd4c65b2e35cea51b9751abb
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165007"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux"></a>자습서: 가상 머신 확장 집합을 만들고 고가용성 앱을 Linux에 배포 

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 단일 확장 집합

[유연한 오케스트레이션](../flexible-virtual-machine-scale-sets.md)이 포함된 가상 머신 확장 집합을 사용하면 부하 분산된 VM의 그룹을 만들고 관리할 수 있습니다. VM 인스턴스의 수는 요구 또는 정의된 일정에 따라 자동으로 늘리거나 줄일 수 있습니다.

이 자습서에서는 Azure에서 가상 머신 확장 집합을 배포하고 다음에 대해 자세히 알아봅니다.

> [!div class="checklist"]
> * 리소스 그룹을 만듭니다.
> * 부하 분산 장치를 사용하여 유연한 확장 집합을 만듭니다.
> * nginx를 확장 집합 인스턴스에 추가합니다.
> * HTTP 트래픽에 대한 80 포트를 엽니다.
> * 확장 집합을 테스트합니다.


## <a name="scale-set-overview"></a>확장 집합 개요

확장 집합에서 제공하는 주요 이점은 다음과 같습니다.
- 손쉬운 여러 VM 만들기 및 관리
- 장애 도메인 간에 VM을 분산하여 고가용성 및 애플리케이션 복원력을 제공합니다
- 리소스 수요 변화에 따라 자동으로 애플리케이션 크기 조정
- 대규모 작업

Flexible 오케스트레이션을 사용하면 Azure는 Azure VM 에코시스템에서 통합된 환경을 제공합니다. Flexible 오케스트레이션은 지역 또는 가용성 영역 내의 장애 도메인에 VM을 분산하여 고가용성 보증(최대 1000개 VM)을 제공합니다. 이렇게 하면 다음을 비롯한 쿼럼 기반 또는 상태 저장 워크로드를 실행하는 데 필수적인 장애 도메인 격리를 유지하면서 애플리케이션을 스케일 아웃할 수 있습니다.
- 쿼럼 기반 워크로드
- 오픈 소스 데이터베이스
- 상태 저장 애플리케이션
- 고가용성 및 대규모를 필요로 하는 서비스
- 가상 머신 유형을 혼합하거나 스폿을 활용하고 온-디맨드 VM을 함께 활용하려는 서비스
- 기존 가용성 집합 애플리케이션

단일 확장 집합과 [오케스트레이션 모드](../../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md)의 유연한 확장 집합 간의 차이점에 대해 자세히 알아보세요.



## <a name="create-a-scale-set"></a>확장 집합 만들기

Azure Portal을 사용하여 유연한 확장 집합을 만듭니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. **가상 머신 확장 집합** 을 검색하여 선택합니다.
1. **가상 머신 확장 집합** 페이지에서 **만들기** 를 선택합니다. **가상 머신 확장 집합 만들기** 가 열립니다.
1. **구독** 에 사용하려는 구독을 선택합니다.
1. **리소스 그룹** 에 대해 **새로 만들기** 를 선택하고, 이름으로 *myVMSSRG* 를 입력한 다음, **확인** 을 선택합니다.
    :::image type="content" source="media/tutorial-create-vmss/flex-project-details.png" alt-text="프로젝트 세부 정보":::
1. **가상 머신 확장 집합 이름** 에 대해 *myVMSS* 를 입력합니다.
1. **지역** 에 대해 가까운 지역(예: *미국 동부*)을 선택합니다.
    :::image type="content" source="media/tutorial-create-vmss/flex-details.png" alt-text="이름 및 지역":::
1. 이 예에서는 **가용성 영역** 을 비워 둡니다.
1. **오케스트레이션 모드** 에 대해 **유연성** 을 선택합니다.
1. 장애 도메인 수에 대해 기본값인 *1* 을 그대로 두거나 드롭다운에서 다른 값을 선택합니다.
   :::image type="content" source="media/tutorial-create-vmss/flex-orchestration.png" alt-text="유연한 오케스트레이션 모드를 선택합니다.":::
1. **이미지** 에 대해 *Ubuntu 18.04 LTS* 를 선택합니다.
1. **크기** 에 대해 기본값을 그대로 두거나 크기(예: *Standard_E2s_V3*)를 선택합니다.
1. **사용자 이름** 에서 *azureuser* 를 입력합니다.
1. **SSH 공개 키 원본** 의 경우 기본값인 **새 키 쌍 생성** 을 그대로 둔 다음, **키 쌍 이름** 에 *myKey* 를 입력합니다.
    :::image type="content" source="media/tutorial-create-vmss/flex-admin.png" alt-text="인증 유형을 선택하고 관리자 자격 증명을 입력하는 관리자 계정 섹션의 스크린샷":::
1. **네트워킹** 탭의 **부하 분산** 아래에서 **부하 분산 장치 사용** 을 선택합니다.
1. **부하 분산 옵션** 에 대해 기본값인 **Azure 부하 분산 장치** 를 그대로 둡니다.
1. **부하 분산 장치 선택** 에 대해 **새로 만들기** 를 선택합니다.
    :::image type="content" source="media/tutorial-create-vmss/load-balancer-settings.png" alt-text="부하 분산 장치 설정":::
1. **부하 분산 장치 만들기** 페이지에서 부하 분산 장치에 대한 이름과 **공용 IP 주소 이름** 을 입력합니다.
1. **도메인 이름 레이블** 에 대해 도메인 이름에 대한 접두사로 사용할 이름을 입력합니다. 이 이름은 고유해야 합니다.
1. 완료되면 **만들기** 를 선택합니다.
    :::image type="content" source="media/tutorial-create-vmss/flex-load-balancer.png" alt-text="부하 분산 장치 만들기":::
1. **네트워킹** 탭으로 돌아가서 백 엔드 풀에 대한 기본 이름을 그대로 둡니다.
1. **크기 조정** 탭에서 기본 인스턴스 수를 *2* 로 그대로 두거나 사용자 고유의 값을 추가합니다. 이는 만들어지는 VM의 수이므로 이 값을 변경하는 경우 구독에 대한 비용과 제한을 알고 있어야 합니다.
1. **크기 조정 정책** 은 *수동* 으로 설정된 상태로 둡니다.
    :::image type="content" source="media/tutorial-create-vmss/flex-scaling.png" alt-text="크기 조정 정책 설정":::
1. **고급** 탭을 선택합니다.
1. **사용자 지정 데이터 및 클라우드 초기화** 아래에서 다음을 복사하여 **사용자 지정 데이터** 텍스트 상자에 붙여넣습니다.
    ```yml
    #cloud-config
    package_upgrade: true
    packages:
      - nginx
      - nodejs
      - npm
    write_files:
      - owner: www-data:www-data
      - path: /etc/nginx/sites-available/default
        content: |
          server {
            listen 80;
            location / {
              proxy_pass http://localhost:3000;
              proxy_http_version 1.1;
              proxy_set_header Upgrade $http_upgrade;
              proxy_set_header Connection keep-alive;
              proxy_set_header Host $host;
              proxy_cache_bypass $http_upgrade;
            }
          }
      - owner: azureuser:azureuser
      - path: /home/azureuser/myapp/index.js
        content: |
          var express = require('express')
          var app = express()
          var os = require('os');
          app.get('/', function (req, res) {
            res.send('Hello World from host ' + os.hostname() + '!')
          })
          app.listen(3000, function () {
            console.log('Hello world app listening on port 3000!')
          })
    runcmd:
      - service nginx restart
      - cd "/home/azureuser/myapp"
      - npm init
      - npm install express -y
      - nodejs index.js
    ```
1. 완료되면 **검토 + 만들기** 를 선택합니다.
1. 유효성 검사가 통과되면 페이지 아래쪽에서 **만들기** 를 선택하여 확장 집합을 배포할 수 있습니다.
1. **새 키 쌍 생성** 창이 열리면 **프라이빗 키 다운로드 및 리소스 만들기** 를 선택합니다. 키 파일은 **myKey.pem** 으로 다운로드됩니다. `.pem` 파일을 다운로드한 위치를 확인하고 다음 단계에서 해당 파일에 대한 경로가 필요합니다.
1. 배포가 완료되면 **리소스로 이동** 을 선택하여 확장 집합을 확인합니다.


## <a name="view-the-vms-in-your-scale-set"></a>확장 집합의 VM 보기

확장 집합 페이지의 왼쪽 메뉴에서 **인스턴스** 를 선택합니다. 

확장 집합에 속한 VM의 목록이 표시됩니다. 이 목록에는 다음이 포함됩니다.

- VM 이름
- VM에서 사용하는 컴퓨터 이름
- VM의 현재 상태(예: *실행 중*)
- VM의 *프로비전 상태*(예: *성공*)

:::image type="content" source="media/tutorial-create-vmss/instances.png" alt-text="확장 집합 인스턴스에 대한 정보 테이블":::


## <a name="open-port-80"></a>포트 80 열기 

인바운드 규칙을 NSG(네트워크 보안 그룹)에 추가하여 확장 집합에서 80 포트를 엽니다.

1. 확장 집합 페이지의 왼쪽 메뉴에서 **네트워킹** 을 선택합니다. **네트워킹** 페이지가 열립니다.
1. **인바운드 포트 규칙 추가** 를 선택합니다. **인바운드 보안 규칙 추가** 페이지가 열립니다.
1. **서비스** 아래에서 *HTTP* 를 선택한 다음, 페이지 아래쪽에서 **추가** 를 선택합니다.

## <a name="test-your-scale-set"></a>확장 집합 테스트

브라우저에서 확장 집합에 연결하여 테스트합니다.

1. 확장 집합에 대한 **개요** 페이지에서 공용 IP 주소를 복사합니다.
1. 브라우저에서 다른 탭을 열고, IP 주소를 주소 표시줄에 붙여넣습니다.
1. 페이지가 로드되면 표시되는 컴퓨팅 이름을 적어 둡니다. 
1. 컴퓨터 이름이 변경될 때까지 페이지를 새로 고칩니다. 

## <a name="delete-your-scale-set"></a>확장 집합 삭제

완료되면 리소스 그룹을 삭제해야 합니다. 그러면 확장 집합에 대해 배포한 모든 항목이 삭제됩니다.

1. 확장 집합에 대한 페이지에서 **리소스 그룹** 을 선택합니다. 리소스 그룹에 대한 페이지가 열립니다.
1. 페이지 위쪽에서 **리소스 그룹 삭제** 를 선택합니다.
1. **삭제하시겠습니까?** 페이지에서 리소스 그룹의 이름을 입력한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 가상 머신 확장 집합을 만들었습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 리소스 그룹을 만듭니다.
> * 부하 분산 장치를 사용하여 유연한 확장 집합을 만듭니다.
> * nginx를 확장 집합 인스턴스에 추가합니다.
> * HTTP 트래픽에 대한 80 포트를 엽니다.
> * 확장 집합을 테스트합니다.

Virtual Machines의 부하 분산 개념에 대해 자세히 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [Virtual Machines 부하 분산](tutorial-load-balancer.md)
