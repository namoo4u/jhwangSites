
DocCS

#### OAUTH_TOKEN

##### Curl
- Accept : application/xml or application/json
- Authorization:
  - Authorization: Basic abclajdlfa=
  - Authorization: Bearer eyJlad..adljfalsd
- Content-Length
- Content-Type
  - application/json
- Accept-Language: fr


##### Using OAuth
- base url : https://documents-krdemo.documents.us2.oraclecloud.com/documents/web?IdcService=GET_OAUTH_TOKEN
- Reponse
```
{

"LocalData": {
"IdcService": "GET_OAUTH_TOKEN",
"StatusCode": "0",
"StatusMessage": "You are logged in as 'U9133ACF5AE2AF282FD7F23A723FD68419FE'.",
"StatusMessageKey": "!csUserLoggedIn,U9133ACF5AE2AF282FD7F23A723FD68419FE",
"dUser": "U9133ACF5AE2AF282FD7F23A723FD68419FE",
"dUserFullName": "Jupil Hwang",
"expiration": "604799",
"idcToken": "1481347104797:C7968F3B2E31D84A029F65AF0FD970A9",
"localizedForResponse": "1",
"tokenValue": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsIng1dCI6IkxLRERYN2VyRUxYcm5fc0Zwc0RFZkt6ejg3YyIsImtpZCI6ImtyZGVtby5jZXJ0In0.eyJzdWIiOiJqdXBpbC5od2FuZ0BvcmFjbGUuY29tIiwib3JhY2xlLm9hdXRoLnVzZXJfb3JpZ2luX2lkX3R5cGUiOiJMREFQX1VJRCIsIm9yYWNsZS5vYXV0aC51c2VyX29yaWdpbl9pZCI6Imp1cGlsLmh3YW5nQG9yYWNsZS5jb20iLCJpc3MiOiJrcmRlbW8iLCJvcmFjbGUub2F1dGguc3ZjX3BfbiI6ImtyZGVtb1NlcnZpY2VQcm9maWxlIiwiaWF0IjoxNDgxMTc0MzA1LCJvcmFjbGUub2F1dGgucHJuLmlkX3R5cGUiOiJMREFQX1VJRCIsIm9yYWNsZS5vYXV0aC50a19jb250ZXh0IjoicmVzb3VyY2VfYWNjZXNzX3RrIiwiZXhwIjoxNDgxNzc5MTA0LCJhdWQiOlsiaHR0cHM6Ly9kb2N1bWVudHMta3JkZW1vLmRvY3VtZW50cy51czIub3JhY2xlY2xvdWQuY29tOjQ0My9kb2N1bWVudHMiXSwib3JhY2xlLm9hdXRoLnN1Yi5pZF90eXBlIjoiTERBUF9VSUQiLCJwcm4iOiJqdXBpbC5od2FuZ0BvcmFjbGUuY29tIiwianRpIjoiZDA0ZjQ2ODYtNjVhMy00YWIyLTk2NjItMmEzODA2M2I0ZTYzIiwib3JhY2xlLm9hdXRoLmNsaWVudF9vcmlnaW5faWQiOiJkZDdmMmYyNy0zN2QyLTRhMTktYjY3NS02ZWU0NWZhODk0NDUiLCJvcmFjbGUub2F1dGguc2NvcGUiOiJodHRwczovL2RvY3VtZW50cy1rcmRlbW8uZG9jdW1lbnRzLnVzMi5vcmFjbGVjbG91ZC5jb206NDQzL2RvY3VtZW50cyIsInVzZXIudGVuYW50Lm5hbWUiOiJrcmRlbW8iLCJvcmFjbGUub2F1dGguaWRfZF9pZCI6IjQ2NjkzMTY5NDk2MjI0MjA3In0.QUsPt6rXmGnVUyDxeMSKYhKxMFTSnuEqM5zybYlR9TZ1atkCDKdMNuC8euZp2SX45L43gVQbjUmyb-PdJ-jFYnv_c73QBZVcU7h8Kae2UrL39BGRF6dOmhp9b1fi6Ldi7L6GaK393MHgspel0v3vOlpDykISeis-arJEvYJsc10Bj5JO_ZFCP9AWVz4FwtpXFMtuiCiA2DtjAdQDo97qh0NYq2HFQCF4muTjV5KPrqi9_wRTNy4pdywezHLmk5PEqlsTGo2w7opo02t1WnlM6d1weGSr2hDKWv3WMn1WNN6moWNHmBKQk2tYNOe9690qWHo3WNYcDa1nBHDnO3OP3A"
},


"ResultSets": {

}
}
```
```
curl -iv -H 'Authorization:Bearer token' -X GET {Docs External URL}/api/1.1/folder/items
```

##### Using Baic Authorization
- Base64 encode
```
  jupil.hwang@oracle.com:W3lc0m31#

  -- encode -->
  Authorization:Basic anVwaWwuaHdhbmdAb3JhY2xlLmNvbTpXM2xjMG0zMSM=

```
```bash
curl -iv -H 'Authorization:Basic anVwaWwuaHdhbmdAb3JhY2xlLmNvbTpXM2xjMG0zMSM=' -X GET https://documents-krdemo.documents.us2.oraclecloud.com/documents/api/1.1/folders/items
```
Response
```json
{
"count": "1",
"errorCode": "0",
"hasMore": "0",
"limit": "100",
"offset": "0",
"ownerFolderID": "self",
"totalResults": "1",
"items": [
{
"type": "folder",
"id": "FC0370A6544CA32F53428D9D723FD68419FE3B212942",
"parentID": "self",
"name": "AutomaticWorkflow",
"ownedBy": {
"displayName": "Jupil Hwang",
"id": "U9133ACF5AE2AF282FD7F23A723FD68419FE",
"type": "user"
}
,
"createdBy": {
"displayName": "Jupil Hwang",
"id": "U9133ACF5AE2AF282FD7F23A723FD68419FE",
"type": "user"
}
,
"modifiedBy": {
"displayName": "Jupil Hwang",
"id": "U9133ACF5AE2AF282FD7F23A723FD68419FE",
"type": "user"
}
,
"createdTime": "2016-12-08T05:10:38Z",
"modifiedTime": "2016-12-08T05:10:38Z",
"size": "0",
"childItemsCount": "0"
}
]
}
```
