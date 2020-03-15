# ネットワーク備忘録

## はじめに

ネットワーク学習の備忘録です。
初心者の学習用。

## アジェンダ(予定)

- ネットワークの基礎的知識
- nginx
- Docker の基礎
- Kubernetes の基礎

## ネットワークの基礎的知識

### 1

- ネットワークの利点は**リソースの共有**にある。
- 人が運ぶスニーカーネットから、LAN(ローカルエリア) へ、そして WAN(ワイルドエリア) へとネットワークは進歩した。

- LAN と WAN の違い

|          | LAN                      | WAN               |
| -------- | ------------------------ | ----------------- |
| 範囲     | 狭い                     | 広い              |
| スピード | 早い                     | 遅い              |
| 品質     | 高い                     | 低い              |
| 接続     | 常時                     | 常時・間欠        |
| ケーブル | 自分で設置               | 電話会社のを借用  |
| 使用料金 | タダ                     | 電話代が必要      |
| 対象     | パソコンとパソコンを接続 | LAN と LAN を接続 |

上記の違いのため、使用する技術も異なる。

### 2

- プロトコル
  どのように通信するか、を決めたルールのこと。<br/>
  プロトコルは何種類かある。<br/>
  それぞれ時と場合と必要性などによって使うプロトコルが違う。一番有名なのは TCP/IP(プロトコルの集合) でインターネットで使用されているプロトコルではデファクトスタンダード。

- 帯域幅
  データ転送速度のこと。
  単位時間あたりに送ることが可能なデータ量、bps。

  - ブロードバンド / 広い幅、ADSL や CATV といった高速回線を指すらしい。
  - ナローバンド / 狭い幅、通常の電話回線 etc 低速回線。

- ネットワークモデル
  通信において、プロトコル以外にも使用機器、データの表現といったものも規格として合わせなければリソースの共有はできないって話し。昔はこの規格がバラバラだったらしい。そのため、統一した規格を ISO が考えた。ベンダー同士は相互通信可能にするためにこのモデルに従って作る。つまり、各ベンダーが作る製品は同じネットワークモデルで作られるので、最低限の部分は同じにして作られている。

### 3

- OSI 参照モデル
  現在の主流。<br/>
  7 段階の実行レイヤーがある。
  それぞれの層でルールがあって、7 層 → 1 層で処理が進み、通信手順を整える。

|        |                      |                    |
| ------ | -------------------- | ------------------ |
| 第７層 | アプリケーション層   | Application Layer  |
| 第６層 | プレゼンテーション層 | Presentation Layer |
| 第５層 | セッション層         | Session Layer      |
| 第４層 | トランスポート層     | Transport Layer    |
| 第３層 | ネットワーク層       | Network Layer      |
| 第２層 | データリンク層       | Data-Link Layer    |
| 第１層 | 物理層               | Physical Layer     |

それぞれの層で機能も異なり、独立しているので、例えば第 6 層で変更があったとしても、その変更は第 7 層には影響しない。

#### 7 つの層の機能について

- 第 7 層 アプリケーション層
  ネットワークっていうサービスを提供する。<br/>
  ネットワークの入り口に位置する層で、ネットワークが可能かどうか判断する層.<br/>
  ユーザアプリケーションにネットワーク・サービスを提供するレイヤー。

- 第 6 層 プレゼンテーション層
  データの形式を決める層。文字コード等。<br/>
  送信元と受信先でデータの形式を揃えないとダメなので。<br/>
  通信するコンピュータ同士がお互い解読できる言語で送ってあげないと受信先で文字化けを起こしたりするって話し。
  解読可能な共通言語に変えているのがこのレイヤー。

- 第 5 層 セッション層
  データの通信の会話をただ成立させている層。<br/>
  よくわからんけど、よくわからんでも良いらしい。

- 第 4 層 トランスポート層
  > ノード間のデータ転送の信頼性を確保するための規定を定めている

エラーを少なくする、確実に通信を成功させるといった信頼性の高い通信サービスを保証する層。<br/>
ホストのどの通信アプリケーションへのデータか と言うところを判別するためにヘッダ-に ポート番号をつけたり、シーケンス番号をつけたりするのはこの層。<br/>
使用プロトコルは TCP か　 UDP が使われる。<br/>

