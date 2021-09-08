---
title: "Next.jsでReact Portalを使用したモーダル画面"
emoji: "🐈"
type: "tech"
topics: ["Nextjs"]
published: false
---

# React Portal とは

親コンポーネントの DOM 階層外にある DOM ノードに対して子コンポーネントをレンダーするための仕組みです。
参考になる情報はたくさん見つかるので、詳細は割愛します。

[React 公式ドキュメント](https://ja.reactjs.org/docs/portals.html)

https://reffect.co.jp/react/react-portals
https://zenn.dev/maktub_bros/articles/a700d189c60ca8
https://tyotto-good.com/blog/react-portal

# Next.js で React Portal を使用する

Next.js で React Portal を使用した情報があまり見つからないので、Next.js リポジトリにあるサンプルソースを元に、作ってみました。

[Next.js 公式ドキュメント](https://nextjs.org/docs/advanced-features/custom-document)

[サンプルソース](https://github.com/vercel/next.js/tree/canary/examples/with-portals)

[今回作ったソースファイル](https://github.com/takasaki376/nextjs-modal)

# ファイル構成

```
- src
  - components
    + ModalPortal.tsx           # React Portalを使用したモーダル画面
    + MessagePortal.tsx         # ModalPortalを使用してメッセージボックス表示
    + ModalBasic.tsx            # React Portalを使用しないモーダル画面（DOM比較用）
  - pages
    + _app.tsx                  # create-next-app から変更なし
    + _document.tsx             # 新規ファイル作成
    + index.tsx                 # create-next-app からbutton追加
  - styles
    + globals.css               # create-next-app から変更なし
    + Home.module.css           # create-next-app からbutton追加
    + MessagePortal.module.css  # 新規ファイル作成
```

# \_document.tsx 作成

pages 配下に\_document.tsx を作成し、`<div id="modal" />`を追加します。
React では、index.html ファイルに記述しますが、Next.js では\_document.tsx に追加します。

```tsx:src/pages/_document.tsx
import Document, { Html, Head, Main, NextScript } from "next/document";

export default class MyDocument extends Document {
  render() {
    return (
      <Html>
        <Head />
        <body>
          <Main />
          { /* 追加するDOMを指定する */ }
          <div id="modal" />
          <NextScript />
        </body>
      </Html>
    );
  }
}
```

# モーダル画面の表示を切り替えるコンポーネント作成

Portals は ReactDom の createPortal メソッドで作成することができ、第一引数には表示させたい内容(child)、第二引数には先ほど \_document.tsx で追加した div 要素(container)を document.getElementById メソッドで取得しています。

```tsx:src/components/ModalPortal.tsx
import type { ReactNode, VFC } from "react";
import { useRef, useEffect } from "react";
import { createPortal } from "react-dom";

type Props = {
  open: boolean;
  children: ReactNode;
};

export const ModalPortal: VFC<Props> = (props) => {
  const ref = useRef<Element | null>(null);

  useEffect(() => {
    ref.current = document.querySelector("#modal");
  }, []);

  if (!props.open) {
    return null;
  }
  return ref.current ? createPortal(props.children, ref.current) : null;
};
```

# モーダル画面レイアウト作成

```tsx:src/componenst/MessagePortal.tsx
import { DOMAttributes, VFC } from "react";
import { ModalPortal } from "./ModalPortal";
import styles from "../styles/MessagePortal.module.css";

type Props = {
  open: boolean;
  onClick: DOMAttributes<HTMLButtonElement>["onClick"];
};

export const MessagePortal: VFC<Props> = (props) => {
  return (
    <ModalPortal open={props.open}>
      <div className={styles.root}>
        <div className={styles.main}>
          <h2>title</h2>
          <main className={styles.message}>
            <span>message</span>
          </main>
          <footer className={styles.button}>
            <button onClick={props.onClick}>閉じる</button>
          </footer>
        </div>
        <div className={styles.back} />
      </div>
    </ModalPortal>
  );
};
```

# 検討ポイント

キーボードの操作をした際に、モーダル画面側ではなく、親階層の DOM になるため、上記の内容のみではまだ使えそうにないです。
追加の情報があれば更新していきます。

# eslint のエラー

Next.js v11.1.2,eslint-config-next v11.1.2 では下記のエラーが発生するため.eslintrc.json にてワーニングとなるように条件追加する。
ISSUE は[こちら](https://github.com/vercel/next.js/issues/13712)

```
./src/pages/_document.tsx
1:1  Error: next/document should not be imported outside of pages/_document.js. See https://nextjs.org/docs/messages/no-document-import-in-page.  @next/next/no-document-import-in-page
```

```json:.eslintrc.json
"rules": {
    "@next/next/no-document-import-in-page": ["warn"]
  }
```
