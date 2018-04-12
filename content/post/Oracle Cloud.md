

### Storage CS 만들기``
####. Authentication token 가져오기

```
curl -v -X GET -H 'X-Storage-User: Storage-<Identity Domain>:<User Name>' -H 'X-Storage-Pass:<Password>' https://storage.us2.oraclecloud.com/auth/v1.0
```
```
curl -v -X GET -H 'X-Storage-User: Storage-jcsdemo0060:cloud.admin' -H 'X-Storage-Pass: dIrty@7TiMBer' https://storage.us2.oraclecloud.com/auth/v1.0
```
< X-Auth-Token: AUTH_tk37abcede5b5e42bae85eb2d5bf811f2d
< X-Storage-Url: https://storage.us2.oraclecloud.com/v1/Storage-jcsdemo0060


#### Create a Container
```
curl -v -H 'X-Auth-Token:AUTH_tk37abcede5b5e42bae85eb2d5bf811f2d' -X PUT https://storage.us2.oraclecloud.com/v1/Storage-jcsdemo0060/HCMDEMO
```
< HTTP/1.1 201 Created


#### Storage CS 상태확인
```
curl -v -H 'X-Auth-Token:AUTH_tk37abcede5b5e42bae85eb2d5bf811f2d' -X GET https://storage.us2.oraclecloud.com/v1/Storage-jcsdemo0060/HCMDEMO
```


### DBaaS CS 만들기
```
curl -V -H 'identityDomainId:jcsdemo0060' -H 'Authorization: ' -H 'X-ID-TENANT_NAME:jcsdemo0060' -X POST /paas/service/dbcs/api/v1.1/instances/{identityDomainId} \
-D '
```
