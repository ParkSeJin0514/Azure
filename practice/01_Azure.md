# 📘 09.02 Azure
## PowerShell CLI Commands
### PowerShell Basic Commands

```bash
# PowerShell 명령의 기본 형식
Get-Service -Name w32time

# PowerShell 도움말 시스템
Get-Help -Name [-detailed | -examples | -full | -online]

Update-Help -Module ServerManager, Microsoft.PowerShell.LocalAccounts

Update-Help –UICulture ko-KR, en-US

Update-Help –SourcePath \\Server01\Share\Help -Credential DokyunPC\steelflea

Save-Help –Module ServerManager -DestinationPath "C:\PowerShell_Lab\SavePSHelp" -Credential Dokyun-PC\steelflea

# 도움말 보는 방법
Get-Help -Name New-Alias -full

# PowerShell 명령을 찾고 빠르게 익히는 방법
Get-Command -Verb Get* -Noun Net*

Get-Help Get-NetAdapter -full

## 개체
# 개체의 멤버 확인 방법
Get-Process | get-Member –MemberType property, method

Get-Process | get-Member –MemberType Properties

## 파이프라인 시스템의 기본 개념
Get-Process | Out-File e:\process.txt

# 둘 이상의 개체가 혼합된 파이프라인 출력
Get-ChildItem –Path C:\Windows | Get-Member

## 명령의 파이프라인 지원 방식 
#ByValue를 사용한 바인딩
'Dhcp','EFS' | Get-Service

# ByPropertyName을 사용한 바인딩
Get-Service | Stop-Process

## 개체 선택 
# 명령의 결과 제한하기 
Get-Service | Select-Object –First 7
 
Get-Service | Select-Object –Last 7

Get-Service | Select-Object –skip 7

Get-Process | Select-Object –index 0,3 

Get-Process | Select-Object –index (3..6) 

Get-Process | gm 

# 표시할 속성 지정 
Get-Process | Select-Object –Property Name,ID,PM,VM

(Get-Process | Select-Object –Property Name,ID,PM,VM -Last 7)[0]

# 사용자 지정 속성 사용하기
Get-ChildItem | Select-Object -Property Name,@{n='Size(MB)'; e={$PSItem.Length/1MB}}

Get-Volume | Select-Object -Property DriveLetter,Size,SizeRemaining 

Get-Volume | Select-Object -Property DriveLetter,@{n='크기 정보';e={'전체 {0:N2} GB 
/ 남음 {1:N2} GB / 사용률 {2:P1}' -f ($PSItem.Size/1GB), ($PSItem.SizeRemaining/1GB), (1 - ($PSItem.SizeRemaining / $PSItem.Size))}} | Sort-Object -Property DriveLetter -Descending

# 결과
DriveLetter 크기 정보
----------- ---------
          D 전체 195.31 GB / 남음 162.88 GB / 사용률 16.6%
          C 전체 270.45 GB / 남음 58.14 GB / 사용률 78.5%
          
## 개체의 정렬과 계산 
# 개체를 정렬하는 Sort-Object 
Get-Process | Sort-Object -Property workingset

# 개체 컬렉션을 계산하는 Measure-Object 
Get-Process | Measure-Object –Property PM –Sum -Average

"Hello PowerShell" | Measure-Object -Character
 
## 개체 필터링 
# 기본 필터링 기법
Get-Service | Where-Object -Property Status –eq Running

Get-Service | Where-Object -Property Name.Length –gt 7

# 고급 필터링 기법 
Get-Service | Where-Object -FilterScript {$PSItem.Name.Length -gt 7}
Get-Service | Where-Object {$_.Name.Length -gt 7}
Get-Service | ? {$_.Name.Length -gt 7} | select -last 7

Get-Volume | Where-Object –Filter { $PSItem.HealthStatus –ne 'Healthy' -or $PSItem.SizeRemaining –lt 100MB } 
```