# 📘 09.05 Azure
## 📳 App Service
### SSH Connect
- Azure CLI로 SSH 연결
```bash
az ssh arc
```
- azcmagent 구성 수정
```bash
azcmagent config set
azcmagent config list
```
- Azure CLI로 NHN Ubuntu Server로 연결
```bash
az ssh arc --subscription "0e15e5c4-5af5-46fb-9ea3-8c1f11e002d5" --resource-group "rg-hybrid" --name "vmlinuxnhn" --local-user "ubuntu" --private-key-file [YOUR PRIVATE KEY]
```
### Create Container
- Azure Container
```bash
# Azure에 연결 (또는 포털 로그)
az login

# 레지스트리용 리소스 그룹 만들기 (또는 기존 리소스 그룹)
az group create --name rg-containerlab-[SUFFIX] --location koreacentral

# 기본 컨테이너 레지스트리를 만들기(또는 포털에서 만들기)
az acr create --resource-group rg-containerlab-[SUFFIX] --name cr[SUFFIX] --sku Basic

# ACR 인스턴스에 로그인
az acr login --name acrkdk

# 컨테이너 레지스트리 인스턴스의 전체 로그인 서버 이름 얻기
az acr list --resource-group rg-container --query "[].{acrLoginServer:loginServer}" --output table

# ACR로그인 서버의 정규화된 이름을 사용하여 이미지 태그 지정. 
# 레지스트리에 이미지를 푸시하려면 먼저 ACR로그인 서버의 정규화된 이름을 태그로 사용해야 한다.
docker tag guestbook-app acrkdk.azurecr.io/guestbook:v1

# 도커 이미지를 ACR에 등록하기
docker push acrkdk.azurecr.io/guestbook:v1

# 레지스트리의 리포지토리에서 업로드된 이미지 나열 (또는 포털 사용)
az acr repository list --name acrkdk --output table

# 리포지토리의 태그 나열
az acr repository show-tags --name acrkdk --repository guestbook --output table

# 컨테이너 레지스트리에서 guestbook-app:v1 컨테이너 이미지를 끌어와 실행
docker image rm acrkdk.azurecr.io/guestbook:v1

docker run -p 8080:3000 -d --name iuguestbook acrkdk.azurecr.io/guestbook:v1

# Admin 사용자 설정
#ACI나 AKS 등에서 ACR의 리포지토리를 액세스하려면 필수.
az acr update --name <acrName> --admin-enabled true

# Admin 사용자의 암호 쿼리
az acr credential show --name acrkdk --query "passwords[0].value"

# az cli로 빌드 및 배포를 한 방에 하기
az acr build --platform linux/amd64 -t acrkdk.azurecr.io/newguestbook:v2 -r acrkdk .
```