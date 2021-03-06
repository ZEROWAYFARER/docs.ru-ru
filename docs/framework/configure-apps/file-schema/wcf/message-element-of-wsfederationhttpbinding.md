---
title: "Элемент &lt;message&gt; &lt;wsFederationHttpBinding&gt; | Microsoft Docs"
ms.custom: ""
ms.date: "03/30/2017"
ms.prod: ".net-framework-4.6"
ms.reviewer: ""
ms.suite: ""
ms.technology: 
  - "dotnet-clr"
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 9d710389-d9d8-4454-9bf2-da4ccda31cec
caps.latest.revision: 28
author: "Erikre"
ms.author: "erikre"
manager: "erikre"
caps.handback.revision: 28
---
# Элемент &lt;message&gt; &lt;wsFederationHttpBinding&gt;
Определяет параметры безопасности уровня сообщений для элемента [\<wsFederationHttpBinding\>](../../../../../docs/framework/configure-apps/file-schema/wcf/wsfederationhttpbinding.md).  
  
## Синтаксис  
  
```  
  
<wsFederationBinding>  
     <binding >  
         <security>  
         <message   
            algorithmSuite="Basic128/Basic192/Basic256/Basic128Rsa15/Basic256Rsa15/TripleDes/TripleDesRsa15/Basic128Sha256/Basic192Sha256/TripleDesSha256/Basic128Sha256Rsa15/Basic192Sha256Rsa15/Basic256Sha256Rsa15/TripleDesSha256Rsa15"  
            issuedTokenType="string"   
            issuedKeyType="SymmetricKey/PublicKey"  
            negotiateServiceCredential="Boolean" >  
            <claimTypeRequirements>  
               <add claimType="URI"  
                    isOptional="Boolean" />  
            </claimTypeRequirements>  
                        <issuer address="Uri" >  
               <headers>  
                  <add name="String"  
                       namespace="String" />  
                          </headers>  
                              <identity>  
                              <certificate encodedValue="String"/>  
                                <certificateReference findValue="String"   
                                 isChainIncluded="Boolean"  
                            storeName="AddressBook/AuthRoot/CertificateAuthority/Disallowed/My/Root/TrustedPeople/TrustedPublisher"  
                                  storeLocation="LocalMachine/CurrentUser"  
                     x509FindType=System.Security.Cryptography.X509certificates.X509findtype/>  
                                   <dns value="String"/>  
                                <rsa value="String"/>  
                                <servicePrincipalName value="String"/>  
                                <usePrincipalName value="String"/>  
                              </identity>  
                        </issuer>  
                        <issuerMetadata address=String" >  
               <headers>  
                  <add name="String"  
                       namespace="String" />  
               </headers>  
               <identity>  
                  <certificate encodedValue="String"/>  
                  <certificateReference findValue="String"   
                     isChainIncluded="Boolean"  
                     storeName="AddressBook/AuthRoot/CertificateAuthority/Disallowed/My/Root/TrustedPeople/TrustedPublisher"  
                     storeLocation="LocalMachine/CurrentUser"  
                     X509FindType=System.Security.Cryptography.X509certificates.X509findtype/>  
                  <dns value="String"/>  
                  <rsa value="String"/>  
                  <servicePrincipalName value="String"/>  
                  <usePrincipalName value="String"/>  
               </identity>  
                        </issuerMetadata>  
            <tokenRequestParameters>  
               <xmlElement>  
               </xmlElement>  
            </tokenRequestParameters>  
         </message>  
      </security>  
   </binding>  
</wsFederationBinding>  
```  
  
## Атрибуты и элементы  
 В следующих разделах описаны атрибуты, дочерние и родительские элементы.  
  
### Атрибуты  
  
|Атрибут|Описание|  
|-------------|--------------|  
|algorithmSuite|Задает алгоритмы шифрования сообщений и ключей.  Допустимые значения этого атрибута см. в таблице "Атрибут algorithmSuite".  Значение по умолчанию — `Basic256`.<br /><br /> Это атрибут типа <xref:System.ServiceModel.Security.SecurityAlgorithmSuite>.  Эти алгоритмы соответствуют алгоритмам, заданным в спецификации языка политики безопасности \(WS\-SecurityPolicy\).|  
|issuedKeyType|Задает тип выдаваемого ключа.  Допустимы следующие значения:<br /><br /> -   SymmetricKey<br />-   PublicKey<br /><br /> Значение по умолчанию — `SymmetricKey`.  Это атрибут типа <xref:System.IdentityModel.Tokens.SecurityKeyType>.|  
|issuedTokenType|Строка, содержащая универсальный код ресурса \(URI\), который задает тип выдаваемых маркеров.  Значение по умолчанию — `null`.|  
|negotiateServiceCredential|Логическое значение, которое определяет, должен ли проводиться обмен учетными данными службы в рамках процесса согласования, или допустимо использование внештатного канала.  Значением по умолчанию является `true`, означающее, что учетные данные службы согласуются.|  
  
