# 转换为 LINE LIFF

现在是激动人心的部分--将你的 Unity WebGL 构建转化为可通过 LINE 访问的迷你 dApp！

## 第 1 步：创建您的 LIFF 应用程序<a id="create-liff-app"></a>

首先，让我们在 LINE 生态系统中设置您的应用程序：

1. LINE 开发人员控制台设置：

   - 访问 LINE 开发人员控制台。
   - 创建一个提供程序（如果已经有一个，请跳过）。

   ![](/img/minidapps/unity-minidapp/create-provider-lc.png)

   - 创建新的 LINE 登录通道。

   ![](/img/minidapps/unity-minidapp/line-login-lc.png)

   - 导航至 LIFF 选项
   - 点击 "添加 LIFF 应用程序

   ![](/img/minidapps/unity-minidapp/line-liff-add.png)

2. 配置 LIFF 设置：

```code
Size: Choose one of:
├── Full (entire screen)
├── Tall (75% of screen)
└── Compact (50% of screen)
Endpoint URL: https://example.com (temporary)
Permissions: Enable as needed
```

:::note
保存您的 LIFF ID - 下一步您将需要它！
:::

## 第 2 步：修改 WebGL 模板<a id="modify-webgl-template"></a>

index.html 文件可帮助我们检查 web3 是否可用、设置 LINE 集成 (LIFF)、加载 Unity 游戏并将所有内容连接在一起。

```html
<!DOCTYPE html>
<html lang="en-us">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Unity WebGL Player</title>
  <script src="https://static.line-scdn.net/liff/edge/2/sdk.js"></script>
  <script src="scripts/dapp_portal_sdk.js"></script>
  <style>
    body { margin: 0; padding: 0; }
    #unity-container { width: 100%; height: 100%; position: absolute; }
    #unity-canvas { width: 100%; height: 100%; background: #231F20; }
    #unity-loading-bar { display: none; }
    #unity-progress-bar-empty { width: 141px; height: 18px; margin-top: 10px; background: url('Build/minidapp.progress-bar-empty-dark.png') no-repeat center; }
    #unity-progress-bar-full { width: 0%; height: 18px; margin-top: 10px; background: url('Build/minidapp.progress-bar-full-dark.png') no-repeat center; }
  </style>
</head>
<body>
  <div id="unity-container">
    <canvas id="unity-canvas"></canvas>
    <div id="unity-loading-bar">
      <div id="unity-progress-bar-empty">
        <div id="unity-progress-bar-full"></div>
      </div>
    </div>
  </div>
  <script src="Build/minidapp.loader.js"></script>
  <script>
    var sdk = null;
    var connectedAddress = null;
    var myGameInstance = null;

    var Module = {
      onRuntimeInitialized: function() {
        console.log("Runtime initialized");
      },
      env: {
        MintToken: function(amount) {
          window.MintToken(amount);
        },
        GetBalance: function() {
          window.GetBalance();
        },
        ConnectWallet: function() {
          window.ConnectWallet();
        },
        DisconnectWallet: function() {
          window.DisconnectWallet();
        },
        GetConnectedAddress: function() {
          var address = window.GetConnectedAddress();
          var bufferSize = lengthBytesUTF8(address) + 1;
          var buffer = _malloc(bufferSize);
          stringToUTF8(address, buffer, bufferSize);
          return buffer;
        }
      }
    };

    async function initializeSDK() {
      try {
        await liff.init({
          liffId: "YOUR_LIFF_ID" // Replace with your LIFF ID
        });

        if (!liff.isLoggedIn()) {
          liff.login();
        }

        sdk = await DappPortalSDK.init({
          clientId: 'YOUR CLIENT ID', // Replace with your CLIENT ID
          chainId: '1001'
        });

        console.log("SDKs initialized");
        return true;
      } catch (error) {
        console.error("SDK init error:", error);
        return false;
      }
    }

    window.ConnectWallet = async function() {
      try {
        if (!sdk) {
          const initialized = await initializeSDK();
          if (!initialized) return null;
        }

        if (!liff.isLoggedIn()) {
          liff.login();
          return;
        }

        const provider = sdk.getWalletProvider();
        const accounts = await provider.request({ method: 'kaia_requestAccounts' });

        if (accounts && accounts.length > 0) {
          connectedAddress = accounts[0];
          myGameInstance.SendMessage('Web3Manager', 'OnWalletConnected', connectedAddress);
        }
      } catch (error) {
        myGameInstance.SendMessage('Web3Manager', 'OnWalletError', error.message);
      }
    }

    window.DisconnectWallet = async function() {
      try {
        if (liff.isLoggedIn()) {
          await liff.logout();
        }

        const provider = sdk.getWalletProvider();
         await provider.disconnect();
         
         // Reset connected address
         connectedAddress = null;
         myGameInstance.SendMessage('Web3Manager', 'OnWalletDisconnected');
         console.log("Wallet disconnected successfully");

      } catch (error) {
           console.error("Disconnect error:", error);
           myGameInstance.SendMessage('Web3Manager', 'OnWalletError', "Disconnect failed: " + error.message);
      }
    }

    window.GetConnectedAddress = function() {
      return connectedAddress || '';
    }

    window.MintToken = async function(amount) {
      try {
        const provider = sdk.getWalletProvider();

        const mintSignature = '0xa0712d68';
        const amountHex = amount.toString(16).padStart(64, '0');
        const data = mintSignature + amountHex;

        const tx = {
          from: connectedAddress,
          to: '0x099D7feC4f799d1749adA8815eB21375E13E0Ddb',
          value: '0x0',
          data: data,
          gas: '0x4C4B40'
        };

        const txHash = await provider.request({
          method: 'kaia_sendTransaction',
          params: [tx]
        });

        myGameInstance.SendMessage('Web3Manager', 'OnMintSuccess', txHash);
        GetBalance();
      } catch (error) {
        myGameInstance.SendMessage('Web3Manager', 'OnMintError', error.message);
      }
    }

    window.GetBalance = async function() {
      try {
        const provider = sdk.getWalletProvider();

        const balanceSignature = '0x70a08231';
        const addressParam = connectedAddress.substring(2).padStart(64, '0');
        const data = balanceSignature + addressParam;

        const result = await provider.request({
          method: 'kaia_call',
          params: [{
            from: connectedAddress,
            to: '0x099D7feC4f799d1749adA8815eB21375E13E0Ddb',
            data: data
          }, 'latest']
        });

        const balance = parseInt(result, 16);
        myGameInstance.SendMessage('Web3Manager', 'OnBalanceReceived', balance.toString());
      } catch (error) {
        myGameInstance.SendMessage('Web3Manager', 'OnBalanceError', error.message);
      }
    }

    createUnityInstance(document.querySelector("#unity-canvas"), {
      dataUrl: "Build/minidapp.data",
      frameworkUrl: "Build/minidapp.framework.js",
      codeUrl: "Build/minidapp.wasm",
      streamingAssetsUrl: "StreamingAssets",
      companyName: "DefaultCompany",
      productName: "minidapp",
      productVersion: "0.1",
    }).then((unityInstance) => {
      myGameInstance = unityInstance;
    });
  </script>
</body>
</html>
```

