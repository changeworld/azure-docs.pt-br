---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Kronos | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Kronos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 5802df8bec8a0d938b132e099dd71fdb1d22a5b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727459"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kronos"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Kronos

Neste tutorial, você aprenderá a integrar o Kronos ao Azure AD (Azure Active Directory). Quando integrar o Kronos ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Kronos.
* Permitir que os usuários sejam conectados automaticamente ao Kronos com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Kronos habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Kronos é compatível com SSO iniciado por **IDP**

## <a name="adding-kronos-from-the-gallery"></a>Adicionando o Kronos da galeria

Para configurar a integração do Kronos ao Azure AD, você precisará adicioná-lo da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Kronos** na caixa de pesquisa.
1. Escolha **Kronos** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-kronos"></a>Configurar e testar o SSO do Azure AD para o Kronos

Configure e teste o SSO do Azure AD com o Kronos usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Kronos.

Para configurar e testar o SSO do Azure AD com o Kronos, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Kronos](#configure-kronos-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Kronos](#create-kronos-test-user)** – para ter um equivalente de B. Fernandes no Kronos que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Kronos**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar Logon Único com SAML**, insira os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<company name>.kronos.net/`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<company name>.kronos.net/wfc/navigator/logonWithUID`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do Kronos](https://www.kronos.in/contact/en-in/form) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Kronos espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![Captura de tela que mostra atributos de usuário com o ícone Editar selecionado.](common/edit-attribute.png)

1. Na seção **Declarações de Usuário** do diálogo **Atributos de Usuário**, configure o atributo de token SAML conforme mostrado na imagem acima e execute as seguintes etapas:

    a. Clique no **ícone Editar** para abrir a caixa de diálogo **Gerenciar declarações do usuário**.

    ![Captura de tela que mostra Atributos e Declarações do Usuário, com o ícone Editar selecionado.](./media/kronos-tutorial/tutorial_usermail.png)

    ![Captura de tela que mostra a caixa de diálogo Gerenciar declarações do usuário, na qual você pode inserir os valores descritos.](./media/kronos-tutorial/tutorial_usermailedit.png)

    b. Na lista **Transformação**, selecione **ExtractMailPrefix()** .

    c. Na lista **Parâmetro 1**, selecione **user.userprincipalname**.

    d. Clique em **Save** (Salvar).

1. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Kronos**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Kronos.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Kronos**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-kronos-sso"></a>Configurar o SSO do Kronos

Para configurar o logon único no lado do **Kronos**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Kronos](https://www.kronos.in/contact/en-in/form). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-kronos-test-user"></a>Criar um usuário de teste do Kronos

Nesta seção, você criará um usuário chamado Brenda Fernandes no Kronos. Trabalhe com a [equipe de suporte do Kronos](https://www.kronos.in/contact/en-in/form) para adicionar os usuários na plataforma do Kronos. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você deverá ser conectado automaticamente ao Kronos para o qual configurou o SSO

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Kronos em Meus Aplicativos, você deverá ser conectado automaticamente ao Kronos, para o qual você configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Kronos, você poderá impor o controle de sessão, que protege contra o vazamento e a infiltração dos dados confidenciais da organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)