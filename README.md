# signPage —— vane 的云端签字页

页面地址：**https://mxx-yyl.github.io/signPage/**

这一页就是 vane 的签字页 `software/src/vane/signpage/index.html`（2.10.0），逐字节同一份：

| 文件 | sha256 |
|---|---|
| `index.html` | `8b87eea50d9e9693f7c534039ad549d7f240ef68868cd622de24a6fda2aa76d2` |

vane 那边：登记 `95d5e093c522`（2026-11-05，flyoc 用 iCloud passkey 签的许可）把这一份字节封进箱 `95d5e093c522`。

## 规矩

用户 2026-11-05：「密碼只放權，簽字永遠用passkey，passkey要麼雲端的icloud要麼本地的，密碼就是唯一的最高權限，除了放權它一般不簽名。」

- **密码只放权**：人（vane 的唯一密码，PERSONS.json 里那把密码钥）只用来担保新设的 passkey —— 下面的「设 passkey」。
- **签字一律用 passkey**：云端用这一页上设的 passkey（iCloud 钥匙串），电脑上用本机签字页的 passkey。
- 2.10.0 的页面上「或者：输密码签」还在（担保时要用）；签别的东西请按「用 passkey 签」。

## 设 passkey（2.10.0 起）

1. 会话 `vane sign setup-link` 给一个链接（不含秘密：人的密码钥的公钥与盐、要设的名字、登记记录的格式），可以长期放着反复用。
2. 打开链接，输一次 vane 的密码（对上 PERSONS.json 那一把才往下走），逐个名字按一下，各新设一把 passkey。
3. 页面用密码一次担保这几把，给一段 JSON：贴回会话 `vane sign setup '<JSON>'`，验过这几把当场就是签字人。换新设备，再开同一个链接设一次。

passkey 绑在建立它的网站上：签字在 mxx-yyl.github.io 上签，passkey 也必须在这里设。

## 签字

1. 云会话要签字时，`vane sign link --cloud` 给一个链接：`https://mxx-yyl.github.io/signPage/#v1.…`。
   要签的原文全在 `#` 后面，浏览器不把它发给任何服务器（GitHub 也看不到）。
2. 打开链接、读原文，按「用 passkey 签」（指纹 / 面容 / PIN）。
3. 页面给出一段 JSON 和「复制，贴回会话」：贴回发起的那个云会话，会话用 `vane sign accept` 验过才落。

电脑（本机 / VM）发起的签字不走这里，照旧走电脑上的签字页（http://localhost:8765/）。电脑与云各签各的。

## 谁能改这一页

签的字节由这一页从链接里摆出来的原文现算，签字密码也是在这一页里输的 —— 改了这一页的人，就能让人「看的是 A、签的是 B」，还能偷走密码。
对这个仓有写权限的都改得了，包括被授权推送的云会话。所以：

- **收回云会话对这个仓的写权限**（GitHub 设置 → Applications → Installed GitHub Apps → Claude → Configure，去掉 signPage），或者保护 main（只让 PR 进、你自己合）；
- 每一次改动都是这里的一个提交，逐行看得到；
- 核对：`curl -s https://mxx-yyl.github.io/signPage/ | sha256sum` 应当等于上面的 sha256，也等于 vane 登记时封进箱的那一份。

## 设置（一次）

Settings → Pages → Build and deployment → Source：**Deploy from a branch**；Branch：**main**，**/ (root)** → Save。约一分钟后上面的地址就能打开。
