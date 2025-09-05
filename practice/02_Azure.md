# 📘 09.03 Azure
## 🧾 Azure Create Server With CLI
### Azure 방법 1
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
az account set --subscription "subscription id"

# Azure 구독 정보 정리
az account clear

# Azure 공급자 확인
az provider show --namespace Microsoft.App | more
az provider show --namespace Microsoft.DataMigration -o table

# Azure 공급자 등록
az provider register --namespace Microsoft.App
az provider register --namespace Microsoft.DataMigration
```
### Azure 방법 2
```bash
# Azure PowerShell 설치
Install-module -Name Az -AllowClobber

# PowerShell Script 외부 권한 확인
Get-ExecutionPolicy

# PowerShell Script 외부 권한 허용
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Currentuser

# Azure 연결
Connect-AzAccount

# Azure 구독 확인
Get-AzSubscription

# Azure 구독 선택
Select-AzSubscription -Subscription (Get-AzSubscription).Id

# 리소스 공급자 확인
Get-AzResourceProvider -ProviderNamespace Microsoft.DataMigration | format-table

# 리소스 공급자 등록
Register-AzResourceProvider -ProviderNamespace Microsoft.DataMigration
```
### Resource Group, VPC 생성

```bash
# CLI 구문 구성
az reference [reference subservice] command parameter

# 리소스 그룹
az group create -l koreacentral -n rg-hallofarmor

# 가상 네트워크
az network vnet create -n vnet-hallofarmor-kr -g rg-hallofarmor --address-prefixes 172.16.0.0/16 --subnet-name snet-jarvis --subnet-prefixes 172.16.2.0/24 -o table
```

### CLI로 변수 사용

```bash
# 공통
$location = "koreacentral"

# 리소스 그룹
$rg = $(az group list --query "[?contains(name, 'hall')].name" -o tsv)

# 가상 네트워크
$vnet = $(az network vnet list --query "[?contains(name, 'hall')].name" -o tsv)
$subnet = $(az network vnet subnet list -g $rg --vnet-name $vnet --query "[?contains(name, 'jarvis')].name" -o tsv)

# 네트워크 보안 그룹
az network nsg create -g $rg -n nsg-vmjarvisbe -l $location

# 공용 IP 주소와 NSG가 연결된 가상 네트워크 카드 만들기
az network nic create -g $rg --vnet-name $vnet --subnet $subnet -n nic-02-vmjarvisbe --network-security-group nsg-vmjarvisbe

# 가상 머신 만들기
az vm create -n vmjarvisbe -g $rg --admin-username tony --authentication-type ssh --generate-ssh-keys --image Ubuntu2204 --nics nic-02-vmjarvisbe --size Standard_DS1_v2

# 가상 머신 연결 (컨트롤 VM에서)
ren $home\.ssh\id_rsa vmjarvisbe_key
# $pvIp = $(az network nic list -g rg-hallofarmor --query "[?contains(name, 'nic-02')].ipConfigurations[].privateIPAddress" -o tsv)

# ssh -i ~/.ssh/vmjarvisbe_key tony@$pvIp

# NGINX 설치 및 샘플 페이지 생성
# 패키지 소스 업데이트
# sudo apt-get -y update

# NGINX 설치
# sudo apt-get -y install nginx

# index.html 파일 만들기
# sudo sh -c 'echo "Running Jarvis Foundation Models from host $(hostname)" > /var/www/html/index.html'
```
### Azure 기본 서버 CLI 명령어
```bash
# 공통
$location = "koreacentral"
$TimeZone ="Korea Standard Time"

# 리소스 그룹
$rg = Get-AzResourceGroup -Name "rg-hallofarmor" -Location $location

# 가상 네트워크
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $rg.ResourceGroupName `
  -Name "vnet-hallofarmor-kc"

# 공용 IP 주소 만들기
$pip = New-AzPublicIpAddress `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "pip-vmjarvisfe"

# 네트워크 보안 그룹
# 포트 3389에 대한 인바운드 네트워크 보안 그룹 규칙 만들기
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
-Name "RDP"  `
-Protocol "Tcp" `
-Direction "Inbound" `
-Priority 1000 `
-SourceAddressPrefix * `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 3389 `
-Access "Allow"

# 포트 80에 대한 인바운드 네트워크 보안 그룹 규칙 만들기
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
-Name "WWW"  `
-Protocol "Tcp" `
-Direction "Inbound" `
-Priority 1001 `
-SourceAddressPrefix * `
-SourcePortRange * `
-DestinationAddressPrefix * `
-DestinationPortRange 80 `
-Access "Allow"

# 네트워크 보안 그룹 만들기
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $location `
-Name "nsg-vmjarvisfe" `
-SecurityRules $nsgRuleRDP,$nsgRuleWeb

# 공용 IP 주소와 NSG가 연결된 가상 네트워크 카드 만들기
$nic = New-AzNetworkInterface `
  -Name "nic-01-vmjarvisfe" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
  
# 가상 머신 구성 
# 자격증명 객체 정의
$securePassword = ConvertTo-SecureString 'Pa55w.rdKTAz' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("tony", $securePassword)

# 가상 머신 구성 만들기
$vmConfig = New-AzVMConfig `
  -VMName "vmjarvisfe" `
  -VMSize "Standard_B2ms" `
  -Securitytype "TrustedLaunch" | `
Set-AzVMOperatingSystem `
  -Windows `
  -ComputerName "vmjarvisfe" `
  -Credential $cred `
  -TimeZone $TimeZone | `
Set-AzVMSourceImage `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2019-Datacenter-gensecond" `
  -Version "latest" | `
Add-AzVMNetworkInterface `
  -Id $nic.Id | `
Set-AzVMBootDiagnostic -Disable

# VM 배포
New-AzVM `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $location -VM $vmConfig -Verbose

# VM 공용 IP 확인
# $PubIp = (Get-AzPublicIpAddress -Name pip-vmjarvisfe).IpAddress   

# VM SSH 연결
# mstsc /v:$PubIp

# IIS 설치 및 샘플 페이지 생성
# Install-WindowsFeature -Name Web-Server -IncludeManagementTools
# Set-Content -Path "C:\inetpub\wwwroot\Default.htm" -Value "Running JARVIS Web Service from host $($env:computername) !"
```