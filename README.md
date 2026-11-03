# signPage —— vane 的云端签字页

页面地址：**https://mxx-yyl.github.io/signPage/**

这一页就是 vane 的签字页 `software/src/vane/signpage/index.html`（2.9.0），逐字节同一份：

| 文件 | sha256 |
|---|---|
| `index.html` | `678e4a860b811131bf8a44735fd9da3df3c7cd443b4a7bb20cedcd76797fe4a4` |

vane 那边：登记许可 `cad36dd2979d`（memo D-252，等签）把这一份字节封进箱；登记之后这里补上箱号。

## 怎么用

1. 云会话（claude.ai 上的 Claude Code）要签字时，`vane sign link --cloud` 给一个链接：`https://mxx-yyl.github.io/signPage/#v1.…`。
   要签的原文全在 `#` 后面，浏览器不把它发给任何服务器（GitHub 也看不到）。
2. 打开链接、读原文，用在 mxx-yyl.github.io 登记过的 passkey 签（Face ID / Touch ID）。
3. 页面给出一段 JSON 和「复制，贴回会话」：贴回发起的那个云会话，会话用 `vane sign accept` 验过才落。

电脑（本机 / VM）发起的签字不走这里，照旧走电脑上的签字页（http://localhost:8765/）。电脑与云各签各的。

## 谁能改这一页

签的字节由这一页从链接里摆出来的原文现算 —— 改了这一页的人，就能让人「看的是 A、签的是 B」。
对这个仓有写权限的都改得了，包括被授权推送的云会话。建议：

- 放好之后收回云会话对这个仓的写权限，或者保护 main（只让 PR 进、你自己合）；
- 每一次改动都是这里的一个提交，逐行看得到；
- 核对：`curl -s https://mxx-yyl.github.io/signPage/ | sha256sum` 应当等于上面的 sha256，也等于 vane 登记时封进箱的那一份。

## 设置（一次）

Settings → Pages → Build and deployment → Source：**Deploy from a branch**；Branch：**main**，**/ (root)** → Save。约一分钟后上面的地址就能打开。
