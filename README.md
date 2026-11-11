# signPage —— vane 的云端签字页

页面地址：**https://mxx-yyl.github.io/signPage/**

> **现状（2026-11-11 核对）：main 上的 `index.html` 不是 vane 箱里封的那一份。**
> 提交 `5aa5d1a`（2026-11-10 16:01 +0800，网页上传）把它换成了下表第一行的那一份：在 2.12.0 上多了一段「云端授权」流程（下面一节），页内版本号仍写 2.12.0。
> vane 仓 `software/src/vane/signpage/index.html` 与箱里封的仍是 2.12.0 原来那一份（现行箱 `6b37f5a55845` 里也是它）。
> 所以「谁能改这一页」一节里的核对此刻对不上箱 —— 要对上，得把这一版收回 vane 仓、抬版本号、出签字页发布单（命名空间 `vane-signpage`）、登记（vane memo D-334 第五节；要用户批准）。

| 文件 | 在哪 | sha256 |
|---|---|---|
| `index.html`（main 现行：云端签字页供的就是它） | 本仓 `5aa5d1a` | `6ef47bf2b5d132190a52ce921af5bdac5fdae35234edfba1304dd6a979373168` |
| `index.html`（2.12.0：vane 登记过、箱里封的） | 本仓 `9c3d9ab`；vane `software/src/vane/signpage/index.html` | `3cd4856d7ab23de10bb419d5b6c498974374d19c92547d28ec6a6994660d3116` |

vane 那边：登记 `6b00f01cb5a1`（2026-11-06，susuqni 用 iCloud passkey 在这一页上签的许可 6b00f01cb5a1，memo D-311）把 2.12.0 那一份字节封进箱 `6b00f01cb5a1`。

## 云端授权（license）—— `5aa5d1a` 加的那一段

用户 2026-11-11：「特别注意云端的license 和 签字都是默认走mxx-yyl.github.io/signpage」。

云端的机器（云会话、远程服务器）上没有使用者能输密码的终端，也打不开它的部署页，所以生产版的授权在云端走这一页：

1. 云端那台机器 `vane license request` 出请求码；发行方 `vendor_tool.py issue` 出两份候选（每年检验 / 永久），`vendor_tool.py link` 出链接（生产版 susu-7.0 起默认就是这种）。
2. 链接 `#v1.` 后面的载荷带 `license.items`（每一项：`ns` = `vane-release`、`t` = 候选的正文、`sha`、`file`、`label`、`def`）与 `pw`（人的密码钥公开的那一半）。
   带 `license` 才进「云端授权 · 激活 vane license」：核对机器、频道与授权原文，单选「每年检验一次 / 永久」，输 vane 密码批准 —— 不要 passkey、不要求浏览器支持 WebAuthn。
3. 密码在这一页里变成签名钥（scrypt → Ed25519，与「输密码签」同一段代码），只签选中的那一份；页面给出一段 JSON，贴回发行方 `vendor_tool.py finalize --assertion '<JSON>'`，
   验过才签出授权文件，云端那台机器 `vane license install`。

核过的（2026-11-11，vane 生产版 susu-7.0 的发版自测）：这一份字节在无头 Chromium 里按原址打开，license 链接 → 选项 → 输密码 → 断言 → `finalize` → `vane license install`「批准成立」；
整个过程页面没有向任何地址发请求（除了加载它自己）。`5aa5d1a` 对 2.12.0 的改动是 `licenseFlow` 一个函数与入口处的分流（72 行加、4 行挪），签名走原来的 `signWithPassword`。
没核过的：没有连真的 github.io（那台构建机连不到），用的是测试批准人的密码、不是真的 vane 密码。
2.12.0 那一份不认 `license`：同一条链接打开是首页。顶层 `items` 的老格式（`vendor_tool.py link --passkey`）在两份页面上都是普通的等签件，只能用 passkey 签。

## 2.12.0：界面全面重画、主动替你勾好建议项

用户 2026-11-06：「對vane的簽字頁面改版，ui 全面重新設計，另外主動智能勾選建議項目」「如果上一次做過了默認就不選」。