## Атрибут algorithmSuite  
  
|Значение|Описание|  
|--------------|--------------|  
|Basic128|Используется шифрование Basic128, Sha1 для хэша и Rsa\-oaep\-mgf1p для шифрования ключа.|  
|Basic192|Используется шифрование Basic192, Sha1 для хэша и Rsa\-oaep\-mgf1p для шифрования ключа.|  
|Basic256|Используется шифрование Basic256, Sha1 для хэша и Rsa\-oaep\-mgf1p для шифрования ключа.|  
|Basic256Rsa15|Используется Basic256 для шифрования сообщения, Sha1 для хэша и Rsa15 для шифрования ключа.|  
|Basic192Rsa15|Используется Basic192 для шифрования сообщения, Sha1 для хэша и Rsa15 для шифрования ключа.|  
|TripleDes|Используется шифрование TripleDes, Sha1 для хэша и Rsa\-oaep\-mgf1p для шифрования ключа.|  
|Basic128Rsa15|Используется Basic128 для шифрования сообщения, Sha1 для хэша и Rsa15 для шифрования ключа.|  
|TripleDesRsa15|Используется TripleDes для шифрования сообщения, Sha1 для хэша и Rsa15 для шифрования ключа.|  
|Basic128Sha256|Используется Basic128 для шифрования сообщения, Sha256 для хэша и Rsa\-oaep\-mgf1p для шифрования ключа.|  
|Basic192Sha256|Используется Basic192 для шифрования сообщения, Sha256 для хэша и Rsa\-oaep\-mgf1p для шифрования ключа.|  
|Basic256Sha256|Используется Basic256 для шифрования сообщения, Sha256 для хэша и Rsa\-oaep\-mgf1p для шифрования ключа.|  
|TripleDesSha256|Используется TripleDes для шифрования сообщения, Sha256 для хэша и Rsa\-oaep\-mgf1p для шифрования ключа.|  
|Basic128Sha256Rsa15|Используется Basic128 для шифрования сообщения, Sha256 для хэша и Rsa15 для шифрования ключа.|  
|Basic192Sha256Rsa15|Используется Aes192 для шифрования сообщения, Sha256 для хэша и Rsa15 для шифрования ключа.|  
|Basic256Sha256Rsa15|Используется Basic256 для шифрования сообщения, Sha256 для хэша и Rsa15 для шифрования ключа.|  
|TripleDesSha256Rsa15|Используется TripleDes для шифрования сообщения, Sha256 для хэша и Rsa15 для шифрования ключа.|  
  
### Дочерние элементы  
  
|Элемент|Описание|  
|-------------|--------------|  
|[\<claimTypeRequirements\>](../../../../../docs/framework/configure-apps/file-schema/wcf/claimtyperequirements-element.md)|Задает коллекцию типов утверждений для этой привязки.  Каждый элемент имеет тип <xref:System.ServiceModel.Configuration.ClaimTypeElement>.|  
|issuer|Задает конечную точку, которая выдает маркер безопасности.  Это элемент типа <xref:System.ServiceModel.Configuration.IssuedTokenParametersEndpointAddressElement>.|  
|issuerMetadata|Задает адрес конечной точки издателя.|  
|[\<tokenRequestParameters\>](../../../../../docs/framework/configure-apps/file-schema/wcf/tokenrequestparameters.md)|Коллекция параметров запроса маркера.  Каждый параметр представляет собой элемент XML.|  
  
### Родительские элементы  
  
|Элемент|Описание|  
|-------------|--------------|  
|[\<безопасность\>](../../../../../docs/framework/configure-apps/file-schema/wcf/security-of-wsfederationhttpbinding.md)|Определяет параметры безопасности для привязки.|  
  
## См. также  
 <xref:System.ServiceModel.FederatedMessageSecurityOverHttp>   
 <xref:System.ServiceModel.Configuration.WSFederationHttpSecurityElement.Message%2A>   
 <xref:System.ServiceModel.WSFederationHttpSecurity.Message%2A>   
 <xref:System.ServiceModel.Configuration.FederatedMessageSecurityElement>   
 [Защита служб и клиентов](../../../../../docs/framework/wcf/feature-details/securing-services-and-clients.md)   
 [Привязки](../../../../../docs/framework/wcf/bindings.md)   
 [Настройка привязок, предоставляемых системой](../../../../../docs/framework/wcf/feature-details/configuring-system-provided-bindings.md)   
 [Using Bindings to Configure Windows Communication Foundation Services and Clients](http://msdn.microsoft.com/ru-ru/bd8b277b-932f-472f-a42a-b02bb5257dfb)   
 [\<привязка\>](../../../../../docs/framework/misc/binding.md)