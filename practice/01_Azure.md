# 📘 08.20 AWS
### Azure CLI Basic Command
```bash
# Azure CLI 버전 확인
az --version

# Azure CLI 수동 업그레이드
az upgrade

# Azure CLI 자동 업그레이드
az config set auto-upgrade.enable=yes

# 업그레이드 중 사용자 확인 인터럽트 방지
az config set auto-upgrade.prompt=no

# 자동 업그레이드 해제
az config set auto-upgrade.enable=no

# Azure 로그인
az login --output table
az login -o table
az login --use-device-code

# Azure 로그아웃
az logout

# Azure 구독 목록 조회
az account list -o table

# Azure 구독 변경
az account set --subscription "b88f99b0-1f3c-4529-86c5-f80b227c53ac"

# Azure 구독 정보 정리
az account clear

# Azure 공급자 확인
az provider show --namespace Microsoft.App | more
az provider show --namespace Microsoft.DataMigration -o table

# Azure 공급자 등록
az provider register --namespace Microsoft.App
az provider register --namespace Microsoft.DataMigration
```
### Azure CLI 구문 구성
```bash
# CLI 구문 구성
az reference [reference subservice] command parameter

# CLI에서 리소스 그룹 생성
az group create -l koreacentral -n rg-helloazure

# CLI에서 Public IP 생성
az network public-ip create -n pip-helloazure -g rg-helloazure
```