- **批准件每件一个勾，默认勾上**：去掉勾的这次不签，留在等签队列里下次签；许可「放宽几处」、担保「你为这个人负责」这类提示一眼看得到。
- **单选按线归拢，每一问都预选好建议项、旁边写理由**（✦）：会话标的默认（UVA「最后一次」、交付方式「合并 trace」）；指控方由会话按「上一次做过了就默认不选」给 —— 这条线自己、或它去掉「-后缀」的那条线在问之前已经被指控方审过，预选「不要」（写明哪条线、哪天审的），否则预选「要」。改了一条线，可以一键带上同一批的其余线。建议只是预选：签的永远是你按下去那一刻页上选中的那几份。
- **签完自动复制**那段 JSON（浏览器不让就按「复制，贴回会话」）；passkey 出错说人话；用不了 passkey 的浏览器（例如某个 app 里面的）给「复制这一页的链接」。
- 签法、签的字节、给出的 JSON 一个字节没变。

## 规矩

用户 2026-11-05：「密碼只放權，簽字永遠用passkey，passkey要麼雲端的icloud要麼本地的，密碼就是唯一的最高權限，除了放權它一般不簽名。」

- **密码只放权**：人（vane 的唯一密码，PERSONS.json 里那把密码钥）只用来担保新设的 passkey —— 下面的「设 passkey」。
- **签字一律用 passkey**：云端用这一页上设的 passkey（iCloud 钥匙串），电脑上用本机签字页的 passkey。
- 2.11.0 起，「或者：输密码签（放权）」只在这一批全是担保时出现；许可、线的许可、发布……这一页只给「用 passkey 签」。

## 设 passkey（2.10.0 起）

1. 会话 `vane sign setup-link` 给一个链接（不含秘密：人的密码钥的公钥与盐、要设的名字、登记记录的格式），可以长期放着反复用。
2. 打开链接，输一次 vane 的密码（对上 PERSONS.json 那一把才往下走），逐个名字按一下，各新设一把 passkey。
3. 页面用密码一次担保这几把，给一段 JSON：贴回会话 `vane sign setup '<JSON>'`，验过这几把当场就是签字人。换新设备，再开同一个链接设一次。

passkey 绑在建立它的网站上：签字在 mxx-yyl.github.io 上签，passkey 也必须在这里设。

## 签字

1. 云会话要签字时，`vane sign link --cloud` 给一个链接：`https://mxx-yyl.github.io/signPage/#v1.…`。
   要签的原文全在 `#` 后面，浏览器不把它发给任何服务器（GitHub 也看不到）。
2. 打开链接：建议的那几项已经勾好、选好，原文都摆着；看一眼，按「用 passkey 签」（指纹 / 面容 / PIN）。
3. 页面给出那段 JSON 并先替你复制好：贴回发起的那个云会话，会话用 `vane sign accept` 验过才落。

电脑（本机 / VM）发起的签字不走这里，照旧走电脑上的签字页（http://localhost:8765/）。电脑与云各签各的。

## 谁能改这一页

签的字节由这一页从链接里摆出来的原文现算，签字密码也是在这一页里输的 —— 改了这一页的人，就能让人「看的是 A、签的是 B」，还能偷走密码。
对这个仓有写权限的都改得了，包括被授权推送的云会话。所以：

- **收回云会话对这个仓的写权限**（GitHub 设置 → Applications → Installed GitHub Apps → Claude → Configure，去掉 signPage），或者保护 main（只让 PR 进、你自己合）；
- 每一次改动都是这里的一个提交，逐行看得到；
- 核对：`curl -s https://mxx-yyl.github.io/signPage/ | sha256sum` 应当等于上面的 sha256，也等于 vane 登记时封进箱的那一份。
  **此刻（`5aa5d1a` 之后）它应当等于最上面表里的第一行 `6ef47bf2…`，与箱里的 `3cd4856d…` 不同** —— 这一版还没有回到 vane 登记（见最上面的现状）。

## 设置（一次）

Settings → Pages → Build and deployment → Source：**Deploy from a branch**；Branch：**main**，**/ (root)** → Save。约一分钟后上面的地址就能打开。
