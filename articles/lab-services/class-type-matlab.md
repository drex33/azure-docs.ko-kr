---
title: Azure Lab Services로 MATLAB을 교육하는 랩 설정 | Microsoft Docs
description: Azure Lab Services로 MATLAB을 교육하는 랩을 설정하는 방법을 알아봅니다.
author: emaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 8df8963f705fa8314ae3314af194f834deb69bdc
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176798"
---
# <a name="setup-a-lab-to-teach-matlab"></a>MATLAB을 교육하는 랩 설정

[MATLAB](https://www.mathworks.com/products/matlab.html)은 행렬 실험실을 의미하는 것으로, [MathWorks](https://www.mathworks.com/)의 프로그래밍 플랫폼입니다.  계산 기능과 시각화 기능이 결합되어 있어 수학, 엔지니어링, 물리학, 화학 분야에서 널리 사용되는 도구입니다.

[캠퍼스 전체 라이선스](https://www.mathworks.com/academia/tah-support-program/administrators.html)를 사용하는 경우 [MATLAB 설치 파일 다운로드](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine)의 지침을 참조하여 템플릿 머신에 MATLAB 설치 관리자 파일을 다운로드합니다.  

이 문서에서는 라이선스 서버를 통해 MATLAB 클라이언트 소프트웨어를 사용하는 클래스를 설정하는 방법을 보여 줍니다.

## <a name="license-server"></a>라이선스 서버

랩에 사용할 템플릿 머신을 수정하려면 먼저 서버에서 [네트워크 라이선스 관리자](https://www.mathworks.com/help/install/administer-network-licenses.html) 소프트웨어를 실행하도록 설정해야 합니다.  이 지침은 MATLAB에 대해 네트워킹 라이선스 옵션을 선택하여 사용자가 라이선스 키 풀을 공유할 수 있는 기관에만 적용됩니다.  라이선스 파일 및 파일 설치 키도 나중에 사용할 수 있도록 저장해야 합니다.  라이선스 파일을 다운로드하는 방법에 대한 자세한 지침은 [인터넷 연결을 사용하여 네트워크 라이선스 관리자 설치](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html) 문서의 첫 번째 단계를 참조하세요.

라이선스 서버를 설치하는 방법에 대한 자세한 지침은 [인터넷 연결을 사용하여 네트워크 라이선스 관리자 설치](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html)에서 확인할 수 있습니다.  대여를 사용하도록 설정하려면 [라이선스 대여](https://www.mathworks.com/help/install/license/borrow-licenses.html) 문서를 참조하세요.

라이선스 서버가 Azure 내의 온-프레미스 네트워크 또는 개인 네트워크에 있는 경우 [랩 계정](tutorial-setup-lab-account.md)에 [가상 네트워크를 피어링](how-to-connect-peer-virtual-network.md)하는 것을 잊지 마세요.  랩 가상 머신에서 라이선스 서버에 액세스할 수 있도록 랩을 만들기 전에 네트워크 피어링을 수행해야 합니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정하려면 Azure 구독을 시작해야 합니다.  Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수도 있고 기존 계정을 사용할 수도 있습니다.  새 랩 계정을 만들려면 [랩 계정 설정 자습서](tutorial-setup-lab-account.md)를 참조하세요.

새 랩을 만들려면 [클래스룸 랩 설정 자습서](tutorial-setup-classroom-lab.md)를 따르세요.  다음 설정을 적용합니다.

| 가상 머신 크기 | 이미지 |
| -------------------- | ----- |
| 중간 | Windows 10 |

MATLAB은 더 많은 운영 체제에서 지원됩니다.  자세한 내용은 [MATLAB 시스템 요구 사항](https://www.mathworks.com/support/requirements/matlab-system-requirements.html)을 참조하세요.

> [!WARNING]
> 랩을 만들기 전에 라이선스 서버의 가상 네트워크에 랩 계정의 [가상 네트워크를 피어링](https://www.mathworks.com/support/requirements/matlab-system-requirements.html)하는 것을 잊지 마세요.

## <a name="template-machine"></a>템플릿 머신

템플릿 머신을 만든 후에는 머신을 시작하고 연결하여 다음 주요 작업을 완료합니다.

1. MATLAB 클라이언트 소프트웨어의 설치 파일을 다운로드합니다.
2. 파일 설치 키를 사용하여 MATLAB을 설치합니다.

MATLAB 설치 프로세스는 여러 부분으로 이루어져 있습니다.  첫 번째 부분에서는 MATLAB 및 설치할 다른 모든 제품의 파일을 다운로드합니다.  파일 설치 키를 사용하려면 설치할 제품의 모든 설치 파일이 미리 다운로드되어 있어야 합니다.  두 번째 부분에서는 템플릿 VM에 MATLAB 소프트웨어를 설치하고 소프트웨어를 활성화합니다.  템플릿 VM이 라이선스 서버를 사용하여 활성화하도록 구성된 경우 학생 VM도 동일합니다.

### <a name="download-installation-files"></a>설치 파일 다운로드

설치 파일을 다운로드하고 라이선스 파일 및 파일 설치 키를 가져오려면 라이선스 관리자여야 합니다.  설치 파일을 다운로드하는 단계는 다음과 같습니다.

1. [https://www.mathworks.com](https://www.mathworks.com) 계정에 로그인합니다.
2. **My Account**(내 계정)를 선택합니다.
3. 계정 페이지의 **My Software**(내 소프트웨어) 섹션에서 랩의 네트워크 라이선스 관리자 설정에 연결된 라이선스를 클릭합니다.
4. 라이선스 세부 정보 페이지에서 **Download Products**(제품 다운로드)를 클릭합니다.
5. 설치 관리자가 자동 추출할 때까지 기다립니다.
6. 설치 관리자를 시작합니다.  
7. **Sign in to your MathWorks Account**(MathWorks 계정에 로그인) 페이지에서 MathWorks 계정을 입력합니다.
8. **MathWorks License Agreement**(MathWorks 사용권 계약) 페이지에서 사용 조건에 동의하고 **Next**(다음) 단추를 클릭합니다.
9. **Advanced Options**(고급 옵션) 드롭다운을 클릭하고 **I want to download without installing**(설치하지 않고 다운로드)을 선택합니다.
10. **Select destination folder**(대상 폴더 선택)에서 **Next**(다음)를 클릭합니다.
11. MATLAB을 설치할 컴퓨터의 플랫폼으로 **Windows** 를 선택합니다.
12. **Select product**(제품 선택) 페이지에서 MATLAB 및 설치할 다른 MathWorks 제품이 선택되어 있는지 확인합니다.
13. **Confirm Selections and Download**(선택 항목 확인 및 다운로드) 페이지에서 **Begin Download**(다운로드 시작)를 선택합니다.  
14. 선택한 제품이 다운로드될 때까지 기다립니다.  **Finish** 를 클릭합니다.

MathWorks 웹 사이트에서 ISO 이미지를 다운로드할 수도 있습니다.

1. [https://www.mathworks.com](https://www.mathworks.com) 계정에 로그인합니다.
2. [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads)으로 이동합니다.
3. 설치할 MATLAB 릴리스를 선택합니다.
4. 관련 링크 아래의 “Get {version}.iso image”({version}.iso 이미지 가져오기) 링크를 클릭합니다. 여기서 {version}에는 R2020a 등이 있습니다.
5. Windows용 파란색 **download release**(릴리스 다운로드) 링크를 클릭합니다.

### <a name="run-installer"></a>설치 관리자 실행

파일이 다운로드되면 두 번째 단계에서는 설치 관리자를 실행합니다. 마찬가지로, 이 단계를 완료하려면 라이선스 관리자여야 합니다.  라이선스 관리자만 파일 설치 키를 사용하여 MATLAB을 설치할 수 있습니다.

1. 다운로드한 라이선스 파일을 확인하고 서버 줄에 라이선스 서버가 올바르게 나열되는지 확인합니다.  라이선스 파일 형식 지정 방법에 대한 자세한 내용은 [네트워크 라이선스 업데이트](https://www.mathworks.com/help/install/ug/network-license-files.html), [라이선스 대여](https://www.mathworks.com/help/install/license/borrow-licenses.html) 및 [호스트 ID 찾기](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license) 문서를 참조하세요.
2. MATLAB 설치 관리자를 시작합니다.
3. **Sign in to your MathWorks Account**(MathWorks 계정에 로그인) 페이지에서 MathWorks 계정을 입력합니다.
4. **MathWorks License Agreement**(MathWorks 사용권 계약) 페이지에서 사용 조건에 동의하고 **Next**(다음) 단추를 클릭합니다.
5. **Advanced Options**(고급 옵션) 드롭다운을 클릭하고 **I have a File Installation Key**(파일 설치 키 있음)를 선택합니다.
6. **Install using File Installation Key**(파일 설치 키를 사용하여 설치) 페이지에서 라이선스 서버의 파일 설치 키를 입력합니다.   **다음** 을 클릭합니다.
7. **Select License File**(라이선스 파일 선택) 페이지에서 이전에 설치 파일을 다운로드할 때 저장한 라이선스 파일로 이동합니다.
8. **Select Destination Folder**(대상 폴더 선택) 페이지에서 **Next**(다음)를 클릭합니다.
9. **Select Products**(제품 선택) 페이지에서 **Next**(다음)를 클릭합니다.
10. **Select Options**(옵션 선택) 페이지에서 **Next**(다음)를 클릭합니다.
11. **Confirm Selections and Install**(선택 항목 확인 및 설치) 페이지에서 **Begin Install**(설치 시작)을 선택합니다.
12. **Installation Complete**(설치 완료) 페이지에서 **Activate MATLAB**(MATLAB 활성화)이 선택되어 있는지 확인합니다.  **Finish** 를 클릭합니다.

## <a name="cost-estimate"></a>예상 비용

이 수업의 가능한 예상 비용을 살펴보겠습니다.  이 예상치에는 라이선스 서버 실행 비용은 포함되지 않습니다.  학생이 25명인 수업을 사용합니다.  예약된 수업 시간은 20시간입니다.  예약된 수업 시간 외에도 숙제 또는 과제에 사용하도록 각 학생에게 10시간이 할당됩니다.  선택한 가상 머신 크기는 랩 단위가 55개인 중간 크기입니다.

다음 예제는 이 수업의 가능한 예상 비용입니다.

학생 25명 \* (예약 시간 20시간 + 할당 시간 10시간) \* 랩 단위 55개 \* 시간당 0.01 USD = 412.50 USD

>[!IMPORTANT]
> 예상 비용은 예제 용도로만 사용됩니다.  가격 책정에 대한 최신 세부 정보는 [Azure Lab Services 가격](https://azure.microsoft.com/pricing/details/lab-services/)을 참조하세요.  

## <a name="next-steps"></a>다음 단계

다음 단계는 모든 랩 설정에 공통됩니다.

- [템플릿 만들기, 관리, 게시](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)