- 第 3 層 ネットワーク層

  > ネットワークとネットワークを相互通信するための規定を定めている。

  離れた場所に存在する相手との間でデータの伝送・運ぶルートの決定(ルーター)・宛先の決定などを行う。<br/>
  どの機器と言う情報（MAC アドレス）が何処にあるかと言った情報の論理アドレスである IP アドレスをこの層で獲得する。

- 第 2 層 データリンク層
  近くの機器とのデータの伝送制御を行う層。
  アドレッシング(簡単にいうと識別のこと)によって、物理アドレスを取得する役割がある。

  - 物理アドレス(MAC アドレス)
    メディアに直接接続されている**誰に届けるか**を識別するための機器の固有番号のことを物理アドレスという。<br/>
    この固有番号は ネットワークインターフェースカード(NIC) が作られた段階で決定されている。
    が、MAC アドレスは、実際の位置と無関係なのでインターネット上の場所を特定することは出来ない。したがって、実際に活用するためにはレイヤ-3 で付加される論理アドレスと一緒に使用しないと何処のネットワークにあるどの機器かと言う情報はわからない。

  - NIC
    現在ではネットワーク用のコネクタや通信用 IC などがあらかじめ PC に内蔵されていることも多く、単体の NIC を用いることはあんまりないみたい。こいつがないとそもそも通信はできず、こいつを拡張的に装着して通信機能を付与していたらしいが今はほぼ初期装備。

- 第 1 層 物理層

  > ビット列を電気信号にするための規定を定めている。

  電気・機械的なルールを決めた層。電気信号のやりとりするところ。
  上の層から送られてきたデータを。外部へと送り出す最後のところになる 。<br/>
  この時にデータのビット列を電気信号に変換する。
  つまり、実際に銅線、光ファイバー、無線 LAN などの回線を通し、デジタル信号とかを使用し、通信を行う。<br/>
  もちろんこの際に障害が起こるらしく、ケーブルを通信の際は使用しているためこのケーブルの抵抗が起因する。
  抵抗についてはいろいろあり、近くに無線がある、別の銅線が近くにある、AC 電源が近くにあっても抵抗が生まれるらしい。<br/>

  - ハブ

    - 上記の抵抗に寄る障害を解決してくれる。要は抵抗等に（ノイズもあるみたい）よって減衰した信号の波形を元へ戻すために増幅し、
      ノイズを除去したりして元の信号に整形してくれる役割。
    - 多くの機器とつなぐ役割。

  - リピータ
    これはハブの最初の役割とやることは同じなので略。

#### データとデータの送信

データを送る際にデータだけを送るということはできない。<br/>
宛先や IP アドレスや通信制御用のデータもまとめて一緒に送らないとそもそも送信できない。
この塊のことを**データユニット**という。

- データのカプセル化
  オブジェクト思考のカプセル化とは違うもの。

  > OSI 参照モデルは各層にそれぞれの通信に関する規定を定めておりその規定に従ったデータを作るために、第 7 層から第 1 層の順で各層で送り主や宛先などの情報を梱包していく。この梱包していくことを カプセル化 という。また逆に、送り先のコンピュータは送られたデータを開封していかなければならない。この開封していくことを、 非カプセル化 と言う。

  引用 : http://www.infraexpert.com/study/networking3.html

  | 階層      | データの呼び名 |
  | --------- | -------------- |
  | レイヤ- 4 | セグメント     |
  | レイヤ- 3 | パケット       |
  | レイヤ- 2 | フレーム       |
  | レイヤ- 1 | ビット         |

  上記の図のように、5~６のレイヤーではデータと呼ぶが、レイヤー 4 からは名前が変わり、レイヤー 4 で制御情報がくっついたデータを**セグメント**と呼び、さらにレイヤー 4 のものにレイヤー 3 での制御情報がくっついたものを**パケット**と呼ぶ。同様に、**フレーム**、**ビット**と呼称が変わる。
