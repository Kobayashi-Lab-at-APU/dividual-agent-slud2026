# 分人型対話エージェントにおける相手に応じた発話スタイルの評価 — 評価材料

鈴木 丈慈，小林 邦和(愛知県立大学)．対話システムシンポジウム 2026(人工知能学会 言語・音声理解と対話処理研究会)の原稿
「分人型対話エージェントにおける相手に応じた発話スタイルの評価」で用いた，プロンプト・人物の説明文・台本・LLM 評価者への質問を公開する．

**人物はすべて架空であり，データは研究のために作った合成データである．実在の人物とは関係しない．**

## 中身

| フォルダ | 内容 |
|---|---|
| `prompts/generation/<生成側の条件>/user_a〜f.txt` | 応答の生成に用いたシステムプロンプト(人物 a〜f)．条件は `preference_specified`(好みの指定)・`personality_only`(人柄のみ)・`no_information`(情報なし) |
| `prompts/templates/` | 上のシステムプロンプトのテンプレート(共通ベースプロンプトと個人向け分人のプロンプト) |
| `personas/preference_descriptions.yaml` | 好みの説明文(発話スタイルの好みを明示した説明文) |
| `personas/personality_knowledge_descriptions.yaml` | 人柄と知識量の説明文と，その第1文 |
| `personas/preferences.yaml` | 各人物の発話スタイルの好みの値 |
| `scenarios/scenarios.yaml` | 台本18本の利用者発話(各3ターン．全人物で共通) |
| `judge/matching/<生成側>__<評価側>/` | LLM 評価者に渡したマッチング課題の質問(1台本×3名の応答と3名の説明文)と正解 `key.json` |
| `judge/descriptions/` | 2種の説明文どうしの対応づけと，人柄と知識量の説明文から好みを予想させた質問 |

### 生成側と評価側の条件

- 生成側(分人に与える相手の情報): 好みの指定 / 人柄のみ / 情報なし
- 評価側(LLM 評価者に示す説明文): 好みの説明文(`preference_description`)/ 人柄と知識量の説明文(`personality_knowledge`)/ その第1文のみ(`first_sentence`)
- `judge/matching/` には，原稿の表4の5セルに当たる組と，原稿では扱っていない「好みの指定 × 第1文のみ」の組(`preference_specified__first_sentence`)を置いた
- 原稿で扱っていない条件: 上のほかに，生成側では人柄と知識量の説明文に発話スタイルの観点の一覧(応答の長さ・丁寧さ・質問を返す頻度など)を加えた条件と，さらに相手との関係を表す一文を加えた条件を，評価側では人柄と知識量の説明文の第1文だけを示す条件を評価した．正答率(Claude / Gemma)は，前の2条件を人柄と知識量の説明文で判定して 0.796 / 0.843 と 0.963 / 0.833，第1文のみ(好みの指定で生成した応答)は 0.926 / 0.815 であった．関係の一文は丁寧さの好みに応じて選んだため丁寧さの手がかりを含み，第1文に含まれる観点は人物により異なるため，原稿では扱っていない．生成側の2条件のプロンプトと質問はこのリポジトリに含めていない

### マッチング課題の質問

- 1ファイルが LLM 評価者への1回の入力である．指示文・3名の説明文(記号 A〜C)・3名に向けた応答(番号1〜3．各3ターン)からなる
- `__o0` と `__o1` は応答の提示順だけを入れ替えたもの．Claude には `__o0` だけを，Gemma 4 31B-it には両方を判定させた
- Gemma 4 31B-it の判定の設定: `google/gemma-4-31B-it`(revision `842da37`)，bf16，思考モードなし，貪欲デコード，最大生成長 2048 トークン
- `key.json` は，各質問の応答番号・説明文の記号がどの人物に当たるか(`responses`・`cards`)と，正解の対応(`truth`)
- 質問の中の応答は Llama-3.1-Swallow-8B-Instruct-v0.5 が生成したものである(乱数シード1つ，最大生成長768トークン)

### システムプロンプトについて

`prompts/generation/` のシステムプロンプトは，生成記録に残した条件・好みの値・人柄と知識量の説明文・関係の段階から，
生成時と同じプロンプト組み立てのコードで組み立て直したものである(生成後にプロンプトの組み立てのコードとテンプレートは変更していない)．
人物 c だけ関係の段階が他と異なる(`personalized`．他は `familiar`)．

## ライセンス

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.ja)．利用するときは下の原稿を引用してほしい．

> 鈴木 丈慈，小林 邦和：分人型対話エージェントにおける相手に応じた発話スタイルの評価，第108回 人工知能学会 言語・音声理解と対話処理研究会(対話システムシンポジウム 2026)(2026)．

連絡先: 鈴木 丈慈 <suzukijoji0606@gmail.com>

---

## English summary

Materials for the paper "Evaluation of Interlocutor-Specific Speaking Styles in a Dividual-Based Dialogue Agent"
(Joji Suzuki and Kunikazu Kobayashi, Aichi Prefectural University; Dialogue System Symposium 2026, JSAI SIG-SLUD).
All personas are fictional and all data are synthetic.

- `prompts/`: system prompts used to generate responses for each persona and condition, and their templates
- `personas/`: persona descriptions (explicit speaking-style preferences; personality and knowledge level) and preference values
- `scenarios/`: 18 three-turn user scripts shared by all personas
- `judge/`: the exact inputs given to the LLM judges (Claude and Gemma 4 31B-it) for the matching task, with answer keys

License: CC BY 4.0.
