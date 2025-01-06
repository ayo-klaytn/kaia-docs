---
sidebar_label: Particle Network
---

# 将粒子网络整合到 dApp 中

![](/img/banners/kaia-particle.png)

## 导言

[粒子网络](https://particle.network) 提供钱包抽象服务，以简化用户入门。

[粒子连接 SDK](https://developers.particle.network/api-reference/connect/desktop/web) 支持与 EVM 兼容的链，包括 Kaia 及其测试网。 它允许使用[社交和 Web3 登录选项](https://developers.particle.network/api-reference/connect/desktop/web#wallet-connectors)进行 2 键登录，所有操作都在一个模态中完成。

通过 Particle Network，Kaia 开发人员可以为 Kaia 主网和测试网嵌入社交登录，让用户只需使用他们的谷歌、电子邮件、X 等信息就能在您的应用程序中生成和使用钱包。

本页提供在基于 Kaia 的应用程序中实施 Particle Connect 的概述和教程，以帮助您开始集成过程。

## 先决条件

- 使用 TypeScript 和 Tailwind CSS 设置的 [Next.js 项目](https://nextjs.org/docs/getting-started/installation)
  - 您可以运行： `npx create-next-app@latest` 来创建它。
- 来自 [Particle Dashboard](https://dashboard.particle.network)的**项目 ID**、**客户密钥**和**应用程序 ID**。

## 安装

要在您的 dApp 中利用 Particle Network，特别是 Particle Connect，您首先需要安装所需的库。 Particle Connect SDK 通过一个界面简化了钱包创建、用户登录和区块链交互过程。 它支持社交登录和 Web3 登录，便于访问。

要安装 SDK 以及 Viem（连接后台）和 ethers（演示 EIP-1193 提供商），请运行

```shell
yarn add @particle-network/connectkit viem@^2 ethers
```

## 初始化粒子连接

首先，我们将设置 Particle Connect，这是 Particle 的旗舰认证 SDK。 在项目根目录下创建名为 `ConnectKit.tsx` 的新文件。 该文件将容纳 "ParticleConnectKit "组件，它是已配置的 "ConnectKitProvider "实例的包装器，是配置 Particle Connect 的主要接口（我们稍后将以编程方式介绍）。

接下来，前往 [Particle dashboard](https://dashboard.particle.network)，创建一个新的网络应用程序项目，并获取以下必要的 API 密钥：

- **`projectId`** - 项目的唯一标识符。
- **`clientKey`** - 客户端特有的密钥。
- **`appId`** - 应用程序的 ID。

将这些 API 密钥存储在`.env`文件中，如下所示：

```plaintext
next_public_project_id='project_id'
next_public_client_key='client_key'
next_public_app_id='app_id'
```

现在，将以下代码添加到您的 `ConnectKit.tsx` 文件中：

```js
"use client";

import React from "react";
import { ConnectKitProvider, createConfig } from "@particle-network/connectkit";
import { authWalletConnectors } from "@particle-network/connectkit/auth";
import { defineChain } from "@particle-network/connectkit/chains";
import { wallet, EntryPosition } from "@particle-network/connectkit/wallet";

const kaiaMainnet = defineChain({
  id: 8217,
  name: "Kaia",
  nativeCurrency: {
    decimals: 18,
    name: "KAIA",
    symbol: "KAIA",
  },
  rpcUrls: {
    default: {
      http: ["https://public-en.node.kaia.io"],
    },
  },
  blockExplorers: {
    default: { name: "Explorer", url: "https://kaiascope.com/" },
  },
  testnet: false,
});

const kaiaTestnet = defineChain({
  id: 1001,
  name: "Kaia Testnet",
  nativeCurrency: {
    decimals: 18,
    name: "KAIA",
    symbol: "KAIA",
  },
  rpcUrls: {
    default: {
      http: ["https://public-en-kairos.node.kaia.io"],
    },
  },
  blockExplorers: {
    default: { name: "Explorer", url: "https://kairos.kaiascope.com/" },
  },
  testnet: true,
});

const config = createConfig({
  projectId: process.env.NEXT_PUBLIC_PROJECT_ID!,
  clientKey: process.env.NEXT_PUBLIC_CLIENT_KEY!,
  appId: process.env.NEXT_PUBLIC_APP_ID!,

  walletConnectors: [authWalletConnectors({})],

  plugins: [
    wallet({
      entryPosition: EntryPosition.BR, // Positions the modal button at the bottom right on login
      visible: true, // Determines if the wallet modal is displayed
    }),
  ],
  chains: [kaiaMainnet, kaiaTestnet],
});

export const ParticleConnectkit = ({ children }: React.PropsWithChildren) => {
  return <ConnectKitProvider config={config}>{children}</ConnectKitProvider>;
};
```

该组件的几乎所有属性都可以配置，从支持的不同登录类型到模态的视觉外观；要探索这些不同的选项，请访问 [Particle 文档](https://developers.particle.network/api-reference/connect/desktop/web#configuration)。

## 将 Particle Connect 集成到您的应用程序中

配置完成后，用 "ParticleConnectKit "组件封装您的应用程序，以启用对 Particle Connect SDK 的全局访问。 要做到这一点，请对 `src` 目录中的 `layout.tsx` 文件作如下修改：

```typescript
import { ParticleConnectkit } from '@/connectkit';
import type { Metadata } from 'next';
import { Inter } from 'next/font/google';
import './globals.css';

const inter = Inter({ subsets: ['latin'] });

export const metadata: Metadata = {
  title: 'Particle Connectkit App',
  description: 'Generated by create next app',
};

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body className={inter.className}>
        <ParticleConnectkit>{children}</ParticleConnectkit>
      </body>
    </html>
  );
}
```

### 连接钱包

设置好 "layout.tsx "文件后，就可以通过中央**连接钱包**按钮连接用户了。 您可以从 `@particle-network/connectkit` 中导入 `ConnectButton` 来实现这一功能。 一旦用户登录，"连接按钮 "就会变成一个嵌入式部件。

```js
import { ConnectButton, useAccount } from '@particle-network/connectkit';

export const App = () => {
    const { address, isConnected, chainId } = useAccount();

    // Standard ConnectButton utilization
    return (
        <div>
            <ConnectButton />
            {isConnected && (
                <>
                    <h2>Address: {address}</h2>
                    <h2>Chain ID: {chainId}</h2>
                </>
            )}
        </div>
    );
};
```

### 获取账户和余额

通过 `ConnectButton` 组件成功连接钱包（或社交登录）后，就可以检索用户的相关 Kaia 地址。 此外，您还可以通过 "publicClient"（利用 Particle Connect 已设置的 Viem 提供商）检索其当前余额（以 KAIA 为单位）。

```js
"use client";

import { useState, useEffect } from "react";
import {
  ConnectButton,
  useAccount,
  usePublicClient,
} from "@particle-network/connectkit";
import { formatEther } from "viem";

export default function Home() {
  // Account-related states
  const { isConnected, address, chain } = useAccount();
  const publicClient = usePublicClient();

  // State variable for balance
  const [balance, setBalance] = useState<string>("");

  // Fetch and display user balance when connected
  useEffect(() => {
    const fetchBalance = async () => {
      if (address) {
        try {
          const balanceResponse = await publicClient.getBalance({ address });
          const balanceInEther = formatEther(balanceResponse);
          setBalance(balanceInEther);
        } catch (error) {
          console.error("Error fetching balance:", error);
        }
      }
    };

    if (isConnected) {
      fetchBalance();
    }
  }, [isConnected, address, publicClient]);

  return (
    <div className="min-h-screen flex flex-col items-center justify-center p-8 bg-black text-white">
      <ConnectButton label="Connect Wallet" />
      {isConnected && (
        <div className="w-full max-w-md mt-6">
          <h2 className="text-xl font-bold text-white mb-4">Account Details</h2>
          <p className="text-lg text-white">
            Address: {address || "Loading..."}
          </p>
          <p className="text-lg text-white">
            Balance: {balance || "Loading..."} {chain?.nativeCurrency.symbol}
          </p>
        </div>
      )}
    </div>
  );
}
```

### 断开钱包连接

用户登录后，可以通过源自 `useDisconnect` 的 `disconnect` 以编程方式强制注销。 这将断开当前活动会话与 dApp 的连接，使用户返回初始状态。

```js
import { useDisconnect } from "@particle-network/connectkit";

const { disconnect } = useDisconnect();

// Inside your component's JSX
<button
  className="mt-4 w-full bg-red-600 hover:bg-red-700 text-white font-bold py-2 px-4 rounded"
  onClick={disconnect}
>
  Disconnect
</button>

```

### 获取用户信息

当用户通过社交账户连接时，可以使用 `useParticleAuth()` 钩子访问 `userinfo`，其中包括用户的连接方式、账户创建日期、姓名、电子邮件和其他[来自 Particle Auth 的相关信息](https://developers.particle.network/api-reference/connect/desktop/web#fetch-user-information-with-particle-auth)。

```js
import { useAccount, useParticleAuth, useWallets } from '@particle-network/connectkit';
import { useState, useEffect } from 'react';

export const App = () => {
    const { getUserInfo } = useParticleAuth();
    const { isConnected } = useAccount();

    // Retrieve the primary wallet from the Particle Wallets
    const [primaryWallet] = useWallets();

    // Store userInfo in a useState to use it in your app
    const [userInfo, setUserInfo] = useState<any>(null);

    useEffect(() => {
        const fetchUserInfo = async () => {
            // Use walletConnectorType as a condition to avoid account not initialized errors
            if (primaryWallet?.connector?.walletConnectorType === 'particleAuth') {
                const userInfo = await getUserInfo();
                setUserInfo(userInfo);
            }
        };

        fetchUserInfo();
    }, [isConnected, getUserInfo]);

    return <h2 className="text-style">Name: {userInfo.name || 'N/A'}</h2>;
};
```

### 发送本地事务

Particle Connect 允许您利用现有的 EIP-1193 提供商，在本例中，我们创建了一个带有 `ethers` 的提供商实例来发送转账交易。

```js
import { useWallets } from "@particle-network/connectkit";
import { ethers, type Eip1193Provider } from "ethers";

const [primaryWallet] = useWallets();

const executeTransaction = async () => {
    // Get the provider from the primary wallet's connector
    const EOAprovider = await primaryWallet.connector.getProvider();

    // Initialize a custom provider using ethers.js with the obtained EIP-1193 provider
    const customProvider = new ethers.BrowserProvider(EOAprovider as Eip1193Provider, "any");

    // Get the signer (an abstraction of the account that can sign transactions)
    const signer = await customProvider.getSigner();

    // Send a transaction with specified recipient address, amount (0.01 ETH), and empty data
    await signer.sendTransaction({
      to: recipientAddress,             
      value: parseEther("0.01"),        
      data: "0x",                       
    });
};


```

## 下一步工作

您可以在 [Particle Connect 文档](https://developers.particle.network/api-reference/connect/desktop/web#key-react-hooks-for-particle-connect) 中找到可用钩子的完整列表。

有关 Particle Network（Particle Connect、Particle Auth 和其他 SDK）的其他指南，请参阅 [Particle Network 文档](https://developers.particle.network) 和 [Particle Network GitHub 账户](https://github.com/Particle-Network)。 此外，您还可以访问 [Particle Network 博客](https://blog.particle.network) 了解有关 Particle Network 服务、即将发布的版本和技术栈的更多信息。