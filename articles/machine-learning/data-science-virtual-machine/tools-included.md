---
title: Azure Data Science Virtual Machine에 포함된 도구
titleSuffix: Azure Data Science Virtual Machine
description: Windows 및 Ubuntu DSVM 이미지에 포함된 도구 목록
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: reference
ms.date: 05/12/2021
ms.custom: contperf-fy20q4
ms.openlocfilehash: ba7006c9dc266819e4c965ae3a72fc0f78506056
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113649970"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine에 포함되는 도구는?

Data Science Virtual Machine은 클라우드에서 데이터를 검색하고 기계 학습을 수행하는 쉬운 방법입니다. Data Science Virtual Machine에는 완전한 운영 체제, 보안 패치, 드라이버 및 인기 있는 데이터 과학 및 개발 소프트웨어가 미리 구성되어 있습니다. 저렴한 CPU 중심 머신에서 여러 GPU, NVMe 스토리지 및 대용량 메모리를 갖춘 매우 강력한 머신에 이르기까지 다양한 하드웨어 환경을 선택할 수 있습니다. GPU가 있는 머신의 경우 모든 드라이버가 설치되고, 모든 기계 학습 프레임워크가 GPU 호환성을 위해 버전과 일치하며, GPU를 지원하는 모든 애플리케이션 소프트웨어에서 가속을 사용하도록 설정됩니다.

Data Science Virtual Machine에는 가장 유용한 데이터 과학 도구가 미리 설치되어 제공됩니다. 

## <a name="build-deep-learning-and-machine-learning-solutions"></a>딥 러닝 및 기계 학습 솔루션 빌드

