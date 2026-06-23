# WSL 3 とは？

Windows 上で Linux 環境を動かせる **WSL（Windows Subsystem for Linux）** の次世代版、**WSL 3** について調べたのでまとめてみた。

> 📝 メモ：WSL 3 は **Build 2026 でプレビュー発表された新しいもの**。
> 情報が出始めたばかりで、仕様や対応範囲は今後変わる可能性があるので、その前提で読んでほしい。

## そもそも WSL とは？

WSL は、**Windows 上で Linux のコマンドやツールをそのまま動かせる仕組み**。
仮想マシンを別途立てたり、デュアルブートしたりせずに、Windows と Linux を併用できる。

これまでのバージョンの違いはざっくり以下のとおり。

| バージョン | 仕組み | 特徴 |
| ---------- | ------ | ---- |
| WSL 1 | Linux のシステムコールを Windows 用に変換 | 起動が速いが互換性に限界 |
| WSL 2 | 軽量な Hyper-V VM 上で**本物の Linux カーネル**を実行 | 互換性・性能が向上、現在の主流 |
| WSL 3 | 仮想化のボトルネックを減らす**新アーキテクチャ** | GPU / NPU へほぼネイティブにアクセス |

> ちなみに、この TIL を書いている環境も `microsoft-standard-WSL2` で、まだ WSL 2 で動いている。

## WSL 3 のポイント

### 1. パラ仮想化（paravirtualization）アーキテクチャ

WSL 2 は「軽量な Hyper-V VM の中で Linux カーネルを動かす」構成だった。
WSL 3 では **パラ仮想化を採用した新しい実行アーキテクチャ**になり、仮想化による**ハードウェアアクセスのボトルネックを取り除く**ことを狙っている。

### 2. GPU / NPU へのほぼネイティブなアクセス

最大の目玉は、**GPU や NPU に近いネイティブ性能でアクセスできる**こと。

- WSL 2 では仮想化のオーバーヘッドがあり、ハードウェアアクセスに制約があった
- WSL 3 ではそれが大幅に改善される
- **PyTorch・CUDA・Ollama** などの AI 系エコシステムにとって特に恩恵が大きい

> ローカルで [オープンウェイトの LLM](../AI/open-weight-llm.md) を動かすような用途とも相性が良さそう。

### 3. 対応ハードウェア（プレビュー時点）

プレビューは、まず以下のような環境から提供されるとのこと。

- Copilot+ PC
- Qualcomm Snapdragon X Elite
- Intel Meteor Lake / Lunar Lake

その後 Windows Update 経由で広く配布され、**AMD のサポートは後から**追加される予定。

## あわせて発表された「WSL コンテナ」

WSL 3 とは別に、**WSL コンテナ（WSL containers）** も発表された。混同しやすいので分けて整理。

- WSL に統合された**ネイティブなコンテナ機能**
- `wslc.exe` というツールで、コマンドラインや API から Linux コンテナを作成・実行・操作できる
- 追加設定がほぼ不要な「すぐ使える（out-of-box）」体験を目指している

> 「WSL 3」と「WSL コンテナ」は**別の発表**。報道でも混在しがちなので注意。

## その他の WSL 改善（2026 年）

WSL 3 と並行して、WSL 全体の地味だが嬉しい改善も進められている。

- **ファイルアクセスの高速化**：Windows ⇔ Linux 間（`/mnt/c` など）の読み書き速度を改善
- **ネットワークの強化**：信頼性・スループットの向上
- **セットアップの簡略化**：インストール・設定をよりスムーズに
- **エンタープライズ向け管理**：ポリシー制御やセキュリティ境界の強化

## まとめ

- WSL 3 は **Build 2026 でプレビュー発表された WSL の次世代版**
- **パラ仮想化アーキテクチャ**で、GPU / NPU へほぼネイティブにアクセスできるのが目玉
- **AI 系（PyTorch / CUDA / Ollama）の用途で恩恵が大きい**
- 似た名前の「WSL コンテナ（`wslc.exe`）」は別の機能なので混同しない

Windows で AI 開発をする人にとっては、かなりインパクトの大きいアップデートになりそうだと感じた。

## 参考

- [GitHub - microsoft/WSL](https://github.com/microsoft/WSL)
- [Microsoft Unveils WSL 3 and WSL Containers for Windows（it-connect）](https://www.it-connect.tech/microsoft-unveils-wsl-3-and-wsl-containers-for-windows/)
- [Microsoft to upgrade WSL with faster file access, better networking and easier setup（windowslatest）](https://www.windowslatest.com/2026/03/31/microsoft-to-upgrade-windows-subsystem-for-linux-wsl-with-faster-file-access-better-networking-and-easier-setup/)
