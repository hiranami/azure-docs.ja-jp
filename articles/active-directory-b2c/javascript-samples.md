---
title: JavaScript のサンプル - Azure Active Directory B2C |Microsoft Docs
description: Azure Active Directory B2C で JavaScript を使用する方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 42dc09ef4518bfda8c63ee183499b1b2e8c22991
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841933"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で使用するための JavaScript のサンプル

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

独自の JavaScript クライアント側コードを、Azure Active Directory B2C (Azure AD B2C) アプリケーションに追加できます。 アプリケーションで JavaScript を有効にするには、[カスタム ポリシー](custom-policy-overview.md)に要素を追加し、[ページ レイアウト](page-layout.md)を選択して、要求内で [b2clogin.com](b2clogin.md) を使用します。 このアーティクルでは、カスタム ポリシー を変更してスクリプトの実行を有効にする方法について説明します。

> [!NOTE]
> ユーザー フローに対して JavaScript を有効にする場合、「[Azure Active Directory B2C における JavaScript とページ レイアウトのバージョン](user-flow-javascript-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

### <a name="select-a-page-layout"></a>ページ レイアウトを選択する

* アプリケーションのユーザー インターフェイスの要素に[ページ レイアウトを選択します](page-layout.md)。

    JavaScript を使用する場合は、カスタム ポリシー内の*すべて*のコンテンツ定義に対して[ページ レイアウト バージョンを定義する](page-layout.md#replace-datauri-values)必要があります。

## <a name="add-the-scriptexecution-element"></a>ScriptExecution 要素を追加する

**ScriptExecution** 要素を [RelyingParty](relyingparty.md) 要素に追加することで、スクリプトの実行を有効にします。

1. カスタム ポリシー ファイルを開きます。 たとえば、*SignUpOrSignin.xml* などです。
2. **ScriptExecution** 要素を **RelyingParty** の **UserJourneyBehaviors** 要素に追加します。

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. このファイルを保存してアップロードします。

## <a name="guidelines-for-using-javascript"></a>JavaScript の使用に関するガイドライン

JavaScript を使用して、アプリケーションのインターフェイスをカスタマイズするときに、次のガイドラインに従います。

- `<a>` HTML 要素ではクリック イベントをバインドしないでください。
- Azure AD B2C コードやコメントへの依存関係は使用しないでください。
- Azure AD B2C の HTML 要素の順序や階層は変更しないでください。 UI 要素の順序を制御するには、Azure AD B2C のポリシーを使用します。
- 任意の RESTful サービスを呼び出すことができますが、次の考慮事項があります。
    - クライアント側の HTTP 呼び出しを許可するために、RESTful サービス CORS を設定することが必要な場合があります。
    - RESTful サービスは必ずセキュリティ保護し、HTTPS プロトコルのみを使用してください。
    - JavaScript を直接使用して Azure AD B2C のエンドポイントを呼び出すことはしないでください。
- JavaScript を埋め込んだり、外部の JavaScript ファイルにリンクしたりできます。 外部の JavaScript ファイルを使用する場合は、相対 URL ではなく必ず絶対 URL を使用してください。
- JavaScript フレームワーク:
    - Azure AD B2C では、特定のバージョンの jQuery を使用します。 別のバージョンの jQuery を含めないでください。 同じページに複数のバージョンを使用すると、問題が発生します。
    - RequireJS の使用はサポートされていません。
    - ほとんどの JavaScript フレームワークは、Azure AD B2C ではサポートされていません。
- `window.SETTINGS`、`window.CONTENT` オブジェクト (現在の UI 言語など) を呼び出すことによって、Azure AD B2C の設定を読み取ることができます。 これらのオブジェクトの値は変更しないでください。
- Azure AD B2C のエラー メッセージをカスタマイズするには、ポリシーでローカライズを使用します。
- ポリシーを使用して実現できるのであれば、それが一般に推奨される方法です。

## <a name="javascript-samples"></a>JavaScript のサンプル

### <a name="show-or-hide-a-password"></a>パスワードの表示または非表示

サインアップに成功するように顧客を支援する一般的な方法は、パスワードの入力内容を表示できるようにすることです。 このオプションを使用して、パスワードを簡単に表示し、必要であれば訂正できるようにすることで、ユーザーのサインアップをサポートできます。 パスワードの入力のすべてのフィールドには、 **[パスワードの表示]** ラベルのあるチェックボックスがあります。  これにより、ユーザーはプレーン テキストでパスワードを表示できます。 セルフ アサート ページのサインアップまたはサインイン テンプレートには、次のコード スニペットを含めます。

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>利用規約の追加

次のコードを、 **[利用規約]** チェックボックスを組み込むページに含めます。 通常、このチェック ボックスは、ローカル アカウントのサインアップとソーシャル アカウント サインアップ ページで必要になります。

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

コードでは、`termsOfUseUrl` を利用規約の同意のリンクで置き換えます。 ディレクトリでは、**termsOfUse** と呼ばれる新しいユーザー属性を作成し、**termsOfUse** をユーザー属性として組み込みます。

## <a name="next-steps"></a>次のステップ

アプリケーションのユーザー インターフェイスをカスタマイズする方法の詳細については、「[Azure Active Directory B2C でカスタム ポリシーを使用してアプリケーションのユーザー インターフェイスをカスタマイズする](custom-policy-ui-customization.md)」を参照してください。