确保在上述代码片段中更改您的 LIFF-ID 。

## 第 3 步：部署 WebGL 构建<a id="step3-deploy-webgl-build"></a>

- 为 WebGL 构建您的 Unity 项目
- 将所有构建文件上传到网络服务器，例如 Netlify

您的部署文件夹结构应如下所示：

```bash
Minidapp/
├── Build/
│   ├── minidapp.data
│   ├── minidapp.framework.js
│   ├── minidapp.loader.js
│   └── minidapp.wasm
├── scripts/
│   └── dapp_portal_sdk.js
└── index.html
```

## 步骤 4：最终配置和测试<a id="step4-final-config-testing"></a>

1. 更新您的 LIFF 端点：
   - 返回 LINE 开发人员控制台
   - 查找您的 LIFF 应用程序
   - 点击 "编辑
   - 更新已部署网站的 URL。

现在，您的迷你 dApp 应该可以随时使用了。

## 总结<a id="summing-up"></a>

恭喜！ 您已成功使用 Unity 创建了第一个 LINE mini dApp！ 完成本指南后，您就实现了一个具有代币铸造功能的迷你 dApp。 构建 LINE mini dApp 超越了传统的应用程序开发，而是要在用户已经信任并每天使用的生态系统中创建无缝的 Web3 体验。

通过 Kaia 的集成，您可以将区块链功能直接带到用户的指尖，消除 Web3 采用的常见障碍。 LINE 的广泛影响力与 Web3 的功能相结合，为我们创造了一个独特的创新机会，这在以前是不可能实现的。

LINE mini dApps 的强大之处在于其多功能性和易用性。 无论您是探索新区块链实施方法的开发人员，还是希望提高客户参与度的企业，抑或是寻求创造新颖数字体验的创新者，该平台都能为您提供实现愿景所需的所有工具。

有关开发 LINE mini dApp 的更多详细信息，请浏览这些综合资源：

- [Kaia文档](https://docs.kaia.io/)
- [行开发人员文档](https://developers.line.biz/en/docs/line-mini-app/)
- [统一文档](https://docs.unity.com/)

## 附录<a id="appendix"></a>

### 附录 A<a id="appendix-a"></a>

[KaiaPlugin.jslib源代码](https://gist.github.com/ayo-klaytn/2aad97e1e263b00f5403177a7ad1fff1#file-kaiaplugin-jslib)

### 附录 B<a id="appendix-b"></a>

[Web3Manager.cs源代码](https://gist.github.com/ayo-klaytn/2aad97e1e263b00f5403177a7ad1fff1#file-web3manager-cs)