| 도구 | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 사용 메모 |
|--|:-:|:-:|:-:|
| [CUDA, cuDNN, NVIDIA 드라이버](https://developer.nvidia.com/cuda-toolkit) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM의 CUDA, cuDNN, NVIDIA 드라이버](./dsvm-tools-deep-learning-frameworks.md#cuda-cudnn-nvidia-driver) |
| [Horovod](https://github.com/horovod/horovod) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> | [DSVM의 Horovod](./dsvm-tools-deep-learning-frameworks.md#horovod) |
| [NVidia System Management Interface(nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> | [DSVM의 nvidia-smi](./dsvm-tools-deep-learning-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| [PyTorch](https://pytorch.org) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> | [DSVM의 PyTorch](./dsvm-tools-deep-learning-frameworks.md#pytorch) |
| [Tensorflow](https://www.tensorflow.org) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM의 TensorFlow](./dsvm-tools-deep-learning-frameworks.md#tensorflow) |
| [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)과 통합(Python) | <span class='green-check'>&#9989;</span></br> (Python SDK, 샘플) | <span class='green-check'>&#9989;</span></br> (Python/R SDK,CLI, 샘플) | [Azure ML SDK](./dsvm-tools-data-science.md#azure-machine-learning-sdk-for-python) |
| [XGBoost](https://github.com/dmlc/xgboost) | <span class='green-check'>&#9989;</span></br> (CUDA 지원) | <span class='green-check'>&#9989;</span></br> (CUDA 지원) | [DSVM의 XGBoost](./dsvm-tools-data-science.md#xgboost) |
| [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span></br> | [DSVM의 Vowpal Wabbit](./dsvm-tools-data-science.md#vowpal-wabbit) |
| [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | <span class='red-x'>&#10060;</span> | <span class='red-x'>&#10060;</span> |  |
| LightGBM | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (GPU, MPI 지원) |  |
| H2O | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| CatBoost | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Intel MKL | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| OpenCV | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Dlib | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Docker | <span class='green-check'>&#9989;</span> <br/> (Windows 컨테이너 전용) | <span class='green-check'>&#9989;</span> |  |
| Nccl | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Rattle | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| ONNX Runtime | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |



## <a name="store-retrieve-and-manipulate-data"></a>데이터 저장, 검색 및 조작

| 도구 | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 사용 메모 |
|--|-:|:-:|:-:|
| 관계형 데이터베이스 | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) <br/> Developer Edition | [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) <br/> Developer Edition | [DSVM의 SQL Server](./dsvm-tools-data-platforms.md#sql-server-developer-edition) |
| 데이터베이스 도구 | SQL Server Management Studio<br/> SQL Server Integration Services<br/> [bcp, sqlcmd](/sql/tools/command-prompt-utility-reference-database-engine) | [SQuirreL SQL](http://squirrel-sql.sourceforge.net/)(쿼리 도구), <br /> bcp, sqlcmd <br /> ODBC/JDBC 드라이버 |  |
| [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [Azure CLI](/cli/azure) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [AZCopy](../../storage/common/storage-use-azcopy-v10.md) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> | [DSVM의 AzCopy](./dsvm-tools-ingestion.md#azcopy) |
| [Blob FUSE 드라이버](https://github.com/Azure/azure-storage-fuse) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> | [DSVM의 blobfuse](./dsvm-tools-ingestion.md#blobfuse) |
| [Azure Cosmos DB Data Migration Tool](../../cosmos-db/import-data.md) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> | [DSVM의 Cosmos DB](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| Unix/Linux 명령줄 도구 | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| Apache Spark 3.1(독립 실행형) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span></br> |  |

## <a name="program-in-python-r-julia-and-nodejs"></a>Python, R, Julia 및 Node.js의 프로그램

| 도구 | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 사용 메모 |
|--|:-:|:-:|:-:|
| [CRAN-R](https://cran.r-project.org/)(미리 설치된 주요 패키지 포함) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| [Anaconda Python](https://www.continuum.io/)(미리 설치된 주요 패키지 포함) | <span class='green-check'>&#9989;</span><br/> (Miniconda) | <span class='green-check'>&#9989;</span></br> (Miniconda) |  |
| [Julia(Julialang)](https://julialang.org/) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| JupyterHub(다중 사용자 노트북 서버) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| JupyterLab(다중 사용자 노트북 서버) | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| Node.js | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
| [Jupyter Notebook Server](https://jupyter.org/) - 다음 커널 포함: | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span> | [Jupyter Notebook 샘플](./dsvm-samples-and-walkthroughs.md) |
| &nbsp;&nbsp;&nbsp;&nbsp; R |  |  | [R Jupyter 샘플](./dsvm-samples-and-walkthroughs.md#r-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; Python |  |  | [Python Jupyter 샘플](./dsvm-samples-and-walkthroughs.md#python-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; Julia |  |  | [Julia Jupyter 샘플](./dsvm-samples-and-walkthroughs.md#julia-language) |
| &nbsp;&nbsp;&nbsp;&nbsp; PySpark |  |  | [pySpark Jupyter 샘플](./dsvm-samples-and-walkthroughs.md#sparkml) |

**Ubuntu 18.04 DSVM 및 Windows Server 2019 DSVM** 에는 다음과 같은 Jupyter 커널이 있습니다.</br> 
* Python 3.8 - 기본값</br>  
* Python 3.8 - PyTorch</br>  
* Python 3.8 - TensorFlow</br>  
* Python 3.6 - AzureML - TensorFlow</br>  
* Python 3.6 - AzureML - PyTorch</br>  
* Python 3.6 - AzureML – AutoML</br>  
* R</br>  
* Python 3.7 - Spark(로컬)</br>  
* Julia 1.2.0</br>  
* R Spark – HDInsight</br>  
* Scala Spark – HDInsight</br>  
* Python 3 Spark - HDInsight</br>  

**Ubuntu 18.04 DSVM 및 Windows Server 2019 DSVM** 에는 다음과 같은  Conda 환경이 있습니다.</br> 
* py38_default  </br>
* py38_tensorflow </br> 
* py38_pytorch  </br>
* azureml_py36_tensorflow  </br>
* azureml_py36_pytorch  </br>
* azureml_py36_automl  </br>


## <a name="use-your-preferred-editor-or-ide"></a>선호하는 에디터 또는 IDE 사용

| 도구 | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 사용 메모 |
|--|:-:|:-:|:-:|
| [메모장++](https://notepad-plus-plus.org/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span></br> |  |
| [Nano](https://www.nano-editor.org/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span></br> |  |
| [Visual Studio 2019 Community Edition](https://www.visualstudio.com/community/) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> | [DSVM의 Visual Studio](dsvm-tools-development.md#visual-studio-community-edition) |
| [Visual Studio Code](https://code.visualstudio.com/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM의 Visual Studio Code](./dsvm-tools-development.md#visual-studio-code) |
| [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM의 RStudio Desktop](./dsvm-tools-development.md#rstudio-desktop) |
| [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) <br/> (기본값으로 사용 안 함) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> | [DSVM의 PyCharm](./dsvm-tools-development.md#pycharm) |
| [IntelliJ IDEA](https://www.jetbrains.com/idea/) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span> |  |
| [Vim](https://www.vim.org) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> |  |
| [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> |  |
| [Git](https://git-scm.com/) 및 Git Bash | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| [OpenJDK](https://openjdk.java.net) 11 | <span class='green-check'>&#9989;</span></br> | <span class='green-check'>&#9989;</span></br> |  |
| .NET Framework | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> |  |
| Azure SDK | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |

## <a name="organize--present-results"></a>조직 및 현재 결과

| 도구 | Windows Server 2019 DSVM | Ubuntu 18.04 DSVM | 사용 메모 |
|--|:-:|:-:|:-:|
| [Microsoft 365](https://www.microsoft.com/microsoft-365)(Word, Excel, PowerPoint) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> |  |
| [Microsoft 팀](https://www.microsoft.com/microsoft-teams) | <span class='green-check'>&#9989;</span> | <span class='red-x'>&#10060;</span> |  |
| [Power BI Desktop](https://powerbi.microsoft.com/) | <span class='green-check'>&#9989;</span></br> | <span class='red-x'>&#10060;</span> |  |
| Microsoft Edge Browser | <span class='green-check'>&#9989;</span> | <span class='green-check'>&#9989;</span> |  |
