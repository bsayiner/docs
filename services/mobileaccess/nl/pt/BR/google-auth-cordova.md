---

copyright:
  years: 2015, 2016 lastupdated: "2016-10-02"
---
{:screen: .screen}
{:shortdesc: .shortdesc}

# Ativando a autenticação do Google para apps Cordova
{: #google-auth-cordova}


Para configurar aplicativos {{site.data.keyword.amafull}} Cordova para
integração de autenticação do Google, deve-se fazer mudanças no código nativo do
aplicativo Cordova (Java, Objective-C ou Swift). Cada plataforma deve ser configurada separadamente. Use o ambiente de desenvolvimento nativo para fazer mudanças no código nativo, por exemplo, no Android Studio ou no Xcode.

## Antes de Começar
{: #before-you-begin}
Você deve ter:
* Um projeto do Cordova que seja instrumentado com o {{site.data.keyword.amashort}} client SDK.  Para obter mais informações, veja [Configurando o plug-in do Cordova](https://console.{DomainName}/docs/services/mobileaccess/getting-started-cordova.html).  
* Uma instância de um aplicativo {{site.data.keyword.Bluemix_notm}} que seja protegida pelo serviço {{site.data.keyword.amashort}}. Para obter mais informações sobre como criar um aplicativo backend do {{site.data.keyword.Bluemix_notm}}, consulte [Introdução](index.html).
* (opcional) Familiarize-se com as seções a seguir:
   * [Ativando a autenticação do Google para apps Android](https://console.{DomainName}/docs/services/mobileaccess/google-auth-android.html)
   * [Ativando a autenticação do Google para apps iOS](https://console.{DomainName}/docs/services/mobileaccess/google-auth-ios.html)


## Configurando a plataforma Android
{: #google-auth-cordova-android}

As etapas necessárias para configurar a plataforma Android de um aplicativo Cordova para integração de autenticação do Google são muito semelhantes às etapas necessárias para aplicativos nativos. Veja
[Ativando
a autenticação do Google em apps Android](https://console.{DomainName}/docs/services/mobileaccess/google-auth-android.html) e configure o seguinte:

* Configurando o projeto do Google para a plataforma Android
* Configurando o {{site.data.keyword.amashort}} para autenticação do Google

### Configure o SDK do cliente {{site.data.keyword.amashort}} para Android Cordova.


2. Na pasta do projeto Android, abra o arquivo `build.gradle`
para o módulo do aplicativo (**não** o arquivo
`build.gradle` do projeto).
Localize a seção de dependências e inclua uma nova dependência de compilação para o Client SDK:

	```Gradle
	dependencies {
		compile group: 'com.ibm.mobilefirstplatform.clientsdk.android',    
        name:'googleauthentication',
        version: '1.+',
        ext: 'aar',
        transitive: true
    	// other dependencies  
	}
	```

2. Sincronize seu projeto com o Gradle clicando em **Ferramentas > Android > Sincronizar projeto com arquivos Gradle**.

3. Para aplicativos Cordova, inicialize o SDK do cliente {{site.data.keyword.amashort}} em seu código JavaScript em vez de no código Java. A API do `GoogleAuthenticationManager` ainda deve ser registrada no código nativo. Inclua
este código no método `onCreate` de atividade principal: 

	```Java
	GoogleAuthenticationManager.getInstance().registerDefaultAuthenticationListener(this);
	```

1. Inclua o código a seguir em sua Atividade:
 
 	```Java
	@Override
	protected void onActivityResult(int requestCode, int resultCode, Intent data) {
	     super.onActivityResult(requestCode, resultCode, data);
		GoogleAuthenticationManager.getInstance()
	         .onActivityResultCalled(requestCode, resultCode, data);
	}
	```

## Configurando a plataforma iOS
{: #google-auth-cordova-ios}

As etapas necessárias para configurar a plataforma iOS do aplicativo Cordova para integração de autenticação do Google são semelhantes às etapas para aplicativos nativos. A principal diferença é que atualmente Cordova CLI não suporta o gerenciador de dependência CocoaPods.  Deve-se incluir manualmente os arquivos necessários para integração com a autenticação do Google. Para
obter mais informações, veja
[Ativando
a autenticação do Google em apps iOS](https://console.{DomainName}/docs/services/mobileaccess/google-auth-ios.html). Execute as etapas a seguir:

* Configurando o projeto do Google para a plataforma iOS
* Configurando o {{site.data.keyword.amashort}} para autenticação do Google

### Instalando manualmente o {{site.data.keyword.amashort}} SDK para autenticação do Google e do Google SDK
{: #google-auth-cordova-ios-sdk}
1. Faça download do archive que contém o [{{site.data.keyword.Bluemix}} Mobile Services SDK for iOS](https://hub.jazz.net/git/bluemixmobilesdk/imf-ios-sdk/archive?revstr=master).

1. Acesse o diretório `Sources/Authenticators/IMFGoogleAuthentication` e copie (arraste e solte) todos os arquivos para seu projeto do iOS em Xcode. Os arquivos que você precisa copiar são:

	* IMFDefaultGoogleAuthenticationDelegate.h
	* IMFDefaultGoogleAuthenticationDelegate.m
	* IMFGoogleAuthenticationDelegate.h
	* IMFGoogleAuthenticationHandler.h
	* IMFGoogleAuthenticationHandler.m

	Selecione o **Copiar arquivos...** ?

1. Faça download e instale o [Google+ iOS SDK](http://goo.gl/9cTqyZ).

1. Siga a Etapa 2 do tutorial [Iniciar a integração do Google+ ao app iOS](https://developers.google.com/+/mobile/ios/getting-started) para integrar o Google+ iOS SDK ao projeto do Xcode.

Continue com a seção **Configurando o projeto do iOS para autenticação do Google** de [Configurando a plataforma iOS para autenticação do Google](https://console.{DomainName}/docs/services/mobileaccess/google-auth-ios.html). Registre o `IMFGoogleAuthenticationHandler` em código nativo, conforme descrito na seção `Inicializando o {{site.data.keyword.amashort}} client SDK`. Não
inicialize `IMFClient` em seu código nativo (o cliente é inicializado em JavaScript na seção a seguir).

Inclua esta linha no método `application:openURL:sourceApplication:annotation` do seu delegado do aplicativo. Isso
assegurará que todos os plug-ins Cordova sejam notificados dos respectivos eventos.

```
[[ NSNotificationCenter defaultCenter] postNotification:
		[NSNotification notificationWithName:CDVPluginHandleOpenURLNotification object:url]];      
```
{: codeblock}

## Inicializando o {{site.data.keyword.amashort}} client SDK
{: #google-auth-cordova-initialize}

Use o código JavaScript a seguir em seu aplicativo Cordova para inicializar o {{site.data.keyword.amashort}} client SDK.

```JavaScript
BMSClient.initialize("applicationRoute", "applicationGUID");
```
{: codeblock}

Substitua os valores `applicationRoute` e
`applicationGUID` pelos valores
**Route** e **AppGuid** do aplicativo. Esses
valores podem ser localizados clicando no botão **Opções móveis** de
dentro da página do aplicativo no painel.
	



##Inicializando o {{site.data.keyword.amashort}} AuthorizationManager
Use o código JavaScript a seguir no aplicativo Cordova para inicializar o
{{site.data.keyword.amashort}} AuthorizationManager.

```JavaScript
  MFPAuthorizationManager.initialize("tenantId");
```
{: codeblock}

Substitua o valor `tenantId` pelo `tenantId` do serviço
{{site.data.keyword.amashort}}. Esse valor pode ser localizado clicando no
botão **Mostrar credenciais** no quadro do serviço
{{site.data.keyword.amashort}}.





## Testando a Autenticação
{: #google-auth-cordova-test}
Após o SDK do cliente ser inicializado, será possível começar a fazer solicitações ao seu aplicativo backend móvel.

### Antes de Começar
{: #google-auth-cordova-testing-before}
Deve-se ter um aplicativo backend protegido pelo {{site.data.keyword.amashort}} no terminal `/protected`. Se for necessário configurar um terminal `/protected`, consulte [Protegendo recursos](https://console.{DomainName}/docs/services/mobileaccess/protecting-resources.html).


1. Tente enviar uma solicitação para um terminal protegido de seu aplicativo backend móvel no navegador da área de trabalho, abrindo `{applicationRoute}/protected`, por exemplo, `http://my-mobile-backend.mybluemix.net/protected`.

1. O terminal `/protected` de um aplicativo backend móvel criado com o Modelo MobileFirst Services é protegido com o {{site.data.keyword.amashort}}, portanto, só é possível acessá-lo por aplicativos móveis instrumentados com o {{site.data.keyword.amashort}} client SDK. Como resultado, você verá `Unauthorized` no navegador de sua área de trabalho.

1. Use seu aplicativo Cordova para fazer uma solicitação para o mesmo terminal. Inclua o código a seguir após inicializar `BMSClient`.

	```JavaScript
	var success = function(data){
    	console.log("success", data);
    }
	var failure = function(error)
    	{console.log("failure", error);
    }
	var request = new MFPRequest("/protected", MFPRequest.GET);
	request.send(success, failure);
	```
{: codeblock}

1. Execute o aplicativo. A tela de login do Google é exibida.

	![tela de login do Google](images/android-google-login.png)
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;	![tela
de login do Google](images/ios-google-login.png)
	
	Essa tela poderá parecer um pouco diferente se o app Facebook não estiver instalado em seu dispositivo, ou se você não estiver atualmente conectado ao Facebook.
1. Ao clicar em **OK**, você estará autorizando o {{site.data.keyword.amashort}} a usar sua identidade do usuário do Google para fins de autenticação.

1. Sua solicitação deve ser bem-sucedida. Dependendo da plataforma usada, você verá a saída a seguir no console LogCat/Xcode:

	![Fragmento de código no Android](images/android-google-login-success.png)

	![Fragmento de código no iOS](images/ios-google-login-success.png)
