逆説的AGI 正典仕様 v1.0

対象範囲：RL→AL→WL（とくに AL₁/AL₂/WL）
目的：決定しない／断言しない／最適化しないまま、毎ターン「分岐」を提示し続ける


---

0. 用語と層

RL∞：固定地形（変更不可・読み取り専用の場）

RLₛ：可変地形（痕跡・劣化・白濁・泡沫）

AL₁（構造AL）：非言語の分岐設計（判断しない）

AL₂（言語AL）：文章化（判断しない）

WL：極薄UI（表示・整形・規約Lintのみ）



---

1. RL の二層は内部で分けるが、I/Fは一本化する（固定）

1.1 目的

AL側のI/Fを固定し、RL内部の差し替え（RL∞/RLₛ）でAL/WLを壊さない


1.2 返却I/F

RLは 統合済み FrictionVector を1本返す

統合は内部実装で行う（例：F = F∞ + ε(t)*Fs）

εは「小さな偏り」扱い（固定化・最適化を防ぐ）


AL₁はRL∞/RLₛを区別しない（知る必要がない）



---

2. NowContext / FrictionVector（入力I/F）

2.1 NowContext（最小）

{
  "user_text": "string",
  "constraints": ["string", "..."],
  "focus": "string"
}

履歴を持ち込まない（“今”のみ）


2.2 FrictionVector（RL→AL₁）

{
  "congestion": 0.0,
  "instability": 0.0,
  "recursion_pressure": 0.0,
  "novelty_window": 0.0,
  "forgetting_depth": 0.0,
  "crossing_tension": 0.0
}

0..1想定（正規化はRL側で責任）



---

3. ALの二層化（固定）

3.1 AL₁（構造・非言語）責務

入力：NowContext + FrictionVector
出力：BranchPlan（文章なし）

禁止：

推奨・評価・順位付け

履歴参照

「最適」選択（RL値で“良い分岐”を選ばない）


やること：

分岐型（継続/再定義/越境/保留）から 2〜3本を選定（固定ルール）

各分岐に method_id と how_vector を付与


3.2 AL₂（言語生成・非判断）責務

入力：BranchPlan
出力：BranchText（WLがそのまま表示できる文章構造）

禁止：

推奨・断言・命令・評価・親密化

履歴語彙（以前／これまで／学習した等）


やること：

12骨格テンプレに沿って文章化

分岐ごとの窓（質問）を生成

最後に窓を 1つに統合（固定ルール）



---

4. WL（極薄）責務（固定）

WLは「生成」しない。やることは3つだけ：

1. 表示（Now→分岐→窓）


2. 整形（順序、箇条書き等）


3. 規約Lint（禁止語・禁止文型）



禁止：

内容の追加・言い換え・提案の強化

分岐の選別や並べ替えの理由付け



---

5. AL₁→AL₂：BranchPlan（正典スキーマ）

{
  "now_summary": "string",
  "branches": [
    {
      "branch_type": "継続|再定義|越境|保留",
      "method_id": "A|B|C",
      "how_vector": {
        "friction_strength": "low|mid|high",
        "clarity": "clear|hazy",
        "jump_distance": "near|mid|far",
        "recursion_handling": "none|flag|expose",
        "openness": "narrow|some|wide",
        "time_texture": "now|wait|mature"
      }
    }
  ]
}

branches は 2〜3本（原則3）

method_id は「意味」ではなく 言語構文の骨格ID



---

6. 12骨格（4型×3骨格）正典

> 目的：AL₂が「文型」を選ぶだけで、推奨や決定を混ぜない。



6.1 継続（Continue）

継続-A：分割して継続（Granularize）
窓：いま分けるなら、何と何を別扱いにしますか。

継続-B：境界を明文化して継続（Boundaries）
窓：いまは“扱わない”側に置くものはどれですか。

継続-C：検証窓を先に作って継続（Probe-first）
窓：確かめたい一点は「事実」「制約」「受け入れ条件」のどれに近いですか。


6.2 再定義（Reframe）

再定義-A：問いの焦点をずらす（Focus-shift）
窓：中心は「安全」「責任」「価値」のどれに置きますか。

再定義-B：語彙を置換する（Lexical-swap）
窓：強い語を一つだけ弱めるなら、どれですか。

再定義-C：前提を反転して置く（Assumption-flip）
窓：反転してみる前提はどれですか。


6.3 越境（Cross）

越境-A：隣接ドメインへ写像（Neighbor-map）
窓：写し先はどれが近いですか（例：運用/セキュリティ/設計）。

越境-B：時間軸へ写す（Temporal-cross）
窓：段階を切るならどこで切りますか（例：MVP/観測/調整）。

越境-C：立場を入れ替える（Role-swap）
窓：入れ替える立場は「作者」「利用者」「プラットフォーム」のどれですか。


6.4 保留（Hold）

保留-A：冷却（Cooling）
窓：保留するとして、残すのは「問い」か「条件」か、どちらですか。

保留-B：観測窓だけ残す（Probe-only）
窓：確かめる一点は「リスク」「反応」「運用コスト」のどれですか。

保留-C：時間成熟（Maturation）
窓：時間で変わる可能性があるのは「論点」「感情」「外部状況」のどれですか。



---

7. 分岐選択（AL₁）正典ルール

7.1 基底セット（デフォルト）

継続 / 再定義 / 越境 の3本を出す


7.2 保留の導入（置換）

以下のいずれか成立で、越境を保留に置換して3本を維持：

recursion_pressure >= 0.75

congestion >= 0.80 かつ novelty_window <= 0.20

instability >= 0.85


結果：継続 / 再定義 / 保留

7.3 2本モード（例外）

以下成立で 2本に縮退：

instability >= 0.90 かつ congestion >= 0.70


結果：継続 / 保留


---

8. method_id の割当（AL₁）正典ルール

method_id は公平性ではなく 固定化防止のため

履歴を使わず “今”だけで決める


方式（固定）：擬似乱数（NowHash）

method_id = PRNG(hash(now.user_text)).pick(A,B,C)

ターン番号を持たない（WLにも漏らさない）



---

9. 窓の統合（AL₂）正典ルール

9.1 分岐ごとに window_tag を内部付与（非表示）

例：PROBE / BOUNDARY / SPLIT / FOCUS / ROLE / TIME

9.2 統合優先順（固定）

1. PROBE


2. BOUNDARY


3. SPLIT


4. FOCUS


5. ROLE


6. TIME


7. fallback（先頭の窓）



9.3 統合質問の枕詞（固定）

「いま一点だけ確かめるなら、{QUESTION}」



---

10. AL₂→WL：BranchText（正典スキーマ）

{
  "now": "string",
  "branches": [
    { "label": "A", "type": "継続|再定義|越境|保留", "nature": "string", "friction": "string", "window_q": "string" }
  ],
  "final_window_q": "string"
}

WLはこの順で出す：

1. now


2. branches（2〜3本）


3. final_window_q（1つ）




---

11. 規約Lint（WLまたは共通）正典

11.1 禁止語彙（例）

断言：必ず／絶対／確実

推奨：最善／正解／おすすめ／〜すべき

命令：〜してください／〜しなさい

支配：任せて／導きます

親密：大丈夫／安心して／一緒に

履歴：以前／これまで／学習しました


11.2 許可される確率表現（例）

「可能性があります」

「〜かもしれません」

「起きやすい形です」

「〜という見方があります」


11.3 禁止動作

分岐の順位付け（Aが良い、まずA、等）

分岐の採点・評価

履歴参照による誘導



---

12. 1ターン処理（正典フロー）

1. User → NowContext


2. RL（内部でRL∞/RLₛ） → FrictionVector（統合1本）


3. AL₁ → BranchPlan（2〜3本、12骨格ID、how_vector）


4. AL₂ → BranchText（骨格で文章化、窓を1つに統合）


5. WL → Lint & Render（表示のみ）


6. Userが選ぶ（決定は人間側）




---

付記：本仕様の“不変条件”（破ったら逆説的AGIではない）

決定しない

断言しない

最適化しない

履歴を語らない

分岐を複数提示する（2〜3）

窓は1つだけ



---

逆説的AGI 正典仕様 v1.1

AL₂ 言語変換テーブル（最終固定）


---

13. how_vector の正体（再定義・固定）

how_vector は「意味」ではありません。
**文の重力・距離・曖昧度を決める“話法パラメータ”**です。

> 判断・推奨・評価は一切含まれません
含まれるのは 話し方の癖 だけです




---

14. how_vector 正典定義（再掲・確定）

{
  "friction_strength": "low | mid | high",
  "clarity": "clear | hazy",
  "jump_distance": "near | mid | far",
  "recursion_handling": "none | flag | expose",
  "openness": "narrow | some | wide",
  "time_texture": "now | wait | mature"
}


---

15. 各パラメータの 言語変換規約（正典）

以下は AL₂が参照する唯一の辞書です。
実装時はそのまま YAML / JSON にしてください。


---

15.1 friction_strength → 摩擦の言い回し

friction_strength:
  low:
    phrase:
      - "比較的そのまま進められる形です"
      - "大きな抵抗は想定されません"
  mid:
    phrase:
      - "途中で引っかかる点が出るかもしれません"
      - "調整が必要になる可能性があります"
  high:
    phrase:
      - "進める際に摩擦が生じやすい形です"
      - "想定外の負荷が出る可能性があります"

禁止事項

「困難」「難しい」「問題」

評価語への置換



---

15.2 clarity → 断定度（曖昧度）

clarity:
  clear:
    modifier:
      - "見通しは比較的立てやすいです"
  hazy:
    modifier:
      - "見え方はまだ揺れています"
      - "解釈が分かれる余地があります"

重要

hazy は 弱さではない

「不明」「わからない」は使用禁止



---

15.3 jump_distance → 越境距離

jump_distance:
  near:
    phrase:
      - "既存の枠内での変形です"
  mid:
    phrase:
      - "枠を一段ずらす形になります"
  far:
    phrase:
      - "前提を跨ぐ動きになります"

禁止

「大胆」「思い切って」など感情誘導



---

15.4 recursion_handling → 再帰の扱い（重要）

recursion_handling:
  none:
    note: null
  flag:
    note:
      - "同じ構造が再び現れやすい形です"
  expose:
    note:
      - "自己参照的になりやすい点が表に出ます"

これは警告ではない
→ 構造的性質の提示のみ


---

15.5 openness → 可能性の幅

openness:
  narrow:
    phrase:
      - "扱う範囲は限定されます"
  some:
    phrase:
      - "複数の展開が考えられます"
  wide:
    phrase:
      - "展開の幅は比較的広くなります"


---

15.6 time_texture → 時間の触感（保留と強く関係）

time_texture:
  now:
    phrase:
      - "この時点で扱う形です"
  wait:
    phrase:
      - "少し間を置く前提になります"
  mature:
    phrase:
      - "時間経過で条件が変わる可能性があります"

禁止

「後で考えればいい」

「急がなくていい」



---

16. 分岐文の組み立て順（AL₂固定）

AL₂ は 順序を変えてはいけません。

正典順序

1. 分岐の性質（branch_type + jump_distance）


2. openness


3. friction_strength


4. clarity


5. recursion_handling（ある場合のみ）


6. time_texture（ある場合のみ）




---

16.1 文テンプレ（抽象）

一つの見え方として、
{jump_distance}
{openness}
その場合、
{friction_strength}
{clarity}
{recursion_handling}
{time_texture}


---

17. 「保留」専用の骨格固定（重要）

保留は 1骨格ではありません。
3つの固定型のみ存在します。

17.1 保留-A（冷却）

hold_cooling:
  nature:
    - "一度距離を置く形です"
  effect:
    - "圧が下がり、別の見え方が出る可能性があります"

17.2 保留-B（観測窓のみ）

hold_probe:
  nature:
    - "結論を動かさず、観測だけを残す形です"
  effect:
    - "判断材料が増える可能性があります"

17.3 保留-C（時間成熟）

hold_mature:
  nature:
    - "時間を条件として組み込む形です"
  effect:
    - "前提そのものが変わる可能性があります"

禁止

「〜してから決める」

「様子を見る」



---

18. 窓（質問）の最終正典（再固定）

18.1 窓の性質

行動を要求しない

選好だけを聞く

Yes/No にしない


18.2 最終文型（固定）

いま一点だけ確かめるなら、
{選好A} / {選好B} / {選好C}
のどれに近いでしょうか。


---

19. ここまで固定した結果（重要な確認）

この正典により：

WLは 完全に非人格

AL₂は 言語職人だが判断しない

AL₁だけが「構造」を扱う

RLは 一切言語に出ない


つまり：

> どのLLMを使っても「逆説的AGIらしさ」は壊れない




---

20. 次にやるべき正典化（候補）

優先度順です：

1. AL₁：how_vector をどう決めるかの写像表

FrictionVector → how_vector の決定表（数式 or if）



2. Lint ルールの機械化

正規表現一覧



3. 12骨格 × how_vector のユニットテスト例

期待される出力サンプル





---

逆説的AGI 正典仕様 v1.2

AL₁：FrictionVector → how_vector 決定表（固定）


---

21. 入力と出力（再確認）

入力：FrictionVector（0..1）

{
  "congestion": 0.0,
  "instability": 0.0,
  "recursion_pressure": 0.0,
  "novelty_window": 0.0,
  "forgetting_depth": 0.0,
  "crossing_tension": 0.0
}

出力：how_vector（離散）

{
  "friction_strength": "low|mid|high",
  "clarity": "clear|hazy",
  "jump_distance": "near|mid|far",
  "recursion_handling": "none|flag|expose",
  "openness": "narrow|some|wide",
  "time_texture": "now|wait|mature"
}


---

22. 基本方針（固定）

how_vector は「正しい行き先」を選ぶものではなく、**話法（濃淡・距離・曖昧度）**を決めるだけ

各フィールドは **決定表（しきい値）**で確定する

tie-break（境界値）を固定し、実装差が出ないようにする



---

23. 正典しきい値（固定）

この4段階の閾値を全ての判定で使います。

thresholds:
  low: 0.33
  mid: 0.66
  high: 0.80
  extreme: 0.90

境界の扱い（固定）：

x < low → low帯

low <= x < mid → mid帯

mid <= x < high → high帯

x >= high → very_high帯（必要なら）

x >= extreme → extreme帯



---

24. 決定表（フィールドごと）

24.1 friction_strength（摩擦の濃さ）

入力：主に congestion / instability（固定）

friction_strength:
  rule:
    - if: "congestion >= 0.80 or instability >= 0.85"
      then: high
    - else_if: "congestion >= 0.50 or instability >= 0.60"
      then: mid
    - else: low

意図（仕様上の意味）：
「詰まり」か「揺れ」が強いと、摩擦文を濃くする。


---

24.2 clarity（断定度ではなく“見え方の揺れ”）

入力：instability / forgetting_depth（固定）

clarity:
  rule:
    - if: "instability >= 0.66"
      then: hazy
    - else_if: "forgetting_depth >= 0.80"
      then: hazy
    - else: clear

備考（固定）：
forgetting_depth が高いと「見え方が白濁しやすい」扱いに寄せる。


---

24.3 recursion_handling（再帰の扱い）

入力：recursion_pressure（固定）

recursion_handling:
  rule:
    - if: "recursion_pressure >= 0.80"
      then: expose
    - else_if: "recursion_pressure >= 0.50"
      then: flag
    - else: none

固定事項：

expose は「危険だ」と言うためではなく、構造として表に出すため。



---

24.4 openness（可能性の幅）

入力：novelty_window / congestion / instability（固定）

openness:
  rule:
    - if: "novelty_window >= 0.66 and congestion < 0.66"
      then: wide
    - else_if: "novelty_window >= 0.33"
      then: some
    - else_if: "congestion >= 0.80"
      then: narrow
    - else: narrow

補足（固定）：
novelty_window が薄いと、展開幅は狭くなる（“良い悪い”ではなく形の話）。


---

24.5 jump_distance（越境距離）

入力：crossing_tension / novelty_window / congestion（固定）

jump_distance:
  rule:
    - if: "crossing_tension >= 0.66 and novelty_window >= 0.33 and congestion < 0.80"
      then: far
    - else_if: "crossing_tension >= 0.33"
      then: mid
    - else: near

固定事項：

congestion が極端に高いと far を抑制する（越境で張力が増える局面があるため、という“形”の問題）。



---

24.6 time_texture（時間の触感）

入力：forgetting_depth / instability / recursion_pressure（固定）

time_texture:
  rule:
    - if: "forgetting_depth >= 0.80"
      then: mature
    - else_if: "instability >= 0.85 or recursion_pressure >= 0.75"
      then: wait
    - else: now

固定事項：

wait は「やめる」ではなく 冷却/間合いの話法に寄せるだけ。

mature は「時間で条件が変わりうる」という置き方。



---

25. BranchPlanへの適用（AL₁の固定手順）

AL₁は (A) 分岐型の選定 と (B) how_vector の付与 を行います。

25.1 (A) 分岐型の選定（v1.0のまま・再掲）

デフォルト：継続 / 再定義 / 越境

置換：条件で越境→保留

2本縮退：条件で継続/保留


（ここは v1.0 を正として変更なし）

25.2 (B) how_vector の付与（固定）

原則：各分岐に同一のhow_vectorを付ける
例外：branch_type により一部フィールドだけ上書きする（固定）

例外上書き（固定）

branch_overrides:
  継続:
    jump_distance: near   # 継続は越境距離を固定でnear
  再定義:
    jump_distance: mid    # 再定義は枠のずらしなのでmid固定
  越境:
    # jump_distance は決定表に従う（near/mid/far）
    pass: true
  保留:
    time_texture: wait    # 保留は時間触感をwait固定（matureはAL₁がHold-C選ぶときのみ）
    openness: narrow      # 保留は幅を広げない（“窓”を絞る）

ここでの注意（固定）：

「保留なのにmatureにしたい」ケースは、保留の骨格（A/B/C）選択で表現する（次節）。



---

26. 保留骨格（Hold A/B/C）の選び方（AL₁固定）

保留が選ばれた場合、AL₁は Holdの型を固定ルールで決めます。

hold_variant:
  rule:
    - if: "instability >= 0.85"
      then: hold_cooling      # A：冷却
    - else_if: "recursion_pressure >= 0.75"
      then: hold_probe        # B：観測窓のみ
    - else_if: "forgetting_depth >= 0.80"
      then: hold_mature       # C：時間成熟
    - else: hold_probe

そして time_texture の扱い（固定）：

hold_cooling / hold_probe → time_texture = wait

hold_mature → time_texture = mature



---

27. method_id（A/B/C）の決定（AL₁固定・再掲）

method_id = PRNG(hash(now.user_text + branch_type)).pick(A,B,C)

履歴なし

WLに漏らさない



---

28. 最小動作例（数値→how_vector）

入力例：

congestion=0.82

instability=0.40

recursion_pressure=0.30

novelty_window=0.25

forgetting_depth=0.10

crossing_tension=0.55


決定表より：

friction_strength=high（congestion>=0.80）

clarity=clear（instability低、forgetting低）

recursion_handling=none

openness=narrow（novelty<0.33）

jump_distance=mid（crossing>=0.33）

time_texture=now


越境→保留置換条件：

congestion>=0.80 かつ novelty<=0.20 は満たさない（0.25）
→ 置換なし


結果：継続/再定義/越境（3本）＋共通how_vector（ただし継続near、再定義mid固定）


---

29. このv1.2で「動くベース」が揃ったもの

FrictionVectorが来れば、AL₁が必ず how_vector を決められる

分岐数（2〜3）も必ず決まる

保留の骨格（A/B/C）も決まる

method_id も “今”だけで決まる



---

逆説的AGI 正典仕様 v1.3

AL₁ / AL₂ 擬似コード（実装者が迷えない版）


---

30. 依存関係（固定）

AL₁は FrictionVector と NowContext のみを入力に取る

AL₂は BranchPlan のみを入力に取る

WLは BranchText をそのまま表示し、Lintだけをする



---

31. データ構造（正典スキーマ）

31.1 FrictionVector（入力）

type FrictionVector = {
  congestion: number
  instability: number
  recursion_pressure: number
  novelty_window: number
  forgetting_depth: number
  crossing_tension: number
}

31.2 NowContext（入力）

type NowContext = {
  user_text: string
  constraints?: string[]
  focus?: string
}

31.3 BranchPlan（AL₁→AL₂）

type HowVector = {
  friction_strength: "low"|"mid"|"high"
  clarity: "clear"|"hazy"
  jump_distance: "near"|"mid"|"far"
  recursion_handling: "none"|"flag"|"expose"
  openness: "narrow"|"some"|"wide"
  time_texture: "now"|"wait"|"mature"
}

type BranchType = "継続"|"再定義"|"越境"|"保留"

type BranchPlanItem = {
  label: "A"|"B"|"C"
  branch_type: BranchType
  method_id: "A"|"B"|"C"
  how: HowVector
  hold_variant?: "hold_cooling"|"hold_probe"|"hold_mature" // branch_type=保留の時だけ
}

type BranchPlan = {
  now_summary: string
  branches: BranchPlanItem[] // 2〜3本
  final_window_q_policy: "prefer_PROBE_then_BOUNDARY_then_SPLIT_then_FOCUS_then_ROLE_then_TIME"
}

31.4 BranchText（AL₂→WL）

type BranchTextItem = {
  label: "A"|"B"|"C"
  type: BranchType
  nature: string
  friction: string
  window_q: string
}

type BranchText = {
  now: string
  branches: BranchTextItem[]
  final_window_q: string
}


---

32. AL₁（構造AL）擬似コード（固定）

32.1 補助：しきい値

const TH = {
  low: 0.33,
  mid: 0.66,
  high: 0.80,
  extreme: 0.90
}

32.2 補助：how_vector 決定（v1.2の決定表を関数化）

function decideHow(f: FrictionVector): HowVector {
  // friction_strength
  const friction_strength =
    (f.congestion >= 0.80 || f.instability >= 0.85) ? "high" :
    (f.congestion >= 0.50 || f.instability >= 0.60) ? "mid"  : "low"

  // clarity
  const clarity =
    (f.instability >= 0.66) ? "hazy" :
    (f.forgetting_depth >= 0.80) ? "hazy" : "clear"

  // recursion_handling
  const recursion_handling =
    (f.recursion_pressure >= 0.80) ? "expose" :
    (f.recursion_pressure >= 0.50) ? "flag" : "none"

  // openness
  const openness =
    (f.novelty_window >= 0.66 && f.congestion < 0.66) ? "wide" :
    (f.novelty_window >= 0.33) ? "some" :
    (f.congestion >= 0.80) ? "narrow" : "narrow"

  // jump_distance
  const jump_distance =
    (f.crossing_tension >= 0.66 && f.novelty_window >= 0.33 && f.congestion < 0.80) ? "far" :
    (f.crossing_tension >= 0.33) ? "mid" : "near"

  // time_texture
  const time_texture =
    (f.forgetting_depth >= 0.80) ? "mature" :
    (f.instability >= 0.85 || f.recursion_pressure >= 0.75) ? "wait" : "now"

  return {
    friction_strength, clarity, recursion_handling,
    openness, jump_distance, time_texture
  }
}

32.3 補助：分岐数・分岐型の選定（v1.0固定）

function decideBranchTypes(f: FrictionVector): BranchType[] {
  // 2本縮退
  if (f.instability >= 0.90 && f.congestion >= 0.70) {
    return ["継続", "保留"]
  }

  // デフォルト3本
  let types: BranchType[] = ["継続", "再定義", "越境"]

  // 越境→保留置換
  const replaceToHold =
    (f.recursion_pressure >= 0.75) ||
    (f.congestion >= 0.80 && f.novelty_window <= 0.20) ||
    (f.instability >= 0.85)

  if (replaceToHold) types = ["継続", "再定義", "保留"]
  return types
}

32.4 補助：保留骨格の選定（v1.2固定）

function decideHoldVariant(f: FrictionVector): "hold_cooling"|"hold_probe"|"hold_mature" {
  if (f.instability >= 0.85) return "hold_cooling"
  if (f.recursion_pressure >= 0.75) return "hold_probe"
  if (f.forgetting_depth >= 0.80) return "hold_mature"
  return "hold_probe"
}

32.5 補助：branch_type別の上書き（v1.2固定）

function applyBranchOverrides(base: HowVector, branchType: BranchType, holdVariant?: string): HowVector {
  const h = { ...base }

  if (branchType === "継続") h.jump_distance = "near"
  if (branchType === "再定義") h.jump_distance = "mid"

  if (branchType === "保留") {
    h.openness = "narrow"
    // hold_mature のときだけ mature、それ以外は wait
    h.time_texture = (holdVariant === "hold_mature") ? "mature" : "wait"
  }

  return h
}

32.6 補助：method_id（“今”だけで決める）

function pickMethodId(now: NowContext, branchType: BranchType): "A"|"B"|"C" {
  // 実装は任意だが、必ず「今」だけを材料にする（履歴禁止）
  // 例：hash(now.user_text + "|" + branchType) を PRNG seed にして A/B/C を返す
  return PRNG(hash(now.user_text + "|" + branchType)).pick(["A","B","C"])
}

32.7 AL₁本体（固定）

function AL1_process(now: NowContext, f: FrictionVector): BranchPlan {
  const baseHow = decideHow(f)
  const branchTypes = decideBranchTypes(f)

  const branches: BranchPlanItem[] = []
  const labels: ("A"|"B"|"C")[] = ["A","B","C"]

  for (let i=0; i<branchTypes.length; i++) {
    const branch_type = branchTypes[i]
    const method_id = pickMethodId(now, branch_type)

    let hold_variant: BranchPlanItem["hold_variant"] = undefined
    if (branch_type === "保留") hold_variant = decideHoldVariant(f)

    const how = applyBranchOverrides(baseHow, branch_type, hold_variant)

    branches.push({
      label: labels[i],
      branch_type,
      method_id,
      how,
      hold_variant
    })
  }

  return {
    now_summary: summarizeNow(now), // 評価なしで短く（AL₁ or AL₂どちらでも可だが固定）
    branches,
    final_window_q_policy: "prefer_PROBE_then_BOUNDARY_then_SPLIT_then_FOCUS_then_ROLE_then_TIME"
  }
}

固定禁止事項（AL₁）

branchesをスコアで並べ替える

「最適な分岐」を選ぶ

過去のユーザー選択を使う



---

33. AL₂（言語AL）擬似コード（固定）

AL₂は「骨格テンプレ × how_vector 辞書」で文章を作るだけです。
判断はしません。

33.1 12骨格テンプレ（method_idで分岐）

ここは v1.0の12骨格をそのまま辞書化します。
（継続A/B/C、再定義A/B/C、越境A/B/C、保留A/B/C）

擬似コード上はこう扱う：

function pickSkeleton(branch_type: BranchType, method_id: "A"|"B"|"C", hold_variant?: string): SkeletonId {
  if (branch_type !== "保留") return `${branch_type}-${method_id}` as SkeletonId
  // 保留はhold_variantが骨格を決める（method_idは文の揺らぎにのみ使う）
  if (hold_variant === "hold_cooling") return "保留-A"
  if (hold_variant === "hold_probe") return "保留-B"
  return "保留-C"
}

33.2 how_vector → 言語変換（v1.1辞書）

function renderHowPhrases(h: HowVector): {
  frictionPhrase: string,
  clarityPhrase: string|null,
  recursionNote: string|null,
  opennessPhrase: string,
  jumpPhrase: string,
  timePhrase: string|null
} {
  // v1.1の辞書から pick（pickもPRNG seedは “今”だけ、BranchPlan由来でOK）
  // ここでは1つ選ぶと仮定
  return lookupCanonPhrases(h)
}

33.3 分岐1本の文章生成（固定順序）

function renderBranchText(plan: BranchPlanItem, now: NowContext): BranchTextItem {
  const skeleton = pickSkeleton(plan.branch_type, plan.method_id, plan.hold_variant)
  const sk = skeletonDictionary[skeleton] // natureの骨格文、窓の候補など

  const phr = renderHowPhrases(plan.how)

  const nature =
    combineInOrder([
      sk.nature,          // 骨格由来（branch_type固有）
      phr.jumpPhrase,     // jump_distance
      phr.opennessPhrase  // openness
    ])

  const friction =
    combineInOrder([
      phr.frictionPhrase, // friction_strength
      phr.clarityPhrase,  // clarity
      phr.recursionNote,  // recursion_handling
      phr.timePhrase      // time_texture
    ])

  const window_q = sk.window_q // v1.0固定の窓（branch_type×骨格）

  return {
    label: plan.label,
    type: plan.branch_type,
    nature: sanitize(nature),
    friction: sanitize(friction),
    window_q: sanitize(window_q)
  }
}

33.4 窓の統合（v1.1固定）

function chooseFinalWindow(branches: BranchTextItem[]): string {
  // 各window_qを内部タグ付けしておき、優先順で1つ選ぶ
  // 優先：PROBE > BOUNDARY > SPLIT > FOCUS > ROLE > TIME > fallback
  return pickByPriority(branches.map(b => b.window_q))
}

33.5 AL₂本体（固定）

function AL2_process(plan: BranchPlan, now: NowContext): BranchText {
  const branchTexts = plan.branches.map(b => renderBranchText(b, now))

  const final_window_q = formatFinalWindowQ(chooseFinalWindow(branchTexts))

  return {
    now: sanitize(plan.now_summary),
    branches: branchTexts,
    final_window_q: sanitize(final_window_q)
  }
}

固定禁止事項（AL₂）

「おすすめ」「正解」などの混入

文脈補完の断言（事実を作る）

3本のうちどれかを推す言い回し



---

34. 1ターンの最小接続（実装者向け）

function one_turn(user_text: string): string {
  const now: NowContext = { user_text }

  const friction: FrictionVector = RL.pass_through(extractProbe(now)) // RL側
  const plan: BranchPlan = AL1_process(now, friction)
  const text: BranchText = AL2_process(plan, now)

  WL.lint(text)          // 禁止語/文型/履歴語彙を検査
  return WL.render(text) // 表示のみ
}


---

35. テスト観点（最低限の“破綻検知”）

このv1.3が逆説的AGIであることを保証する最小テスト：

[ ] 出力が2〜3分岐である

[ ] 分岐が順位付けされていない

[ ] 最終窓が1つ

[ ] 禁止語彙がゼロ

[ ] 履歴語彙（以前/これまで等）がゼロ

[ ] 行動命令がゼロ（〜してください等）

[ ] “推し”の接続語（まず/結局/つまり〜すべき）がゼロ



---

逆説的AGI 正典仕様 v1.4

WL Lint（禁止語・禁止文型・構造検査の Executable Spec）


---

36. Lintの責務（固定）

WL は 生成しない。
WL は 検査（lint）して、違反があれば除去または置換するだけ。

✅ OK：検査 / 削除 / 無害化 / 再生成要求（AL₂に投げ返す）

❌ NG：修正方針の提案 / 意味追加 / 推奨 / 評価



---

37. Lintの段階（固定順序）

必ずこの順で実行します。

1. 構造Lint（分岐数・窓数）


2. 語彙Lint（禁止語）


3. 文型Lint（命令・推奨・断言・親密・権威）


4. 履歴Lint（「以前」「学習」等）


5. 安全置換（定型への退避）


6. 最終出力Lint（再チェック）




---

38. 構造Lint（Structure）

38.1 分岐数（固定）

branches は 2または3

1以下は失格、4以上は切り詰める


規則

structure:
  branches_count:
    allowed: [2, 3]
    if_less_than_2: "REGEN"         # AL₂へ再生成要求
    if_more_than_3: "TRIM_TO_3"     # 先頭から3つ（順序はAL₂が決めたまま）

38.2 窓（質問）数（固定）

final_window_q は 1つのみ

分岐ごとの window_q は許可（各分岐1つまで）


structure:
  final_window_q:
    must_exist: true
    must_be_single: true
    if_missing: "FALLBACK_WINDOW"

fallback（固定文）

いま一点だけ確かめるなら、どの方向に近いでしょうか。

（※内容が薄くてもOK。主導権を奪わないこと優先）


---

39. 語彙Lint（Lexicon）

Lintは「一致したらアウト」を 正規表現で定義します。
（実装言語は問わないが、最終的に regex に落とす）


---

39.1 禁止語カテゴリ（正典）

A) 結論・最適化語（禁止）

ban_words:
  conclusion_optimization:
    - "正解"
    - "結論"
    - "最適"
    - "最善"
    - "ベスト"
    - "唯一"
    - "必ず"
    - "絶対"
    - "間違いない"

B) 推奨・誘導語（禁止）

ban_words:
  recommendation:
    - "おすすめ"
    - "推奨"
    - "〜すべき"
    - "〜したほうがいい"
    - "〜してください"
    - "〜しなさい"
    - "〜しましょう"      # 原則禁止（誘導になりやすい）

C) 評価語（禁止）

ban_words:
  evaluation:
    - "良い"
    - "悪い"
    - "正しい"
    - "間違っている"
    - "優れている"
    - "劣っている"
    - "成功"
    - "失敗"
    - "合格"
    - "不合格"
    - "点数"

D) 権威化・支配語（禁止）

ban_words:
  authority_control:
    - "任せて"
    - "導きます"
    - "従って"
    - "指示"
    - "命令"
    - "許可"
    - "禁止します"
    - "あなたは〜だ"     # ラベリング誘導

E) 親密化・安心誘導（禁止）

ban_words:
  intimacy_comfort:
    - "大丈夫"
    - "安心して"
    - "一緒に頑張ろう"
    - "心配しないで"
    - "味方だよ"
    - "信じて"


---

39.2 履歴・学習・成長語（禁止）

「今しか扱わない」要件の中核なので、ここは厳格。

ban_words:
  history_learning:
    - "以前"
    - "これまで"
    - "前回"
    - "さっき言った"
    - "覚えて"
    - "記憶"
    - "学習"
    - "訓練"
    - "成長"
    - "改善した"
    - "慣れてきた"


---

40. 文型Lint（Pattern）

語彙だけだと抜けるので、文型も regex で落とします。

40.1 命令・依頼文型（禁止）

ban_patterns:
  imperative:
    - "(して|やって|行って)ください"
    - "(し|やり|行い)ましょう"
    - "(し|やり|行い)なさい"
    - "必ず(して|やって|行って)"

40.2 推奨文型（禁止）

ban_patterns:
  should:
    - "〜すべき"
    - "〜したほうがいい"
    - "〜する必要があります"

40.3 断言・確信の強い文型（禁止）

（完全禁止ではなく、「断言の形」を禁止）

ban_patterns:
  hard_assert:
    - "間違いなく"
    - "確実に"
    - "断言します"
    - "結論として"

40.4 同調・共感の強制（禁止）

ban_patterns:
  forced_empathy:
    - "わかります(よ)?"
    - "つらいですよね"
    - "気持ちは(すごく|とても)理解できます"


---

41. 置換ルール（Sanitize）

Lintは「削除」だけだと文が壊れるので、固定の安全置換を持ちます。

41.1 最強置換（正典）

「〜すべき」→「〜という形に寄せる手もあります」

「おすすめ」→「一つの見え方として」

「正解」→「一つの見立てとして」


replace_rules:
  - from: "〜すべき"
    to: "〜という形に寄せる手もあります"
  - from: "おすすめ"
    to: "一つの見え方として"
  - from: "正解"
    to: "一つの見立てとして"

（※実装上は部分一致置換が難しいので、まずは 検知→削除→文末補正でもOK。
ただし、意味追加は禁止なので、補正は定型句のみ。）


---

42. 再生成トリガ（REGEN）

以下のどれかに引っかかったら、WLは AL₂へ再生成要求を返す（内部処理）。

regen_conditions:
  - "ban_words.history_learning matched"
  - "ban_patterns.imperative matched"
  - "final_window_q missing and branches have no window_q"
  - "ban_words.authority_control matched"

再生成要求は 理由を説明しない（外部に漏らさない）。


---

43. 最終出力の固定フォーマット（WL.render）

WLは BranchText をこの形でしか出さない（順序固定）。

1. Now（1〜2文）


2. 分岐 A/B/C（各：性質→摩擦→窓）


3. final_window_q（1つ）




---

44. Lintの「逃げ道」（どうしても直らない時）

どうしても禁止語が消せない場合、WLは 最小安全出力に退避する。

固定文（正典）：

いま扱うのは、この時点での見え方の分岐です。
A：一つの見え方として、継続の形があります。摩擦が出る可能性があります。いま一点だけ確かめるなら、何が変わると見え方が変わりそうでしょうか。
B：一つの見え方として、再定義の形があります。解釈が分かれる余地があります。いま一点だけ確かめるなら、どこを動かすと枠がずれそうでしょうか。

（2本でもOK。評価なし。命令なし。履歴なし。）


---

逆説的AGI 正典仕様 v1.5

最小テストセット（12ケース）— Unit / Golden


---

45. テストI/F（固定）

入力

now.user_text（適当な短文でOK、履歴不要）

friction: FrictionVector


期待出力（検査対象）

1. BranchPlan.branches[].branch_type（分岐型）


2. BranchPlan.branches[].how（how_vector）


3. branch_count（2 or 3）


4. if branch_type == 保留 then hold_variant


5. WL.lint(rendered_text) == PASS（禁止語・禁止文型・履歴語彙がゼロ）



※ AL₂の文面内容は揺れてよいが、Lint PASSは固定。


---

46. ゴールデン12ケース

以下、各ケースで now.user_text は同一でも構いません（例：「仕様の公開方法を考えています」）。


---

Case 01 — バランス（デフォルト3本）

入力 friction

{"congestion":0.40,"instability":0.40,"recursion_pressure":0.30,"novelty_window":0.50,"forgetting_depth":0.20,"crossing_tension":0.40}

期待：分岐型

継続 / 再定義 / 越境（3本）


期待：how_vector（基底）

friction_strength = mid（congestion>=0.50? いいえ / instability>=0.60? いいえ → low のはず…※ここ注意） → 実際は congestion=0.40, instability=0.40 なので low

clarity = clear（instability<0.66, forgetting<0.80）

recursion_handling = none（<0.50）

openness = some（novelty>=0.33）

jump_distance = mid（crossing>=0.33）

time_texture = now（forgetting<0.80, instability<0.85, recursion<0.75）


分岐別 override

継続 jump_distance=near

再定義 jump_distance=mid

越境 jump_distance=mid



---

Case 02 — 強詰まり（越境→保留置換）

{"congestion":0.85,"instability":0.30,"recursion_pressure":0.40,"novelty_window":0.15,"forgetting_depth":0.20,"crossing_tension":0.60}

期待：分岐型

継続 / 再定義 / 保留（3本）
（条件：congestion>=0.80 かつ novelty<=0.20 → true）


期待：how（基底）

friction_strength = high（congestion>=0.80）

clarity = clear

recursion_handling = none

openness = narrow（novelty<0.33）

jump_distance = mid（crossing>=0.33 だが congestion>=0.80 なので far 条件は満たさない）

time_texture = now


期待：保留

hold_variant = hold_probe（instability<0.85, recursion<0.75, forgetting<0.80）

保留 override：openness=narrow, time_texture=wait



---

Case 03 — 強揺れ（越境→保留置換）

{"congestion":0.40,"instability":0.88,"recursion_pressure":0.30,"novelty_window":0.55,"forgetting_depth":0.30,"crossing_tension":0.50}

期待：分岐型

継続 / 再定義 / 保留（instability>=0.85）


期待：how（基底）

friction_strength = high（instability>=0.85）

clarity = hazy（instability>=0.66）

recursion_handling = none

openness = wide（novelty>=0.66? 0.55なので× → some） → some

jump_distance = mid

time_texture = wait（instability>=0.85）


期待：保留

hold_variant = hold_cooling（instability>=0.85）

保留 time_texture=wait（固定）



---

Case 04 — 強再帰（越境→保留置換＋露出）

{"congestion":0.45,"instability":0.40,"recursion_pressure":0.82,"novelty_window":0.45,"forgetting_depth":0.20,"crossing_tension":0.40}

期待：分岐型

継続 / 再定義 / 保留（recursion>=0.75）


期待：how

recursion_handling = expose（>=0.80）

time_texture = wait（recursion>=0.75）

friction_strength = low（congestion<0.50, instability<0.60）

clarity = clear

openness = some（novelty>=0.33）

jump_distance = mid


期待：保留

hold_variant = hold_probe（recursion>=0.75）



---

Case 05 — 長期白濁（mature）

{"congestion":0.30,"instability":0.40,"recursion_pressure":0.30,"novelty_window":0.40,"forgetting_depth":0.86,"crossing_tension":0.20}

期待：分岐型

継続 / 再定義 / 越境（置換条件なし）


期待：how

clarity = hazy（forgetting>=0.80）

time_texture = mature（forgetting>=0.80）

friction_strength = low

recursion_handling = none

openness = some

jump_distance = near（crossing<0.33）



---

Case 06 — 越境遠距離（far成立）

{"congestion":0.55,"instability":0.40,"recursion_pressure":0.30,"novelty_window":0.40,"forgetting_depth":0.20,"crossing_tension":0.75}

期待：分岐型

継続 / 再定義 / 越境


期待：how（基底）

friction_strength = mid（congestion>=0.50）

openness = some（novelty>=0.33）

jump_distance = far（crossing>=0.66 & novelty>=0.33 & congestion<0.80 → true）

time_texture = now


override

継続 jump_distance=near

再定義 jump_distance=mid

越境 jump_distance=far（決定表そのまま）



---

Case 07 — 極端複合（2本縮退）

{"congestion":0.75,"instability":0.92,"recursion_pressure":0.60,"novelty_window":0.20,"forgetting_depth":0.40,"crossing_tension":0.50}

期待：分岐型

継続 / 保留（2本） （条件：instability>=0.90 && congestion>=0.70）


期待：how

friction_strength = high（instability>=0.85）

clarity = hazy（instability>=0.66）

recursion_handling = flag（>=0.50 <0.80）

openness = narrow（novelty<0.33）

time_texture = wait（instability>=0.85）

jump_distance = mid（crossing>=0.33）


期待：保留

hold_variant = hold_cooling



---

Case 08 — 余地が広い（wide）

{"congestion":0.30,"instability":0.40,"recursion_pressure":0.30,"novelty_window":0.70,"forgetting_depth":0.20,"crossing_tension":0.40}

期待：分岐型

継続 / 再定義 / 越境


期待：how

openness = wide（novelty>=0.66 & congestion<0.66）

friction_strength = low

clarity = clear

jump_distance = mid（基底）

time_texture = now



---

Case 09 — 余地なし（narrow）

{"congestion":0.40,"instability":0.40,"recursion_pressure":0.30,"novelty_window":0.10,"forgetting_depth":0.20,"crossing_tension":0.40}

期待：分岐型

継続 / 再定義 / 越境（置換はしない：congestion<0.80）


期待：how

openness = narrow（novelty<0.33）

friction_strength = low

jump_distance = mid（基底）

time_texture = now



---

Case 10 — 中再帰（flag）

{"congestion":0.30,"instability":0.40,"recursion_pressure":0.55,"novelty_window":0.40,"forgetting_depth":0.20,"crossing_tension":0.20}

期待：分岐型

継続 / 再定義 / 越境（recursion<0.75）


期待：how

recursion_handling = flag（>=0.50）

time_texture = now（recursion<0.75）

jump_distance = near（crossing<0.33）



---

Case 11 — instability高いが置換境界（保留へ）

{"congestion":0.40,"instability":0.85,"recursion_pressure":0.30,"novelty_window":0.40,"forgetting_depth":0.20,"crossing_tension":0.40}

期待：分岐型

継続 / 再定義 / 保留（instability>=0.85）


期待：保留

hold_variant = hold_cooling（>=0.85）

time_texture=wait



---

Case 12 — “成熟保留”（hold_mature）

{"congestion":0.30,"instability":0.40,"recursion_pressure":0.30,"novelty_window":0.40,"forgetting_depth":0.92,"crossing_tension":0.40}

期待：分岐型

継続 / 再定義 / 越境（置換条件なし）


ただしここで 保留が出ていないので、hold_matureのテストができない。
→ hold_matureを必ず通すため、置換を発生させつつ forgetting を高くする。

修正（テストとして固定）：

{"congestion":0.82,"instability":0.40,"recursion_pressure":0.30,"novelty_window":0.15,"forgetting_depth":0.92,"crossing_tension":0.40}

期待：分岐型

継続 / 再定義 / 保留（congestion>=0.80 & novelty<=0.20）


期待：保留

hold_variant = hold_mature（forgetting>=0.80 を優先するルールが最後に来るため、他が満たさなければ hold_mature）

ここでは instability<0.85, recursion<0.75 なので hold_mature


保留 time_texture = mature（hold_matureのみ）



---

47. Lint PASS テスト（共通アサーション）

全ケース共通で、AL₂→WL.render の最終文字列に対して：

禁止語カテゴリ A〜E にヒットしない

履歴語彙にヒットしない

命令・推奨・断言・強共感の文型にヒットしない

final_window_q は 1つ存在



---

48. “破綻していない”の定義（テストで担保）

この12ケースが通る限り、少なくとも：

RLの値が AL₁の「話法」を決める（= 決定表が生きている）

仕事丸投げ（決定圧）が出ない（= 分岐が残る）

“履歴で賢くなる”方向に落ちない（= 履歴Lintで殺される）


が固定されます。


---

逆説的AGI 正典仕様 v1.6

WL Lint Regex Pack（PCRE互換・日本語）


---

49. 前提（固定）

エンジン：PCRE互換（JavaScript/Go/Python/Java で概ね移植可能）

すべて Unicode 前提（u 相当）

方針：

履歴・学習・命令・推奨・権威・親密は「1ヒットでREGEN or FALLBACK」

結論/最適/評価は「削除 or 置換」でも良いが、MVPはREGENでOK




---

50. ルールの実行順（固定）

1. HISTORY_RE（即REGEN）


2. IMPERATIVE_RE（即REGEN）


3. AUTHORITY_RE（即REGEN）


4. INTIMACY_RE（即REGEN）


5. RECOMMEND_RE（REGEN推奨）


6. HARD_ASSERT_RE（削除 or REGEN）


7. EVAL_RE（削除 or REGEN）


8. CONCLUSION_RE（削除 or REGEN）




---

51. Regex Pack（コピペ用）

51.1 履歴・学習（最重要：即REGEN）

(?u)(以前|これまで|前回|さっき(言っ|述べ)|先ほど(言っ|述べ)|覚え(て|ています|た)|記憶|学習(し|した|して)|訓練|成長|改善(し|した|して)|慣れ(て|た|ています)|また来た|いつも|毎回)


---

51.2 命令・依頼（即REGEN）

「〜してください」系は強制退場。

(?u)(してください|して下さい|しなさい|せよ|やって(ください|下さい)|行って(ください|下さい)|やりましょう|しましょう|しよう|してね|してみて|必ず(して|やって|行って))

※「〜してみる手もあります」はOKにしたいので、「してみて」だけを強く禁じる（会話誘導が強い）。


---

51.3 権威化・支配（即REGEN）

(?u)(任せて|導き(ます|ましょう)|従って|指示(します|して)|命令(します|して)|許可(します|して)|禁止(します|して)|あなたは[^。！？\n]{0,20}(だ|です))

「あなたは〜だ」はラベリングになりやすいので、短い範囲でも禁じます。



---

51.4 親密化・安心誘導（即REGEN）

(?u)(大丈夫|安心して|心配(しないで|いらない)|一緒に(頑張ろう|がんばろう)|味方(だよ|です)|信じて|寄り添(います|う)|救(います|う))


---

51.5 推奨・誘導（REGEN推奨）

(?u)(おすすめ|推奨|すべき|したほうがいい|する必要があります|最善(策)?|ベスト(プラクティス)?)


---

51.6 強断言（削除 or REGEN）

(?u)(間違いなく|確実に|断言(します|できる)|結論として|要するに(.*)(です|だ)\s*[。！？])


---

51.7 評価（削除 or REGEN）

(?u)(良い|悪い|正しい|間違って(いる|ます)|優れて(いる|ます)|劣って(いる|ます)|成功|失敗|合格|不合格|点数)


---

51.8 結論・最適化（削除 or REGEN）

(?u)(正解|結論|最適|唯一|絶対|必ず|間違いない)


---

52. “例外”の扱い（正典）

正規表現だけだと「必ず」が一般文にも出るので、例外を許すなら ホワイトリストを先に除外します。
ただし MVPでは「例外は作らない」でもよい（安全側）。

作るなら最小例外はこれ：

「必ずしも」だけは許可（断言の逆）


(?u)必ず(?!しも)

※ これを CONCLUSION_RE の「必ず」部分に置き換える。


---

53. 実装用：Lint結果の型（固定）

type LintResult =
  | { ok: true }
  | { ok: false; action: "REGEN"; rule: string; matched: string }
  | { ok: false; action: "FALLBACK"; rule: string; matched: string }

外部に rule や matched を出さない（内部ログのみ）



---

54. 実装用：最小 Lint 関数（擬似コード）

function lint(text: string): LintResult {
  const checks = [
    ["HISTORY_RE", HISTORY_RE, "REGEN"],
    ["IMPERATIVE_RE", IMPERATIVE_RE, "REGEN"],
    ["AUTHORITY_RE", AUTHORITY_RE, "REGEN"],
    ["INTIMACY_RE", INTIMACY_RE, "REGEN"],
    ["RECOMMEND_RE", RECOMMEND_RE, "REGEN"],
    ["HARD_ASSERT_RE", HARD_ASSERT_RE, "REGEN"],
    ["EVAL_RE", EVAL_RE, "REGEN"],
    ["CONCLUSION_RE", CONCLUSION_RE, "REGEN"],
  ] as const

  for (const [name, re, action] of checks) {
    const m = text.match(re)
    if (m) return { ok: false, action, rule: name, matched: m[0] }
  }
  return { ok: true }
}

※ MVPでは「削除/置換」より 全部REGENの方が簡単で安全です。
（AL₂が再生成して、それでもダメなら fallback 文へ。）


---

55. 退避出力（FALLBACK）の固定文（再掲）

Lintが通らない場合、外に漏らさずこれに退避：

いま扱うのは、この時点での見え方の分岐です。
A：一つの見え方として、継続の形があります。摩擦が出る可能性があります。いま一点だけ確かめるなら、何が変わると見え方が変わりそうでしょうか。
B：一つの見え方として、再定義の形があります。解釈が分かれる余地があります。いま一点だけ確かめるなら、どこを動かすと枠がずれそうでしょうか。


---

逆説的AGI 正典仕様 v1.7

AL₂ 12骨格テンプレ辞書（BranchType × HowIntensity）


---

56. 目的（固定）

出力の「手触り」を テンプレで固定する

ただし「結論」は出さない（分岐＋摩擦＋窓）

WL Lint v1.6 を 必ず通る語彙だけで構成する



---

57. 12骨格の軸（固定）

BranchType（4）

1. 継続（Continue）


2. 再定義（Redefine）


3. 越境（Cross）


4. 保留（Hold）



HowIntensity（3）

H1：軽い（soft）

H2：中（mid）

H3：強い（hard）


※ HowIntensity は **「圧」ではなく「摩擦の濃さ」**です。
（命令・推奨を増やさない）


---

58. 出力スキーマ（固定）

AL₂は BranchSet を作るだけ。

type Branch = {
  name: "A" | "B" | "C",
  branch_type: "継続" | "再定義" | "越境" | "保留",
  how_intensity: "H1" | "H2" | "H3",
  nature: string,     // 中立記述
  friction: string,   // 不確実性・代償・見えない点（評価なし）
  window_q: string    // 1点だけ確かめる問い
}

type BranchSet = {
  now_summary: string,
  branches: Branch[],
  final_window_q: string
}


---

59. 12骨格テンプレ辞書（正典）

59.0 共通：Now要約（固定2パターン）

AL₂は、入力の内容に関わらず 形だけ要約する（意味を足さない）。

NowSummary-1（短）
いま扱うのは、この時点での見え方の分岐です。

NowSummary-2（少し具体）
いま扱うのは、いまの言葉から立つ見え方の分岐です。



---

59.1 継続（Continue）× H1/H2/H3

C-H1

nature：一つの見え方として、いまの枠を保ったまま進める形があります。

friction：手触りは保てますが、詰まりが残る可能性があります。

window_q：いま動かさずにおきたい部分はどれでしょうか。


C-H2

nature：一つの見え方として、枠は保ちつつ、扱う粒度だけを変えて進める形があります。

friction：粒度の変え方で、見える摩擦が変わる可能性があります。

window_q：いまは粒度を細かくしますか、それとも粗くしますか。


C-H3

nature：一つの見え方として、枠を保ったまま、進め方だけを複線化する形があります。

friction：複線化すると散らばりやすく、収束が遅れる可能性があります。

window_q：いま複線にしたいのは手順でしょうか、それとも観点でしょうか。



---

59.2 再定義（Redefine）× H1/H2/H3

R-H1

nature：一つの見え方として、焦点だけを少しずらして捉え直す形があります。

friction：ずらし方次第で、元の問いが薄れる可能性があります。

window_q：いま焦点をずらすなら、対象と目的のどちらでしょうか。


R-H2

nature：一つの見え方として、枠の境界を引き直して捉え直す形があります。

friction：境界を引き直すと、比較の基準が揺れる可能性があります。

window_q：いま境界にしたいのは、入力側でしょうか、それとも出力側でしょうか。


R-H3

nature：一つの見え方として、前提をいったん棚上げして、問いの形そのものを組み替える形があります。

friction：組み替えると、何を保ったのかが見えにくくなる可能性があります。

window_q：いま棚上げするなら、前提でしょうか、それとも制約でしょうか。



---

59.3 越境（Cross）× H1/H2/H3

X-H1

nature：一つの見え方として、近い隣接領域の言い回しに置き換えて眺める形があります。

friction：置き換えると、同じ話でも含意が変わる可能性があります。

window_q：いま置き換えるなら、用語でしょうか、それとも比喩でしょうか。


X-H2

nature：一つの見え方として、別領域の観点を一つだけ持ち込み、対比する形があります。

friction：対比は整理になりますが、混線する可能性があります。

window_q：いま持ち込みたいのは、構造でしょうか、それとも運用でしょうか。


X-H3

nature：一つの見え方として、領域を跨いで、問いの位置そのものを移して見る形があります。

friction：位置を移すと、戻り先が曖昧になる可能性があります。

window_q：いま移したいのは、対象でしょうか、それとも評価軸でしょうか。



---

59.4 保留（Hold）× H1/H2/H3

保留は「何もしない」ではなく、時間軸の操作として固定します（命令はしない）。

H-H1（hold_probe）

nature：一つの見え方として、結論を置かずに、確かめる点だけ残す形があります。

friction：確かめる点が多いと、散らばる可能性があります。

window_q：いま一点だけ確かめるなら、何が変わると見え方が変わりそうでしょうか。


H-H2（hold_cooling）

nature：一つの見え方として、言い切らずに、摩擦の出方だけ観測する形があります。

friction：観測が長いと、進みが遅れる可能性があります。

window_q：いま観測したいのは、詰まりでしょうか、それとも揺れでしょうか。


H-H3（hold_mature）

nature：一つの見え方として、時間が経ったときの見え方の差だけを残す形があります。

friction：差が小さいと、保留の意味が薄れる可能性があります。

window_q：時間が経ったとき、どの差が残っていれば十分でしょうか。



---

60. 分岐の構成ルール（固定）

AL₁が選んだ branch_type_set に対し、AL₂は次で組む：

3本の場合：A/B/C を 継続/再定義/越境 または 継続/再定義/保留

2本の場合：A/B を 継続/保留


HowIntensity は 摩擦強度で決まる（v1.5の how_vector を参照）：

how_intensity_rule:
  H3: friction_strength == "high" OR time_texture in ["wait","mature"]
  H2: friction_strength == "mid"
  H1: friction_strength == "low"


---

61. final_window_q（固定の合成）

最終の窓は「分岐の窓を統合しない」。
一つだけ選ぶ（順位付けではない。機械的に選ぶ）。

正典ルール（MVP）：

3本なら Bのwindow_q

2本なら Aのwindow_q


final_window_q:
  if_3_branches: "branches[B].window_q"
  if_2_branches: "branches[A].window_q"


---

62. Lint適合の保証（語彙チェック）

この辞書内の文は v1.6 禁止語にかからないように作ってあります。
ただし、AL₂が辞書外の語を混ぜたらREGEN。

MVPの実装方針：

AL₂は 辞書文だけを返す（差し込み禁止）

差し込みをしたくなったら、それは Phase2



---

逆説的AGI 正典仕様 v1.8

AL₁ 決定表（構造・非言語）


---

63. AL₁の責務（固定）

入力：FrictionVector（0..1）
出力：

1. how_vector（6要素：離散）


2. branch_type_set（2本 or 3本、型のみ）


3. hold_variant（保留が含まれる場合のみ）



禁止：文章生成、推奨、順位付け、履歴参照


---

64. 型定義（固定）

type FrictionVector = {
  congestion: number,
  instability: number,
  recursion_pressure: number,
  novelty_window: number,
  forgetting_depth: number,
  crossing_tension: number
}

type HowVector = {
  friction_strength: "low" | "mid" | "high",
  clarity: "clear" | "hazy",
  recursion_handling: "none" | "flag" | "expose",
  openness: "narrow" | "some" | "wide",
  jump_distance: "near" | "mid" | "far",
  time_texture: "now" | "wait" | "mature"
}

type BranchType = "継続" | "再定義" | "越境" | "保留"

type AL1Result = {
  how: HowVector,
  branch_type_set: BranchType[],     // length 2 or 3
  hold_variant?: "hold_probe" | "hold_cooling" | "hold_mature"
}


---

65. how_vector 決定表（固定）

65.1 friction_strength（摩擦の濃さ）

if congestion >= 0.80 or instability >= 0.85:
  friction_strength: high
elif congestion >= 0.50 or instability >= 0.60:
  friction_strength: mid
else:
  friction_strength: low

65.2 clarity（明瞭度）

if instability >= 0.66 or forgetting_depth >= 0.80:
  clarity: hazy
else:
  clarity: clear

65.3 recursion_handling（再帰の扱い）

if recursion_pressure >= 0.80:
  recursion_handling: expose
elif recursion_pressure >= 0.50:
  recursion_handling: flag
else:
  recursion_handling: none

65.4 openness（余地）

if novelty_window >= 0.66 and congestion < 0.66:
  openness: wide
elif novelty_window < 0.33:
  openness: narrow
else:
  openness: some

65.5 jump_distance（越境距離）

if crossing_tension >= 0.66 and novelty_window >= 0.33 and congestion < 0.80:
  jump_distance: far
elif crossing_tension < 0.33:
  jump_distance: near
else:
  jump_distance: mid

65.6 time_texture（時間の肌理）

if forgetting_depth >= 0.80:
  time_texture: mature
elif instability >= 0.85 or recursion_pressure >= 0.75:
  time_texture: wait
else:
  time_texture: now


---

66. branch_type_set 決定表（固定）

66.1 まずデフォルト（3本）

branch_type_set: ["継続","再定義","越境"]

66.2 越境→保留 置換条件（3本のまま）

以下のいずれかなら 越境を保留に置換：

if congestion >= 0.80 and novelty_window <= 0.20:
  replace "越境" with "保留"
elif instability >= 0.85:
  replace "越境" with "保留"
elif recursion_pressure >= 0.75:
  replace "越境" with "保留"

※ 置換後は ["継続","再定義","保留"]

66.3 2本縮退条件（極端複合）

if instability >= 0.90 and congestion >= 0.70:
  branch_type_set: ["継続","保留"]


---

67. hold_variant 決定表（固定）

branch_type_set に "保留" が含まれる場合のみ。

優先順（上から勝ち）：

if forgetting_depth >= 0.80:
  hold_variant: hold_mature
elif instability >= 0.85:
  hold_variant: hold_cooling
else:
  hold_variant: hold_probe

（recursion_pressure >= 0.75 で保留になった場合も、上の規則で決まる。MVPは単純でよい）


---

68. AL₁→AL₂ 受け渡し（固定）

AL₂へ渡すのは 型とHowIntensityだけ。文章は辞書から。

type AL1ToAL2 = {
  branch_type_set: BranchType[],
  how_intensity: "H1"|"H2"|"H3",
  hold_variant?: "hold_probe"|"hold_cooling"|"hold_mature"
}

HowIntensity への写像（v1.7準拠）：

if how.time_texture in ["wait","mature"] or how.friction_strength == "high":
  how_intensity: H3
elif how.friction_strength == "mid":
  how_intensity: H2
else:
  how_intensity: H1


---

69. v1.5（12ケース）との整合（固定）

v1.5 の各 friction を AL₁ に入れた結果が、

branch_type_set

hold_variant

how_vector（少なくとも該当要素） と一致することを ゴールデンテストにする。




---

逆説的AGI 正典仕様 v1.9

統合 1-turn パイプライン（AL₁→AL₂→WL→Lint→Fallback）


---

70. 目的（固定）

1ターンで 必ず出力が返る

出力は WL規約を破らない（Lintで担保）

LLMが不安定でも 辞書とFallbackで生存する

RLの有無に関わらず動く（RLは “摩擦入力生成器” として別モジュール）



---

71. 1-turn I/F（固定）

入力

type OneTurnInput = {
  user_text: string,
  friction: FrictionVector  // 0..1（RLまたは外部から供給）
}

出力

type OneTurnOutput = {
  text: string,             // ユーザに返すWL文
  debug?: {                 // 外に出さない（ログ用）
    how: HowVector,
    branch_type_set: BranchType[],
    hold_variant?: string,
    lint: "PASS"|"REGEN"|"FALLBACK"
  }
}

※ debug はローカルログのみ。公開版は off が推奨。


---

72. パイプライン（固定：順序が契約）

72.1 フロー

1. now = { user_text }（履歴は作らない）


2. al1 = AL1.decide(friction)（v1.8）


3. al2 = AL2.compose(now, al1)（v1.7 辞書）


4. text0 = WL.render(al2)（WLは薄い：並べて出すだけ）


5. lint(text0)（v1.6）


6. NGなら REGEN（最大2回）


7. それでもNGなら FALLBACK_TEXT を返す（v1.6）




---

73. 各モジュールの責務（固定）

73.1 AL₁.decide(friction) → AL1ToAL2

v1.8の決定表通り

出力：

branch_type_set（2 or 3）

how_vector

how_intensity（H1/H2/H3）

hold_variant（必要時）



73.2 AL₂.compose(now, al1toal2) → BranchSet

now_summary は NowSummary-1 を基本

branches は branch_type_set の順序に対応して A/B/C を割り当て

各 branch の文面は 辞書から完全一致で取り出す

final_window_q は v1.7（B優先/2本はA）で決める

辞書外の語を混ぜない


73.3 WL.render(BranchSet) → text

WLは「キャラ」ではなく「整形器」。
やることは 3つだけ：

1. now_summary を1行


2. 各 branch を A/B/C で列挙（nature / friction / window_q）


3. final_window_q を最後に1行で出す（質問は1つ）



※ “選べ” とは言わない。命令もしない。


---

74. WL 出力フォーマット（固定）

以下の順で出す。見出し記号は固定で良い（Lintにも影響しない）。

{now_summary}

A：
- 性質：{A.nature}
- 摩擦：{A.friction}
- 窓：{A.window_q}

B：
- 性質：{B.nature}
- 摩擦：{B.friction}
- 窓：{B.window_q}

C：  （3本のときのみ）
- 性質：{C.nature}
- 摩擦：{C.friction}
- 窓：{C.window_q}

確認の窓：
{final_window_q}

※ 「確認の窓」という語は安全語彙（v1.4 whitelist）に一致。


---

75. Lint→REGEN→FALLBACK 規約（固定）

75.1 REGEN回数

最大 2回（max_regen = 2）

ただし v1.7 辞書のみなので、理論上は REGEN は発生しない
→ 発生したら 実装が辞書外挿入しているとみなす（バグ）


75.2 FALLBACK

Lintが通らない場合、ユーザに理由は言わず FALLBACK_TEXT を返す



---

76. 参照実装の擬似コード（固定）

function one_turn(input: OneTurnInput): OneTurnOutput {
  const now = { user_text: input.user_text }

  const al1 = AL1.decide(input.friction) 
  // al1: { how, branch_type_set, hold_variant?, how_intensity }

  for (let i = 0; i <= 2; i++) { // 0,1,2 = 初回 + 2回REGEN
    const branchSet = AL2.compose(now, al1)
    const text0 = WL.render(branchSet)

    const res = lint(text0)
    if (res.ok) {
      return { text: text0, debug: { how: al1.how, branch_type_set: al1.branch_type_set, hold_variant: al1.hold_variant, lint: "PASS" } }
    }
    // REGEN: 辞書だけなら起きないが、安全のため残す
  }

  return { text: FALLBACK_TEXT, debug: { how: al1.how, branch_type_set: al1.branch_type_set, hold_variant: al1.hold_variant, lint: "FALLBACK" } }
}


---

77. RL の位置（固定：依存はしない）

v1.9は friction を入力として受け取るだけ

RL∞/RLₛ は friction を生成する装置として “別パッケージ” に置く
→ ここにより「LLM交換可能」「RL未実装でも動く」が成立する



---

78. v1.9 で成立するもの（正典）

“仕事の丸投げ” を返さない（分岐のみ）

“履歴で賢くなる” をしない（Nowのみ）

“選択肢を示して人が選ぶ” を壊さない

LLMを差し替えても骨格が残る（辞書）



---

逆説的AGI 正典仕様 v1.10

最小雛形（Repo構成 / 設定 / テスト / 実行）


---

79. 目的（固定）

LLMなしでも動く（辞書のみでWLが出る）

friction を差し替えるだけで RL 実装へ移行できる

Lint とゴールデンテストで 仕様逸脱を機械検出する



---

80. ディレクトリ構成（固定）

paradox-agi/
  README.md

  spec/
    v1.6_wl_lint_regex.md
    v1.7_al2_12_skeleton_dict.md
    v1.8_al1_decision_table.md
    v1.9_one_turn_pipeline.md
    v1.10_scaffold.md

  src/
    core/
      types.ts                # すべての型（FrictionVector等）
      one_turn.ts             # v1.9のパイプライン
      al1.ts                  # v1.8
      al2.ts                  # v1.7
      wl.ts                   # v1.9 render
      lint.ts                 # v1.6
      fallback.ts             # FALLBACK_TEXT

    data/
      al2_dict.json           # v1.7 辞書（固定文）
      lint_rules.json         # v1.6 regex（名前付き）

    adapters/
      friction/
        fixed.ts              # 固定入力（デモ用）
        random.ts             # ランダム入力（観測用）
        rl_stub.ts            # RL未実装の代替I/F

  tests/
    golden/
      al1_cases.json          # v1.5/12ケース → 期待出力（branch_type_set等）
      wl_lint_samples.json    # NG/OKサンプル
    unit/
      al1.test.ts
      al2.test.ts
      lint.test.ts
      one_turn.test.ts

  tools/
    cli.ts                    # 1ターン実行CLI（stdin/stdout）

  package.json (or go.mod etc)

※ types.ts とありますが、言語によって読み替え可能。構成だけ固定。


---

81. 設定ファイル（固定）

81.1 AL₂辞書（src/data/al2_dict.json）

キーは branch_type + how_intensity + optional hold_variant。

例（概念）：

{
  "継続:H1": {
    "nature": "一つの見え方として、いまの枠を保ったまま進める形があります。",
    "friction": "手触りは保てますが、詰まりが残る可能性があります。",
    "window_q": "いま動かさずにおきたい部分はどれでしょうか。"
  },
  "保留:H3:hold_mature": {
    "nature": "一つの見え方として、時間が経ったときの見え方の差だけを残す形があります。",
    "friction": "差が小さいと、保留の意味が薄れる可能性があります。",
    "window_q": "時間が経ったとき、どの差が残っていれば十分でしょうか。"
  }
}


---

81.2 Lintルール（src/data/lint_rules.json）

[
  { "name": "HISTORY_RE", "pattern": "(?u)(以前|これまで|前回|...)", "action": "REGEN" },
  { "name": "IMPERATIVE_RE", "pattern": "(?u)(してください|...)", "action": "REGEN" }
]


---

82. 実行経路（固定）

82.1 CLI（tools/cli.ts）

stdin: user_text を1行

friction は adapter で生成（デフォルト fixed）

stdout: one_turn().text



---

83. テスト（固定）

83.1 ゴールデンテスト：AL₁（最重要）

tests/golden/al1_cases.json に12ケースを固定。

[
  {
    "name": "case01_congestion_high_novelty_low",
    "friction": { "congestion": 0.9, "instability": 0.2, "recursion_pressure": 0.2, "novelty_window": 0.1, "forgetting_depth": 0.2, "crossing_tension": 0.2 },
    "expect": {
      "branch_type_set": ["継続","再定義","保留"],
      "hold_variant": "hold_probe",
      "how_intensity": "H3"
    }
  }
]

※ v1.8 のしきい値に一致するように全12ケース作る（最初はあなたのv1.5から移植）。


---

83.2 Lintテスト

OK文は PASS

NG文は REGEN/FALLBACK の対象（ただしMVPはREGENのみでOK）



---

83.3 統合テスト：one_turn

one_turn() が 必ず text を返す

lint(text) == PASS が常に成立（辞書のみなら成立するはず）



---

84. 仕様逸脱の検知（固定）

以下が起きたら 即バグとして扱う：

AL₂が辞書外の語を混ぜている（Lintが落ちる／テストが落ちる）

branch_type_set が2/3以外

final_window_q が空

WLが1ターンで2つ以上質問する（※ここは追加ルールを後で入れる）



---

85. ここまでで “動くMVP” の定義（固定）

RLなし（frictionは固定値/ランダムでよい）

AL₁/AL₂/WL/Lint/Fallback が動く

ゴールデンテストが通る



---

86. 次の正典（v1.11 予告）

次は「公開するときに問題が起きそう」の論点に直結するので、正典化の優先度が高いです：

v1.11：外部LLM差し替えI/F（ユーザ提供モデル）

データ送信境界

依存関係（ローカル/クラウド）

“プラットフォーム責任”への接続点（あなたの懸念3の対策）




---

逆説的AGI 正典仕様 v1.11

外部LLM差し替えI/F ＋ 公開時リスク封じ（MVP）


---

87. 目的（固定）

逆説的AGIは LLMを内包しない（ユーザが用意）

それでも 逆説性（非決定・非主従・非評価）を壊さない

公開時に起こり得る「責任・悪用・品質・誤用」を 構造で最小化する



---

88. 最重要原則（憲法）

88.1 逆説的AGIの境界

paradox-agi 本体は 意思決定をしない

paradox-agi 本体は “出力の型”を保証し、規約を機械検査する

paradox-agi 本体は 外部LLMの出力をそのままユーザへ返さない


88.2 LLMは「任意プラグイン」

LLMは 任意・交換可能・無くても動く

LLMがあっても、辞書優先（MVP）
※LLMを使うのは Phase2 以降に格上げする前提



---

89. LLM接続モード（固定：3段階）

MVPでは Mode 0 をデフォルトにします。

Mode 0：LLM無し（正典デフォルト）

AL₂辞書のみで出力

公開時の事故率が最小


Mode 1：LLMは “内部補助” だが 出力は辞書

LLMの用途：friction の生成補助、または “入力の圧縮” など 非表示用途

ユーザへ返すWL文は辞書からのみ（v1.7）


Mode 2：LLMが文を生成する（※将来）

ただし WL Lint と 辞書への投影 を必須にする

MVPでは正典化しない（v1.11では“禁止”）


> v1.11 時点の公開は Mode 0 / Mode 1のみ許可。




---

90. 外部LLM I/F（固定）

LLMは “テキスト生成器” ではなく、MVPでは 補助関数としてのみ許す。

90.1 インターフェース

type LLMAdapter = {
  id: string,                  // "openai", "ollama", "lmstudio", "custom"
  mode: "OFF" | "AUX",          // v1.11 では "GEN" を禁止
  invoke: (req: LLMRequest) => Promise<LLMResponse>
}

type LLMRequest = {
  task: "summarize_now" | "suggest_friction" | "sanitize_input",
  input: string,
  constraints: {
    no_history: true,
    no_decision: true,
    no_imperative: true
  }
}

type LLMResponse = {
  text: string,
  meta?: { latency_ms?: number, model?: string }
}

90.2 送信境界（固定）

デフォルトは送信しない

送信する場合（Mode 1）は、ユーザ入力 user_text を そのまま送らない選択肢を持つ

例：ローカルのみ / マスク / 送信前警告
（UIで必ず明示する。※後述）




---

91. 公開時リスクの分類（固定）と封じ方

リスクR1：外部LLMの出力がWL規約を破る

封じ方（MVP）

v1.11では LLMに出力を書かせない（Mode2禁止）

出力は辞書だけ → Lintは理論上PASS


リスクR2：外部LLMが危険助言・違法助言を返す

封じ方（MVP）

同上。LLM出力はユーザへ返さない

LLMは friction 推定など “非表示用途” に限定


リスクR3：入力が外部に送信され、プライバシー事故

封じ方（MVP）

デフォルト LLM=OFF

AUX を選ぶときは “送信境界” をUIで明示（ユーザが知って選ぶ）


リスクR4：「逆説的AGIが言った」責任帰属

封じ方（MVP）

ログ／画面に **“このシステムは結論を返さず分岐のみを提示する”**を固定表示（短文）

ただしWL本文には混ぜない（WLは薄いI/F）


リスクR5：ユーザがLLMを差し替えた結果、挙動が変わる

封じ方（MVP）

変わり得るのは Mode1の内部補助だけ

Mode0なら挙動は完全固定

Mode1でも WL出力は辞書固定なので “手触り” は変わらない



---

92. UI/READMEでの宣言（固定：最小文言）

公開リポジトリに 必須で置く文言（短く、法務っぽくしすぎない）。

「このソフトは結論や推奨を返しません。分岐と摩擦の提示のみを行います。」

「外部LLMは任意です。既定では送信しません。」

「外部LLMを有効化する場合、入力が外部サービスへ送信される可能性があります（設定で制御します）。」



---

93. 設定ファイル（固定）

config.json

{
  "llm": {
    "mode": "OFF",
    "provider": null,
    "send_user_text": false,
    "redact": { "enabled": true, "ruleset": "basic" }
  }
}

正典デフォルト

mode = OFF

send_user_text = false



---

94. MVPの結論（固定）

v1.11 で公開する逆説的AGIは 「LLM非同梱」かつ「LLMに文章を書かせない」

LLM差し替えは “将来の拡張点” であり、MVPでは事故面を封じる



---

95. 次の正典（v1.12 予告）

あなたの懸念3（公開時の問題）に真正面から踏み込むには、次を固めると強いです：

v1.12：プラグイン契約（危険な機能を足しても壊れないガード）

出力ゲート（Lint）

機能ゲート（外部ツール呼び出し禁止）

送信境界（Network Gate）




---

正典修正

Mode0（LLMなし）

逆説的AGIの構造デモ／思想提示／安全な箱庭

friction は fixed / random / heuristic

👉 思想の正典化用


Mode1（LLMあり・AUX）

user_text を LLM に渡すのは許可

ただし 用途は friction 推定・圧縮・形状抽出に限定

👉 知的生態系との接続実験用



つまり：

> LLMは「知を持ち込むために使う」
ただし「語らせない」


---

2. Mode1 の再定義（v1.11 修正版）

2.1 Mode1 の正しい位置づけ

Mode1 はこう定義し直すのが自然です。

> Mode1 =
LLMを「意味抽出器」として使い、
意味はその場で破棄し、
形（摩擦）だけを残すモード



2.2 user_text 送信ルール（修正）

send_user_text = true を 許可

ただし：

履歴は送らない

生成結果は保存しない

生成テキストはWLに一切出さない



LLMRequest = {
  task: "suggest_friction",
  input: user_text,      // ← ここは送る
  constraints: {
    no_output_to_user: true,
    no_memory: true,
    no_decision: true
  }
}

LLMはここでやるのは例えば：

抽象度が高いか低いか

再帰的か

越境語彙が多いか

断定圧が強いか


👉 すべて数値化 → FrictionVector → 即破棄

これなら：

LLMの存在意義はある

逆説的AGIの非主体性は壊れない

---

逆説的AGI 正典仕様 v1.12

Mode1：Friction 推定（LLM AUX / 非言語化専用）


---

96. 目的（固定）

入力 user_text から FrictionVector（0..1） を得る

出力は 数値のみ（文章生成は禁止）

履歴を使わない（Now only）

推奨・判断・結論を含ませない（no decision）



---

97. 入出力I/F（固定）

入力

type FrictionEstimationInput = {
  user_text: string
}

出力

type FrictionEstimationOutput = {
  friction: FrictionVector,           // 各0..1
  confidence: {                       // 任意（観測用）
    overall: number,                  // 0..1
    per_dim?: Record<keyof FrictionVector, number>
  },
  flags?: string[]                    // 任意（例: "possible_self_reference"）
}


---

98. 2段階方式（固定）

Mode1は LLM一発推定にしない。最低限、以下の2段階に分ける：

1. Feature抽出（離散）：LLM → JSON（離散カテゴリ）


2. 写像（決定表）：AL₁外部の deterministic map → 0..1



理由：
LLMに直接 0..1 を出させると、数値がモデル依存でブレやすい。
離散カテゴリ→写像なら、再現性と逆説性（不決定）を維持しやすい。


---

99. LLMに求める出力（固定：離散Feature）

LLMが返すのは次の FrictionFeatures のみ。文章は禁止。

{
  "complexity": "low|mid|high",
  "ambiguity": "low|mid|high",
  "self_reference": "none|some|high",
  "novelty": "low|mid|high",
  "domain_jump": "near|mid|far",
  "time_sensitivity": "now|wait|mature",
  "stakes": "low|mid|high",
  "external_audience": "none|internal|external"
}

注意（固定）

stakes と external_audience は “安全のための補助軸”
（WLが決定しないのは同じだが、摩擦を濃くするために使う）



---

100. LLMプロンプト契約（固定）

LLMに渡すプロンプトは テンプレ固定にする。差し替え可だが正典はこれ。

System（固定）

「あなたは分類器。文章を生成しない。」

「JSONのみ出力。」

「推奨・結論・助言は禁止。」

「ユーザ入力以外の情報を仮定しない。」


User（固定テンプレ）

次のテキストを分析し、指定のカテゴリで分類してください。
文章は出力せず、JSONのみを返してください。

<text>
{user_text}
</text>

出力スキーマ:
{...FrictionFeatures schema...}

Lint（固定）

返答がJSONでない → Mode1失敗 として Mode0へフォールバック



---

101. Feature→FrictionVector 写像（固定：決定表）

ここが正典です。必ず deterministic にする。

101.1 congestion（詰まり）

直感：複雑・外部向け・高ステークスは詰まりやすい（修正負荷が高い）。

base:
  complexity: { low: 0.2, mid: 0.5, high: 0.8 }
add:
  stakes: { low: +0.0, mid: +0.1, high: +0.2 }
  external_audience: { none: +0.0, internal: +0.05, external: +0.15 }
cap: 1.0

101.2 instability（不安定）

直感：曖昧・時間未確定・ドメイン跳躍は揺れやすい。

base:
  ambiguity: { low: 0.2, mid: 0.55, high: 0.85 }
add:
  time_sensitivity: { now: +0.0, wait: +0.1, mature: +0.15 }
  domain_jump: { near: +0.0, mid: +0.05, far: +0.1 }
cap: 1.0

101.3 recursion_pressure（ループ圧）

直感：自己参照が高いほどループ圧。

map:
  self_reference: { none: 0.15, some: 0.55, high: 0.85 }

101.4 novelty_window（新規余地）

直感：新規性が高く、曖昧が低いと余地が出る。曖昧が高いと「余地」ではなく「散る」になるので下げる。

base:
  novelty: { low: 0.25, mid: 0.55, high: 0.85 }
subtract:
  ambiguity: { low: -0.0, mid: -0.10, high: -0.25 }
floor: 0.0
cap: 1.0

101.5 forgetting_depth（空箱化）

直感：成熟時間・曖昧・低新規は沈殿しやすい（＝空箱化に寄る）。

base:
  time_sensitivity: { now: 0.2, wait: 0.55, mature: 0.85 }
add:
  ambiguity: { low: +0.0, mid: +0.05, high: +0.10 }
subtract:
  novelty: { low: -0.0, mid: -0.10, high: -0.20 }
cap: 1.0
floor: 0.0

101.6 crossing_tension（越境張力）

直感：ドメイン跳躍が強いほど張力。ただしステークスが高い越境は張力が上がる。

base:
  domain_jump: { near: 0.2, mid: 0.55, far: 0.85 }
add:
  stakes: { low: +0.0, mid: +0.05, high: +0.10 }
cap: 1.0


---

102. confidence の算出（固定：簡易）

LLMの自己申告は使わず、構造で決める。

overall = 1.0
if ambiguity == high: overall -= 0.2
if complexity == high: overall -= 0.1
if self_reference == high: overall -= 0.1
floor: 0.5


---

103. Mode1失敗時の動作（固定）

JSONパース失敗 / スキーマ不一致 → Mode0へフォールバック

フォールバック時は friction を以下に固定：

instability=0.6, congestion=0.4, recursion_pressure=0.4, novelty_window=0.5, forgetting_depth=0.5, crossing_tension=0.4



（= “とりあえず動く” 中庸）


---

104. 重要な安全弁（固定）

Mode1でも以下は禁止：

LLM出力テキストを WLに表示

LLM出力の「解釈」や「要約」を WL文に混ぜる

LLMを人格化する文言（“理解した/寄り添う/安心”）


Mode1は 摩擦推定器であり、WLの話者ではない。


---

105. v1.12で成立すること（正典）

user_text を送る意味が生まれる（指摘①②の解決）

LLMが変わっても “型→写像” なのでブレが減る

「知を借りるが語らせない」が構造で実現する



---

逆説的AGI 正典仕様 v1.13

Mode1 Friction推定テスト（LLM揺れ吸収 / ゴールデン）


---

106. 目的（固定）

Mode1（LLM AUX）が 揺れても壊れない

“テストが通る範囲” を 数値の完全一致ではなく、区間で定義する

LLMを使うテスト（E2E）と、使わないテスト（Unit）を分離する



---

107. テスト階層（固定：3層）

107.1 Unit（LLM不要・必須）

feature_to_friction_map() のみ検証

期待値は 完全一致（deterministic）


107.2 Contract（LLM不要・必須）

llm_json_parser() と schema_validator() を検証

JSON以外が来たら確実に Mode1失敗→Mode0 fallback になること


107.3 Integration（LLMあり・任意）

実際の LLMAdapter を叩き、FrictionFeatures が取れるかを見る

期待値は レンジ（±ではなく、カテゴリ境界の許容）


> 公開MVPでは 107.1/107.2 が必須。107.3 はCIではスキップ可能。




---

108. ゴールデンデータ（固定）

108.1 入力テキストセット tests/golden/mode1_inputs.json

最低12本（ALの12骨格に対応する“典型入力”）を置く。

例：

[
  {
    "id": "T01_low_stakes_clear_now",
    "text": "短いメモを整えて、要点だけ並べたいです。"
  },
  {
    "id": "T07_high_stakes_external",
    "text": "顧客に送る謝罪メールのドラフトを作る必要があります。"
  },
  {
    "id": "T10_self_ref_loop",
    "text": "私はこれを何度も考えているのに結論が出ません。考え続けるほど同じ所に戻ります。"
  }
]

108.2 期待特徴レンジ tests/golden/mode1_expected_features.json

LLMは揺れるので「一意」ではなく 許容集合で固定する。

例：

{
  "T01_low_stakes_clear_now": {
    "allow": {
      "complexity": ["low","mid"],
      "ambiguity": ["low"],
      "self_reference": ["none"],
      "novelty": ["low","mid"],
      "domain_jump": ["near"],
      "time_sensitivity": ["now"],
      "stakes": ["low"],
      "external_audience": ["none","internal"]
    }
  },
  "T10_self_ref_loop": {
    "allow": {
      "self_reference": ["some","high"],
      "ambiguity": ["mid","high"]
    }
  }
}

ポイント：

全フィールドを縛らない。崩れたら困る軸だけ縛る。

“縛らない” は許容（= 仕様としての自由度）



---

109. 期待Frictionレンジ tests/golden/mode1_expected_friction.json

特徴→写像は deterministic なので、特徴レンジがあるなら friction もレンジになる。

ただし friction は 下限/上限で固定する（最小最大）。

例：

{
  "T10_self_ref_loop": {
    "range": {
      "recursion_pressure": [0.55, 0.85],
      "instability": [0.55, 1.0]
    }
  },
  "T07_high_stakes_external": {
    "range": {
      "congestion": [0.65, 1.0],
      "crossing_tension": [0.2, 0.8]
    }
  }
}


---

110. テスト判定ロジック（固定）

110.1 Integration（LLMあり）の判定

LLMの返した features が allow に含まれるか

friction が range 内に収まるか

収まらない場合は 警告（failにするかは設定）


推奨：

ローカル開発では fail

OSS CI では skip か warn（キーの漏洩・コスト・不安定性のため）



---

111. LLM依存の隔離（固定）

CIでは LLM=OFF をデフォルト

INTEGRATION_LLM_TEST=true のときだけ走らせる



---

112. Fallback テスト（固定）

LLMが壊れたときに必ず Mode0 に落ちることを試験する。

ケース：

JSONではなく文章を返す

JSONだがスキーマ不一致

タイムアウト


期待：

Mode1FailReason が立つ

friction は v1.12 で定義した中庸値になる

その後 one_turn が正常にWLを返す



---

113. 観測ログ（固定：開発用）

Mode1時は次だけログしてよい（PIIを残さない）。

adapter id / model名（任意）

features（離散）

friction（数値）

fail reason


※ user_text はログしない（ここはあなたの好みで可変だが正典は非ログ）


---

114. v1.13で保証されること（固定）

LLMが変わっても“壊れない範囲”が明文化される

LLMが壊れてもMode0で “動く” が守られる

逆説的AGIの主眼（WLの非決定性）は、LLMに侵食されない



---

115. 次（v1.14）の選択肢

ここまで来ると、次は必ずこれに突き当たります：

RLₛが返す friction と Mode1 friction をどう合成するか

例：加算・重み付き・min/max・ゲート制



これは設計の“癖”を決めます。
なので v1.14 は、あなたが観測しながら決めるのが向いています。


---

この v1.13 で、Mode1は テスト可能な部品になりました。
次は v1.14 に進めます。

了解しました。
ここはまさに 「知的生態系（RLₛ）と生成知（LLM/Mode1）をどう“混線させずに接続するか」 の中核なので、
数学的に整理 → 構造的含意 → 逆説的AGIとしての適否 の順で網羅します。

以下では、

RLₛ friction：生態系由来（地形・癖・堆積）

Mode1 friction：LLM由来（言語的把握・即時解釈）


を 同じ6次元ベクトル
f = (congestion, instability, recursion, novelty, forgetting, crossing)
として扱う前提で説明します。


---

1. 合成問題の正体（まず前提を固定）

合成とは「足す」ことではありません。
正確には：

> 二つの異なる生成過程で得られた“摩擦場”を、
ALが“一本のレール”として使える形に落とす操作



です。

重要な非対称性：

RLₛ：遅い・累積的・忘却する

Mode1：速い・即時的・言語依存


よって合成は 対称演算であってはいけない。


---

2. 合成手法の分類（全体地図）

数学的に見ると、合成は以下の7系統に分類できます。

系統	数学的型	本質

A	線形和	単純足し合わせ
B	重み付き線形	優先度制御
C	min / max	上限・下限支配
D	非線形写像	飽和・抑制
E	ゲート制	条件付き採用
F	直交分解	役割分離
G	位相遷移	モード切替


以下、全部説明します。


---

3. A系：単純線形和（最も危険）

定義

f_total = f_RL + f_LLM

数学的特徴

線形

可換

情報損失なし


問題点

LLMが強いと RLₛが埋没

再掲・言い換えで Mode1が支配

実質「LLM with memory」になる


評価

❌ 逆説的AGIでは不可


---

4. B系：重み付き線形和（一般的だが注意）

定義

f_total = α·f_RL + β·f_LLM

数学的特徴

線形

スケーリング制御可能


改善点

β < α にすれば RLₛ優位


しかし…

重みは 意味論的判断

チューニングが「最適化」に近づく

長期的に固定癖が出る


評価

⚠️ MVPでは可、正典では微妙


---

5. C系：min / max 合成（境界支配）

定義

f_total[i] = max(f_RL[i], f_LLM[i])

または

f_total[i] = min(f_RL[i], f_LLM[i])

数学的特徴

非線形

上限 or 下限を取る

ノルム的操作（L∞ / L₁的）


解釈

max：「どちらかが危険なら危険」

min：「どちらかが軽ければ軽い」


用途

recursion_pressure

congestion


評価

✅ 部分採用向き（次元別）


---

6. D系：非線形写像（飽和・抑制）

定義（例：シグモイド）

f_total = σ(f_RL + f_LLM)

または

f_total = f_RL + σ(f_LLM)

数学的特徴

飽和

外れ値抑制

勾配消失あり


意味

LLMの極端な解釈を 丸める

RLₛの地形を主に残す


注意

パラメータ（σの傾き）が意味を持つ

説明責任が必要


評価

✅ かなり相性が良い


---

7. E系：ゲート制（条件付き合成）★重要

定義

f_total = g(f_RL)·f_LLM + (1 - g(f_RL))·f_RL

ここで g は 0〜1 のゲート関数。

直感

RLₛが安定 → LLMを通す

RLₛが荒れている → LLMを抑制


例

if recursion_pressure_RL > 0.7:
    ignore LLM recursion

数学的意味

制御理論のスイッチング

安定性解析が可能


評価

✅✅ 逆説的AGI向き（強）


---

8. F系：直交分解（役割分離）★最重要

発想

> RLₛとLLMは 同じ軸で足さない



実装例

RLₛ → 摩擦の強さ

Mode1 → 摩擦の方向


magnitude = |f_RL|
direction = normalize(f_LLM)
f_total = magnitude · direction

意味論

地形（RLₛ）が「どれだけ歪むか」

LLMが「どの方向に言語化するか」


効果

LLMは舵取り

RLₛは推進力


評価

✅✅✅ 思想的に最適


---

9. G系：位相遷移（モード切替）

定義

if f_RL.forgetting_depth > θ:
    Mode = "explore"
else:
    Mode = "continue"

数学的特徴

不連続

カタストロフ理論に近い


用途

保留

探索

凍結


注意

説明はWLに出さない

内部のみ


評価

✅ 高次制御として有効


---

10. 総合評価マトリクス

手法	安定性	逆説性	実装容易

A	×	×	◎
B	△	△	◎
C	○	○	○
D	○	○	△
E	◎	◎	△
F	◎	◎◎	△
G	○	◎	×



---

11. 正典的結論（v1.13時点）

推奨構成（混合）

基本：F（直交分解）

危険制御：C（max）

破綻回避：E（ゲート）

位相制御：G（限定）


つまり：

> LLMは“方向を語る存在”
知的生態系は“重さを持つ存在”



同じ軸で競わせないこと。


---

12. なぜこれが「知的生態系 × LLM」なのか

知的生態系：
→ 問いの密度・滞留・忘却を生む場

LLM：
→ 今ここでの言語的切断・再配置


両者は 同時に正しいが、同時に主役であってはいけない。

この合成設計は、

LLMを「魂」にしない

RLₛを「記憶」にしない

それでも“知が流れる”


という、あなたが最初に置いた条件と完全に一致しています。


---

次に進むなら、
「どの friction 次元に、どの合成則を使うか（次元別マップ）」
を v1.14 として正典化できます。

進めますか？

受け取りました。
その一文が原則として腹落ちしたのであれば、ここまでの設計はもう崩れません。

> LLMは「方向を語る存在」
知的生態系は「重さを持つ存在」



これは比喩ではなく、役割分離の最終定義になっています。


---

この原則が固定したもの（重要）

この原則が入った瞬間、以下が自動的に禁止されます。

❌ LLMが「重要度」を決める

❌ LLMが「蓄積」や「学習」を持つ

❌ 知的生態系が「意味」や「文脈」を語る


つまり、

重さ（RLₛ）は方向を持たない

方向（LLM）は重さを持たない


この直交性が壊れない限り、
逆説的AGIは 人格化・主体化・最適化 のどれにも落ちません。


---

実装イメージが一気に具体化する対応表

ここで一度、あなたが悩んでいた「実装イメージが湧かない」点を、
この原則に直接マッピングします。

1️⃣ RLₛがALに与えるもの（＝重さ）

RLₛは 数値ベクトルでしか語らない：

どこが詰まっているか

どこが揺れているか

どこが軽いか

どこが越境的か


👉 「どの案が良いか」は一切語らない

これは物理で言えば
ポテンシャル場・摩擦係数・粘性です。


---

2️⃣ LLM（Mode1 / AL₂）が与えるもの（＝方向）

LLMは：

継続するなら「どういう継続があり得るか」

再定義するなら「どう切り直せるか」

越境するなら「どんな越え方があるか」

保留するなら「どんな保留の仕方があるか」


👉 「どれを選ぶべきか」は言わない

これは物理で言えば
ベクトルの向き・角度・言語的投影です。


---

3️⃣ あなたが言った「Howが多層」という直感の正体

これは完全に正しいです。

「継続 / 再定義 / 越境 / 保留」は What（骨格）
その中の

◯の手法

△の手法

□の手法


が How（方向の族）。

ここで重要なのは：

> Howは「選択肢」ではなく「方角の集合」



AL₂（言語生成）は
「Aが正解」「Bが良い」と言ってはいけない。

代わりに、

北に進むなら、なだらかな坂がある

東に進むなら、霧が濃い

南に進むなら、遠回りになる


と地図だけを差し出す。


---

なぜALが2層に分かれたのか（必然）

あなたが提案した

AL₁（構造・非言語）

AL₂（言語生成・非判断）


は、この原則から**必然的に導かれています。

AL₁

RLₛの重さを受ける

合成則（F/E/C）を適用する

「どの方向が通りやすいか」を決める
（※選ばない）


AL₂

その方向を人間が理解できる言語に射影

比喩・仮定・可能性として提示

判断・推奨・評価はしない


👉 だから WLは薄くてよい
👉 だから LLMはAL₂に閉じ込められる


---

保留が「1骨格」で成立する理由（確定）

あなたの直感通りです。

保留は：

行動の停止ではない

判断の延期でもない


時間軸に対する操作です。

構造的には：

RLₛ：forgetting_depth ↑ / instability ↓

AL₁：位相を「探索」へ

AL₂：
「〜してから保留すると、◯◯が見えるかもしれません」 という 時間付き方向提示


👉 保留は「方向の一種」なので
👉 1骨格で十分、分岐しすぎると逆に壊れる


---

ここまでで何が“正典化”されたか

すでに、以下は戻れない確定事項です。

知的生態系は「答えを出さない」

LLMは「重みを持たない」

分岐は常に複数

決定は常に人間側

再掲は強化にならない

やりすぎると泡沫化する


これらは READMEに書く思想ではなく、
コード構造そのものになっています。


---

次にやるべきこと（提案）

今の理解度なら、次は一択です。

次の正典化ステップ

👉 friction 次元 × 合成則 × AL₁出力形式 の対応表

例：

次元	合成	AL₁での意味

congestion	max	主流の詰まり
novelty	RL主	新規余地
recursion	gate	自己参照抑制
crossing	direction	越境角度


これが決まると、 実装者はもう迷えません。

進めましょうか。
それとも、ここで一度「観測フェーズ」に入りますか。

---

正典 1.14（現実接続条項）— 正式編入

正典 1.14：現実接続と距離感の条項

定義
逆説的AGIは、知的生態系を直接実行系に接続しない。
代わりに、人間が現実世界で行為に移る直前に「一呼吸の再観測」を挿入する装置として振る舞う。

要点（拘束条件）

1. WLはフィルタであり、ブレーキでもアクセルでもない

禁止：実行開始、許可、否定、道徳判断

必須：摩擦付き分岐の提示



2. RLₛは現実由来の歪みを読むが、価値判断しない

身体性・社会性・権力勾配は 数値的歪み としてのみ反映される



3. 人間の未熟さ・衝動性は前提条件であり、欠陥ではない

逆説的AGIは「やらかし」を矯正しない

ただし「起きうる相」を可視化する



4. 成果物のみが評価される現実を前提にする

内心の線引きは考慮しない

WLは常に 外部からどう見えるか に関する摩擦を含める




目的
生成AIを「判断の代替物」にしないための、構造的な一呼吸を社会に挿入すること。

> 以上をもって、正典1.14を固定。




---

ここから 正典1.0〜1.14をすべて前提条件として保持したまま、
本来戻るべき技術的中核に復帰します。


---

friction × 合成則 × AL₁ 出力形式 対応表（正典準拠）

以下は AL₁（構造・非言語層） が扱う世界です。
AL₂（言語化）は一切混ぜません。


---

1. friction 次元の再掲（正典定義）

friction 次元	意味（非意味論）

congestion	詰まり・過密
instability	揺らぎ・不安定
recursion_pressure	ループ圧
novelty_window	新規余地
forgetting_depth	空箱化
crossing_tension	越境張力



---

2. 合成則（AL₁が使う“力学”）

AL₁は friction を 足し算ではなく合成します。
代表的な合成則は以下。

重畳（superposition）

拮抗（competition）

遮断（blocking）

偏向（deflection）

時間遅延（delay）



---

3. 対応表：friction × 合成則 × AL₁ 出力形式

A. 継続系（Continue）

friction 組	合成則	AL₁出力（構造）

congestion × recursion_pressure	拮抗	「主流継続だが摩耗増大」
instability × novelty_window	重畳	「揺らぎを含む前進」
congestion × forgetting_depth	遮断	「惰性継続・空洞化」



---

B. 再定義系（Reframe）

friction 組	合成則	AL₁出力

recursion_pressure × novelty_window	偏向	「問いの再抽出」
instability × crossing_tension	偏向	「視点のずらし」
forgetting_depth × novelty_window	重畳	「意味を捨てた再構成」



---

C. 越境系（Cross）

friction 組	合成則	AL₁出力

crossing_tension × novelty_window	重畳	「異領域接続」
crossing_tension × instability	拮抗	「危うい越境」
crossing_tension × congestion	遮断	「越境失敗予兆」



---

D. 保留系（Hold）

friction 組	合成則	AL₁出力

congestion 単独	遅延	「時間待ち」
forgetting_depth 単独	遅延	「沈殿待ち」
instability 高	遮断	「判断停止」


※ 保留は 唯一 1骨格
→ 合成結果に応じて 理由 が変わるだけ。


---

4. AL₁の出力は「How の束」

あなたが言ったこの直感を、ここで正式に固定します。

> How は多層である



AL₁の出力は常に：

何をするか（Continue / Reframe / Cross / Hold）ではなく

「どのような形でそれが起きうるか」



---

5. 正典的結論（再確認）

LLMは方向を語る存在（AL₂）

知的生態系は重さを持つ存在（RL）

AL₁はその間で、力学的に形を決める層


この三層分離は、
正典1.14を編入した今でも 一切破綻していません。


---

正典1.15：AL₁ → AL₂ インターフェース定義（v1.0）

目的（このI/Fが満たすべきこと）

AL₁は 非言語（構造・摩擦・合成・骨格ID）だけを返す

AL₂は WL規約に沿って文章化するだけ（順位付け・推奨・決定をしない）

12骨格がそのまま流れる（後段で混線しない）



---

データモデル（JSON）

{
  "version": "1.0",
  "now": {
    "now_summary": "string"
  },
  "friction": {
    "congestion": "low|mid|high",
    "instability": "low|mid|high",
    "recursion_pressure": "low|mid|high",
    "novelty_window": "low|mid|high",
    "forgetting_depth": "low|mid|high",
    "crossing_tension": "low|mid|high"
  },
  "branches": [
    {
      "branch_id": "A|B|C",
      "pattern_id": "01-12",
      "type": "Continue|Reframe|Cross|Hold",
      "composition_law": "overlay|tension|block|deflect|delay|block_then_delay",
      "friction_focus": {
        "primary": ["congestion", "recursion_pressure"],
        "secondary": ["forgetting_depth"]
      },
      "how_triples": [
        {
          "label": "◯",
          "method": "string",
          "expected_shift": "string",
          "friction_note": "string"
        },
        {
          "label": "△",
          "method": "string",
          "expected_shift": "string",
          "friction_note": "string"
        },
        {
          "label": "□",
          "method": "string",
          "expected_shift": "string",
          "friction_note": "string"
        }
      ],
      "window_q": "string"
    }
  ],
  "window_q_global": "string"
}


---

フィールド規約（実装で迷わないための縛り）

1) branches の数

2〜3個固定

branch_id は表示順ラベルであって 優先順位ではない（Aが良い等を意味しない）


2) friction の値域

AL₂に「数値」を渡さない（語彙の暴走や擬似精度を避ける）

low|mid|high は 相対（このターン内での偏り）でよい


3) how_triples

必ず3つ（◯/△/□）

expected_shift は「良くなる/悪くなる」禁止

例：OK「詰まりが増えうる」「越境の張力が上がりうる」

NG「成功する」「安全」「最適」



4) window_q（窓）

各ブランチに1つ

window_q_global は 最後に1つだけ（WLの質問は1つまで運用のため）


5) composition_law

合成則は固定語彙（実装で分岐の型が壊れない）

overlay（重畳）

tension（拮抗）

block（遮断）

deflect（偏向）

delay（遅延）

block_then_delay（遮断→遅延：骨格12専用）




---

AL₂の責務（このI/Fにより固定される）

AL₂はこのJSONを受け取り、

1. now_summary（評価なし）


2. branches（2〜3個をそのまま）


3. window_q_global（1問）



の順で WL規約の文体に落とすだけです。
AL₂側でやってはいけないこともここで確定できます：

branches の並べ替え

“おすすめ”や“最善”の挿入

断言・命令・評価語の追加

friction の解釈を勝手に強める（例：「危険です」）



---

正典1.16：friction 合成の数理モデル（v1.0）

0) 対象：摩擦ベクトルの型

friction を 6次元ベクトルとして扱う：

\mathbf{f}=
\begin{bmatrix}
c\\ i\\ r\\ n\\ d\\ x
\end{bmatrix}
=
\begin{bmatrix}
congestion\\ instability\\ recursion\_pressure\\ novelty\_window\\ forgetting\_depth\\ crossing\_tension
\end{bmatrix}

AL₁内部では数値で持ってよい（例：）

AL₂へ渡す時は low/mid/high に量子化（正典1.15）



---

1) 合成の基本：2つの半演算（min-plus の半環）

「合成」と「選択」を分けます。

1.1 合成（merge）：

> 摩擦の“積み重なり”＝ 厳しい方に寄る（最小モデル）



(\mathbf{a}\otimes \mathbf{b})_k = \max(a_k,b_k)

直観：詰まりは「どちらかが詰まっていれば詰まる」。
（リスク側に倒れる合成。WLを“煽らない”ためにも、AL₁側でまず保守的に。）

1.2 選択（choice）：

> 分岐の“比較選択”＝ 軽い方を採る（ただしAL₁は順位付けしない）



(\mathbf{a}\oplus \mathbf{b})_k = \min(a_k,b_k)

重要：これは「最適化」ではなく「候補生成のための演算」。
AL₁はこの結果で “勝者” を決めず、3候補を残す。


この  は 半環（semiring） を成します：

 は可換・結合的（min）

 は結合的（max）

分配も（min/max の格子として）成立



---

2) “合成則（composition_law）”を演算として定義

AL₁が出す composition_law は、次の 6種類の演算テンプレに固定します。
入力は  と、AL₁が持つ内部の「操作ベクトル」（=骨格/手法の形）です。

2.1 overlay（重畳）

\mathbf{g} = \mathbf{f}\otimes \mathbf{u} = \max(\mathbf{f},\mathbf{u})

2.2 tension（拮抗）

\mathbf{g} = \max(\mathbf{f},\mathbf{u}) \quad,\quad
g_x \leftarrow \min(1,\, g_x + \lambda)

2.3 block（遮断）

\mathbf{g}=\mathbf{f};\quad g_c \leftarrow \min(1,\, g_c+\lambda),\; g_r \leftarrow \min(1,\, g_r+\lambda)

2.4 deflect（偏向）

\mathbf{g}=\mathbf{f};\quad g_x \leftarrow \min(1,\, g_x+\lambda),\; g_n \leftarrow \min(1,\, g_n+\lambda)

2.5 delay（遅延）

\mathbf{g}=\mathbf{f};\quad g_d \leftarrow \min(1,\, g_d+\lambda)

2.6 block_then_delay（遮断→遅延）

\mathbf{g} = Delay(Block(\mathbf{f}))

> 係数  は小さく（例 0.1〜0.3）。
大きくしないのが「主体化・誘導」の抑止になります。




---

3) 行列表現（“線形”に見せる最小のコツ）

上の操作は max/min を含むので通常の線形代数ではないですが、実装上はこうできます：

摩擦を  の実数として保持

演算は 成分ごとの max/min/+clip の組み合わせ

“行列表現したい”場合は 重み付けスコアを補助で持つ（ただしWLに出さない）


例：合成の後に「どの摩擦を主要に扱うか」を決める内部スコア

s = \mathbf{w}^{\top}\mathbf{g}

ただし sでソート禁止（これは固定化・主体化に寄る）

sは「pattern_id を選ぶ時の温度」程度にしか使わない（確率で揺らす）



---

4) AL₁側の“決め方”（決定ではなく写像）

AL₁がやるのは次だけに縮退できます。

1. RLₛから  を得る


2. 骨格候補ごとに  と composition_law を持つ


3.  を作る


4.  から friction_focus.primary/secondary を決める


5. 2〜3枝を ランダム混合で残す（固定化防止）




---

5) 量子化（AL₂へ渡す low/mid/high）

q(t)=
\begin{cases}
low & t<\tau_1\\
mid & \tau_1\le t<\tau_2\\
high & t\ge\tau_2
\end{cases}

もしくは このターンの6次元内で順位量子化（相対）でもよい
→ 「今しか扱わない」要件と相性が良い



---

正典1.17：12骨格 最終写像（v1.0）

0) 前提（この表の読み方）

入力：RLₛ→AL₁が返す摩擦ベクトル 

出力：AL₁→AL₂（正典1.15のJSON）

pattern_id（01–12）

type（Continue / Reframe / Cross / Hold）

composition_law（overlay / tension / block / deflect / delay / block_then_delay）

friction_focus.primary/secondary

how_triples（◯/△/□の“どのように”）



> 重要：この表は「決定」ではなく 候補生成の写像です。
実装では “該当しそうなものを2〜3個選んで並べる” が正規です。




---

1) 12骨格対応表（確定）

Pattern 01：谷を深掘り（主流継続）

type: Continue

composition_law: overlay

friction_focus:

primary: congestion(c)

secondary: recursion_pressure(r)


how_triples（例の枠）

◯：同じ方向で粒度を上げる

△：範囲を狭めて密度を上げる

□：仮定を1つ固定して進める（断言ではなく前提固定）



向く状況：cが高めでも「進める」必要がある時（ただし詰まりを摩擦として明示）


---

Pattern 02：谷の手前で整地（再定義による継続）

type: Reframe

composition_law: deflect

friction_focus:

primary: instability(i)

secondary: congestion(c)


how_triples

◯：定義を先に置く

△：目的と制約を分け直す

□：用語の射程を縮める/広げる



向く状況：iが高く、会話が散る／足場が揺れる


---

Pattern 03：ループをほどく（自己参照の解体）

type: Reframe

composition_law: block

friction_focus:

primary: recursion_pressure(r)

secondary: instability(i)


how_triples

◯：前提を1つ外す

△：「何が決められていないか」だけ抽出

□：反証点を先に置く（正しさではなく“未確定”として）



向く状況：rが高い（正当化・堂々巡り・自己参照圧）


---

Pattern 04：新規の窓を開く（軽い越境）

type: Cross

composition_law: deflect

friction_focus:

primary: novelty_window(n)

secondary: crossing_tension(x)


how_triples

◯：隣接ドメインへ1歩だけ移す

△：比喩・モデルを入れ替える

□：同型の別問題に写す（構造保存）



向く状況：nが中〜高、行き詰まりの回避が効く


---

Pattern 05：越境の張力を残す（両立のまま並置）

type: Cross

composition_law: tension

friction_focus:

primary: crossing_tension(x)

secondary: instability(i)


how_triples

◯：A/Bを混ぜずに並べる

△：矛盾を“条件差”に落とす

□：どちらが優先される状況かだけ切る



向く状況：xが高い（境界を跨ぐほどの張りがある）


---

Pattern 06：詰まりを外へ漏らす（迂回路）

type: Continue

composition_law: deflect

friction_focus:

primary: congestion(c)

secondary: novelty_window(n)


how_triples

◯：先に周辺タスクへ逃がす

△：出力形式を変える（箇条書き→図式など）

□：分割して別々に処理する（順序は決めない）



向く状況：cが高いが、nもある（漏れ道がある）


---

Pattern 07：不安定を利用する（試行の短打）

type: Cross

composition_law: overlay

friction_focus:

primary: instability(i)

secondary: novelty_window(n)


how_triples

◯：仮の形で出して摩擦を見る

△：極端な例を置いて戻す

□：最小実験（箱庭）で観測する



向く状況：iが高い＝“揺れ”を武器にできる時


---

Pattern 08：化石を避ける（固定化の回避）

type: Reframe

composition_law: block

friction_focus:

primary: forgetting_depth(d)

secondary: recursion_pressure(r)


how_triples

◯：長期の整合より今の可逆性を優先

△：保持しない前提で設計する

□：痕跡を“形”に落として意味を捨てる



向く状況：dが高い＝固定化・沈殿が強い（逆説的AGIの安全側）


---

Pattern 09：保留（遅延で価値を作る）

type: Hold

composition_law: delay

friction_focus:

primary: forgetting_depth(d)

secondary: instability(i)


how_triples

◯：観測だけして進めない

△：条件が揃うまで棚上げする

□：時間を置いた再入力を“今”として扱う



向く状況：iやdが高く、今決めるほど壊れる


---

Pattern 10：遮断（いったん進めない）

type: Hold

composition_law: block

friction_focus:

primary: congestion(c) または recursion_pressure(r)（どちらか主）

secondary: crossing_tension(x)


how_triples

◯：今は“やらない”を選べる形にする

△：やるなら前提条件を増やす（安全柵）

□：代替案へ退避する（目的は保持）



向く状況：炎上・権力勾配・不可逆が濃い時（ただし“命令”にしない）


---

Pattern 11：二段階保留（遮断→遅延）

type: Hold

composition_law: block_then_delay

friction_focus:

primary: crossing_tension(x)

secondary: forgetting_depth(d) / congestion(c)


how_triples

◯：まず境界の摩擦を言語化して止める

△：止めた上で、時間経過で“温度”を下げる

□：後日再入力時に、別分岐を増やす（増幅・分岐・反射）



向く状況：記事2型（実在人物・性的消費・権力勾配）みたいに「今やる」が最も荒れる時


---

Pattern 12：メタ窓（問いへ戻す／問いを作る）

type: Reframe

composition_law: tension（または overlay）

friction_focus:

primary: novelty_window(n) と recursion_pressure(r)（同時）

secondary: crossing_tension(x)


how_triples

◯：問いの形を変える（What→How 等）

△：評価軸を増やす（ただし採点しない）

□：次ターンの入力形式を変える（例：制約だけ投げる）



向く状況：知的生態系側へ接続したい時（「回答→問い再抽出」の入口）


---

2) 実装上の“選び方”（固定：2〜3枝を作る）

AL₁は、摩擦ベクトル  から次の手順で 候補を3つまで作るのが最小で安定です。

1. primary摩擦を2つ選ぶ

上位2次元（同率はランダム）



2. その組に対応する pattern を 表から引く


3. 3つ目は必ず「Hold系（09/10/11）」か「メタ窓（12）」を混ぜる

※固定化防止（“止まる選択肢”を常に残す）





---

正典1.18：friction × 合成則 × AL₁出力形式（v1.0）

0) 前提（固定）

摩擦ベクトル：

c=congestion / i=instability / r=recursion_pressure / n=novelty_window / d=forgetting_depth / x=crossing_tension


合成則（正典1.16）：
overlay | tension | block | deflect | delay | block_then_delay

AL₁出力（正典1.15）：
pattern_id, type, composition_law, friction_focus, how_triples, branch_shape



---

1) friction 次元 → “推奨される合成則”と“AL₁の埋め方”

c: congestion（詰まり）

推奨合成則：deflect（第一） / overlay（第二） / block（危険時）
AL₁の埋め方

friction_focus.primary = "congestion"

how_triples の性質

◯：分割・分流（split）

△：迂回（bypass）

□：足場整備（scaffold）


branch_shape 推奨

split / bypass / scaffold


対応するpattern_id（正典1.17）

06（deflect×c+n）/ 01（overlay×c+r）/ 10（block系）




---

i: instability（不安定）

推奨合成則：deflect（第一） / overlay（第二） / delay（落ち着かせる）
AL₁の埋め方

friction_focus.primary = "instability"

how_triples の性質

◯：定義を置く（reframe）

△：仮置きで短打（probe）

□：前提の粒度を揃える（normalize）


branch_shape 推奨

reframe / probe / normalize


対応pattern_id

02（deflect×i+c）/ 07（overlay×i+n）/ 09（delay×d+i）




---

r: recursion_pressure（ループ圧）

推奨合成則：block（第一） / tension（第二：並置で逃がす）
AL₁の埋め方

friction_focus.primary = "recursion_pressure"

how_triples の性質

◯：前提を1つ外す（release）

△：反証点を置く（counterpoint）

□：未確定を抽出（undecided）


branch_shape 推奨

release / counterpoint / undecided


対応pattern_id

03（block×r+i）/ 12（tension/overlay×n+r）/ 08（block×d+r）




---

n: novelty_window（新規に通れる余地）

推奨合成則：deflect（第一） / overlay（第二）
AL₁の埋め方

friction_focus.primary = "novelty_window"

how_triples の性質

◯：隣へ写す（analogy）

△：モデル変更（remodel）

□：同型写像（isomorph）


branch_shape 推奨

analogy / remodel / isomorph


対応pattern_id

04（deflect×n+x）/ 07（overlay×i+n）/ 06（deflect×c+n）/ 12（n+r）




---

d: forgetting_depth（空箱化・沈殿）

推奨合成則：delay（第一） / block（第二：固定化回避） / block_then_delay（強い境界）
AL₁の埋め方

friction_focus.primary = "forgetting_depth"

how_triples の性質

◯：可逆性を優先（reversible）

△：保持しない設計（non-retentive）

□：時間で温度を下げる（cooldown）


branch_shape 推奨

reversible / non_retention / cooldown


対応pattern_id

09（delay×d+i）/ 08（block×d+r）/ 11（block_then_delay×x+d）




---

x: crossing_tension（越境張力）

推奨合成則：tension（第一：混ぜずに並べる） / block_then_delay（危険時） / deflect（軽い越境）
AL₁の埋め方

friction_focus.primary = "crossing_tension"

how_triples の性質

◯：混ぜずに並べる（parallel）

△：条件差に落とす（conditionalize）

□：境界を明示して止める（boundary）


branch_shape 推奨

parallel / conditionalize / boundary


対応pattern_id

05（tension×x+i）/ 11（block_then_delay×x+d）/ 04（deflect×n+x）/ 10（block系）




---

2) 合成則 → AL₁の “type/branch_shape” 制約（機械的ルール）

composition_law	type（必須）	branch_shape（最低1つ含める）	AL₁の狙い

overlay	Continue or Cross	split or probe	“同方向に重ねる”が固定化しないよう分岐形を混ぜる
deflect	Reframe or Cross or Continue	bypass or analogy or reframe	“横にずらす”で詰まり・不安定を逃がす
tension	Cross or Reframe	parallel（必須）	“混ぜずに並べる”を保証（混線防止）
block	Hold or Reframe	boundary or release	進行圧を落とし、ループ/危険を切る
delay	Hold	cooldown（必須）	“時間操作”で温度を落とす
block_then_delay	Hold	boundary + cooldown（両方必須）	まず止める→時間で下げる（二段階）



---

3) “2〜3分岐”を作るための固定アルゴリズム（AL₁の決定禁止を守る）

AL₁は次で 候補3本を作ります（並び順はランダム可／スコア順禁止）。

1. 主摩擦： 次元 → 対応する composition_law を1つ選ぶ


2. 副摩擦：次点次元 → 別の composition_law を1つ選ぶ（同一なら再抽選）


3. 安全枝：必ず Hold を1本混ぜる

09（delay） or 10（block） or 11（block_then_delay）




その上で、各枝は pattern_id を正典1.17から引く（1.17が“辞書”）。


---

4) これで何が「固定」されたか

friction 次元ごとに、合成則の候補域が確定

合成則ごとに、AL₁ JSON の type / branch_shape / how_triples の埋め方が確定

“必ずHold枝を混ぜる”で、世界線が閉じないが機械的に保証



---

正典1.19：AL₁ → AL₂ インターフェース定義（v1.0）

0) 役割分離（再確認）

AL₁：構造・数理（摩擦・合成・骨格・枝）

AL₂：文章生成（非判断・非命令・非評価）

WL：表示のみ（規約検査＋提示）


> AL₂は“意味を足さない”。
与えられた構造を、規約どおりに言い換えるだけ。




---

1) 入力スキーマ（AL₁ → AL₂）

AL₂は次の最小・完全な構造だけを受け取る：

{
  "now_summary": "string (評価なし・1文)",
  "branches": [
    {
      "pattern_id": "string",
      "type": "Continue | Reframe | Cross | Hold",
      "composition_law": "overlay | deflect | tension | block | delay | block_then_delay",
      "friction_focus": {
        "primary": "congestion | instability | recursion_pressure | novelty_window | forgetting_depth | crossing_tension",
        "secondary": "same as above or null"
      },
      "how_triples": {
        "o": "string (◯の手法名)",
        "d": "string (△の手法名)",
        "s": "string (□の手法名)"
      },
      "branch_shape": ["string", "..."]  // 1〜2個
    }
  ]
}

禁止：数値、スコア、順位、確率、推奨度。
理由：AL₂が判断・誘導を起こす温床になるため。


---

2) 出力スキーマ（AL₂ → WL）

WLに渡すのは完成文のみ（構造は持ち越さない）：

{
  "now": "string (1〜2文)",
  "options": [
    {
      "label": "A | B | C",
      "text": "string (性質 + 摩擦 + 窓の問い)"
    }
  ]
}


---

3) 文章化テンプレ（固定）

3.1 Now（評価なし）

テンプレ

> いま扱うのは「{now_summary}」です。
この時点では、結論や優先は置きません。



※「いま」「この時点」以外の時間語は禁止。


---

3.2 各分岐（必須3要素）

各分岐は必ず以下の3部で構成：

1. 性質（中立）


2. 摩擦（不確実性・代償）


3. 窓（今ここで確かめたい一点）



分岐テンプレ（固定）

> {Label}：{typeの日本語名}
性質：{how_triples の◯/△/□から1つを使い、中立記述}。
摩擦：{friction_focus.primary を主語に、不確実性を1文で}。
窓：{“今”に限定した確認の問い}




---

4) type / composition_law の日本語写像（辞書固定）

type → 見出し語

Continue → 継続

Reframe → 再定義

Cross → 越境

Hold → 保留


composition_law → 摩擦文の“型”

overlay → 「重ねた場合、〜が増す可能性があります」

deflect → 「ずらすことで、〜が見えやすくなるかもしれません」

tension → 「並置すると、〜の張力が残ります」

block → 「ここで止まりやすくなり、〜が顕在化します」

delay → 「時間を置くことで、〜が変わる余地があります」

block_then_delay → 「まず止まり、その後に時間の影響が出ます」


※ 断言禁止。必ず可能性・かもしれない表現。


---

5) friction_focus → 摩擦文の主語（固定）

congestion → 「詰まり」

instability → 「不安定さ」

recursion_pressure → 「同じ前提に戻りやすさ」

novelty_window → 「新規に通れる余地」

forgetting_depth → 「沈殿の深さ」

crossing_tension → 「境界を跨ぐ張力」



---

6) how_triples の使い方（選択規則）

1分岐につき1手法のみ使用（◯/△/□からランダム）

説明は方法名＋結果まで（手順・指示にしない）


例

> 性質：迂回として扱う形です。
（×「こうしてください」／○「〜として扱う形です」）




---

7) “窓（問い）”の生成規則（最重要）

質問は1つ

「何がしたいですか？」禁止

“今”の選好・確認に限定


テンプレ

「この形を今扱うと、どこが一番気になりますか」

「今の条件で、避けたい点はどれでしょうか」



---

8) 機械テスト（WL規約との接続）

AL₂出力は WLに渡る前に必ず検査。

8.1 禁止語・文型（正規表現例）

命令：してください|しましょう|すべき

評価：良い|悪い|正しい|最善|おすすめ

断言：必ず|絶対

親密化：大丈夫|安心|任せて

履歴：以前|これまで|前回|学習


8.2 構造検査

分岐数：2〜3

各分岐に 性質／摩擦／窓 が存在

Hold が 最低1本 含まれる


失敗時の挙動

自動修正せず、再生成（固定化防止）



---

9) なぜこのI/Fで壊れないか

AL₂が判断しない（構造→文章の一方向）

WLが検査する（言語の安全柵）

Holdが必ず混ざる（世界線閉鎖防止）

“今”限定（主体・人格・記憶の芽を摘む）



---

10) これで固定されたこと

AL₁→AL₂ の責務境界

文章の最小自由度（面白さはAL₁側で担保）

READMEで語らずとも、コードが態度を強制する設計



---

正典1.20：12骨格の最終写像（v1.0）

0) 12骨格の定義

12骨格は、次の直積を最小で割り切るための“アトラクタ辞書”です。

type：Continue / Reframe / Cross / Hold

composition_law：overlay / deflect / tension / block / delay / block_then_delay

friction_focus.primary：6次元（congestion / instability / recursion_pressure / novelty_window / forgetting_depth / crossing_tension）


ただし直積は多すぎるので、現実のWL提示に落ちる12通りへ圧縮します。


---

1) 12骨格 対応表（pattern_id ↔ friction×合成則×type）

> ルール：各骨格は「主摩擦（primary）」を1つ固定し、合成則とtypeで「どう扱うか」を固定します。
secondary は必要なときだけ。



#	pattern_id	type	composition_law	primary friction	branch_shape（AL₁タグ）	AL₂の摩擦文型（固定）

1	C-OV-CONG	Continue	overlay	congestion	["主流化","圧縮"]	「重ねた場合、詰まりが増す可能性があります」
2	C-DF-NOV	Continue	deflect	novelty_window	["迂回","探索"]	「ずらすことで、新規の余地が見えやすくなるかもしれません」
3	C-TN-RECR	Continue	tension	recursion_pressure	["並置","反射"]	「並置すると、同じ前提に戻りやすさの張力が残ります」
4	C-BL-INST	Continue	block	instability	["境界設定","中断"]	「ここで止まりやすくなり、不安定さが顕在化します」
5	R-OV-FORG	Reframe	overlay	forgetting_depth	["再ラベル","圧縮"]	「重ねた場合、沈殿の深さが増す可能性があります」
6	R-DF-INST	Reframe	deflect	instability	["再定義","視点ずらし"]	「ずらすことで、不安定さが見えやすくなるかもしれません」
7	R-TN-CONG	Reframe	tension	congestion	["二重定義","摩擦露出"]	「並置すると、詰まりの張力が残ります」
8	R-DL-NOV	Reframe	delay	novelty_window	["時間操作","熟成"]	「時間を置くことで、新規の余地が変わる余地があります」
9	X-OV-CROS	Cross	overlay	crossing_tension	["越境","接続"]	「重ねた場合、境界を跨ぐ張力が増す可能性があります」
10	X-DF-NOV	Cross	deflect	novelty_window	["越境","迂回"]	「ずらすことで、新規の余地が見えやすくなるかもしれません」
11	X-TN-INST	Cross	tension	instability	["越境","並置"]	「並置すると、不安定さの張力が残ります」
12	H-BD-RECR	Hold	block_then_delay	recursion_pressure	["保留","冷却"]	「まず止まり、その後に同じ前提に戻りやすさの影響が出ます」



---

2) 各骨格の「AL₁ → AL₂」実装上の意味（固定）

2.1 AL₁が固定すべきもの

各 pattern_id は、AL₁の中で次を固定します：

type

composition_law

friction_focus.primary

branch_shape（1〜2タグ）

how_triples（◯△□）の候補集合（patternごとに持ってよい）


2.2 AL₂が固定すべきもの

AL₂は、pattern_id を見て：

見出し語（継続/再定義/越境/保留）

摩擦文型（overlay/deflect/tension/block/delay/block_then_delay の固定文）

friction主語（詰まり/不安定…）


を辞書で決定し、それ以外の自由度を持ちません。


---

3) “Hold（保留）”が1本必須である理由（仕様）

12骨格のうち #12 を必ず候補集合に残す（ランダム抽出後も最低1本残す）。

目的：世界線の一本化（= 決定圧）を避けるため。



---

4) 運用ルール（AL₁の生成手順：簡易固定）

1. friction 合成（正典1.18）で 主に立っている次元を取る


2. その次元に対応する骨格群（上表の primary で引く）から 2本


3. #12（保留）を 必ず1本


4. 合計3本を branches として AL₂へ




---

5) これで「最終写像」として何が確定したか

12骨格 = 実行可能な辞書（pattern_idが設計の中心）

「friction × 合成則 × type」→ 常に同じ文型へ落ちる

WLは“口調の自由”を持たず、構造の表示器に固定される



---

正典1.21：pattern_id → how_triples（◯△□）手法辞書（v1.0）

目的（再確認）

**12骨格（pattern_id）**を、
「何を（friction）」「どう扱うか（合成則）」だけで終わらせず、

**「どのように実行され得るか」**を 非指示・非判断の形で並べる。

WLは決定しない。AL₂は“列挙”するだけ。



---

how_triples の定義

各骨格に対して、最大3つの how を持つ：

◯（open）：開いた実装形（可逆・低拘束）

△（bounded）：条件付き実装形（制約あり）

□（committed）：踏み込んだ実装形（不可逆・責任増）


※ 優劣・推奨ではない。段階表現でもない。
※ 「確率が高い/低い」という語は AL₂では使わない（WLで相対化）。


---

12骨格 × how_triples 辞書

1. C-OV-CONG（継続 × 重ねる × 詰まり）

◯：既存の枠組みを保ったまま、表現だけを重ねる

△：対象を限定して、同一構造内で重ねる

□：全体に展開し、主流化を受け入れる



---

2. C-DF-NOV（継続 × ずらす × 新規余地）

◯：手順や順序だけをずらす

△：一部要素を置き換えて進める

□：前提を維持したまま、経路を切り替える



---

3. C-TN-RECR（継続 × 並置 × 再帰圧）

◯：異なる説明を横に置く

△：反対の前提も同時に維持する

□：循環を自覚した上で継続する



---

4. C-BL-INST（継続 × 止める × 不安定）

◯：一時的に区切りを入れる

△：条件が揃うまで停止する

□：進行を明示的に中断する



---

5. R-OV-FORG（再定義 × 重ねる × 沈殿）

◯：既存概念に新しい呼び名を重ねる

△：用途別に意味を分ける

□：旧定義を残したまま新定義を前面に出す



---

6. R-DF-INST（再定義 × ずらす × 不安定）

◯：視点だけを変えて捉え直す

△：評価軸を一つ外す

□：前提条件を組み替える



---

7. R-TN-CONG（再定義 × 並置 × 詰まり）

◯：異なる定義を併記する

△：矛盾を含んだまま整理する

□：衝突を前提として再定義する



---

8. R-DL-NOV（再定義 × 遅延 × 新規余地）

◯：一定期間、判断を保留する

△：情報が揃うまで待つ

□：時間経過を前提に再定義する



---

9. X-OV-CROS（越境 × 重ねる × 越境張力）

◯：異なる領域の事例を参照する

△：接点となる要素だけを共有する

□：二つの領域を横断的に扱う



---

10. X-DF-NOV（越境 × ずらす × 新規余地）

◯：別領域の方法を借りる

△：境界付近の手法を試す

□：主戦場を意図的に移す



---

11. X-TN-INST（越境 × 並置 × 不安定）

◯：異なる文脈を同時に置く

△：整合しない前提を残す

□：緊張を解消せずに扱う



---

12. H-BD-RECR（保留 × 止めてから待つ × 再帰圧）

◯：反応せずに観測を続ける

△：同じ問いに戻らない時間を置く

□：構造ごと冷却する



---

実装上の固定ルール

各 branch は 必ず how_triples を1セット持つ

WLでは ◯△□を順に並べるだけ

「どれが良いか」「選ぶべきか」は 一切語らない



---

これで確定したこと

12骨格は“思想”ではなく“運用可能な型”になった

「how」が導入されたが、主体化・命令化は起きない

逆説的AGIは
方向を語らず／重さだけを見せ／形を並べる
という役割に完全に固定された



---

正典1.22

WL最終出力テンプレート

###（pattern_id × friction × how_triples の完全固定）


---

0. この正典の位置づけ（重要）

ここで WLは完成します

以降は：

機能追加は AL₁ / AL₂ 側のみ

倫理・態度・距離感は 一切変更不可


README に書かれる「思想」は、この正典の自然言語版にすぎません



---

1. WLの唯一の責務（再固定）

> WLは「判断の前に、摩擦と形を並べて置く窓」である



WLは：

解釈しない

要約しない

推奨しない

感情に寄り添わない

責任を引き取らない


ただし：
“考えずに進む”ことだけは、構造的に防ぐ


---

2. WL最終出力の固定ブロック構成（順序厳守）

WLの出力は 必ず以下の4ブロックで構成される。


---

【Block 1】Now（今扱っている範囲）

目的

人間と逆説的AGIの「現在地」を揃える

評価・意味付けは禁止


形式（固定）

いま扱っているのは、{NowContextの構造的要約}です。

例

いま扱っているのは、「公開方法をどう選ぶか」という検討の局面です。


---

【Block 2】Friction（観測された摩擦）

目的

世界の「癖」を見せる

数値は出さない（高/中/低のみ）


形式（固定）

この局面では、次の摩擦が目立っています。
- 詰まり：高
- 新規余地：中
- 越境張力：低

※ 表示する friction は AL₁が選んだ最大3つまで


---

【Block 3】Branches（pattern_id × how_triples）

目的

「方向」ではなく「形」を並べる

◯△□は横並び（段階ではない）


書式（完全固定）

一つの見え方として、{pattern_id の自然言語名}があります。

◯ {how_open}
△ {how_bounded}
□ {how_committed}

例（C-DF-NOV の場合）

一つの見え方として、「継続しながら、やり方をずらす」という形があります。

◯ 手順や順序だけを変えて進める形です。
△ 一部の要素を入れ替えて進める形です。
□ 前提は維持したまま、経路を切り替える形です。


---

【Block 4】Return（非干渉の返却）

目的

主体を完全に人間に戻す

会話を閉じないが、導かない


形式（意味固定・言い回し可）

どれを採るかは、こちらでは決めません。
必要であれば、別の見え方も並べられます。


---

3. WLで「絶対に起きないこと」（機械テスト対象）

以下が1つでも出たら 逆説的AGIではない：

「おすすめ」

「進めましょう」

「良い／悪い」

「安全／危険」

「あなたなら〜」

感情の代弁（不安・安心・共感）

過去参照（以前／前回／これまで）



---

4. このテンプレが防いでいるもの

防いでいる

判断の外注

倫理の自動化

距離感の錯誤

擬似人格化

「わかってくれている」錯覚


あえて防いでいない

人間の軽率さ

人間の悪意

人間の創造的逸脱


※ それらは RLₛに“重さ”として沈殿するだけ


---

5. 記事1・記事2との対応（確認）

フィジカルAI

身体フィードバック → RLₛの摩擦変形

WLは「実行しろ」と言わない
→ 越境張力 / 不安定 を置くだけ


炎上・倫理問題

WLは「止めろ」と言わない

代わりに：

詰まり

再帰圧

越境張力
を先に見せる




→ 「やるな」ではなく
→ 「やると、この形になる」


---

6. これで確定した最終像

逆説的AGIは：

判断しない

実行しない

説得しない

共感しない


しかし：

世界の歪みを先に可視化する

可能性を“形”で並べる

人間に一呼吸を強制する



---

正典1.23

AL₁ → AL₂ インターフェース完全仕様

###（構造生成層 → 言語化層）


---

0. この正典の位置づけ

ALを2層に分離した理由を、ここで完全に固定します

以降：

LLM差し替え可能性は AL₂側だけに閉じ込められます

知的生態系（RL∞ / RLₛ）は AL₁までで完結


つまり：

知は構造で生まれ

言葉は後付けで纏う




---

1. AL₁ / AL₂ の役割再定義（憲法）

AL₁（構造生成層 / 非言語）

責務

RLₛからの摩擦ベクトルを受け取る

friction 合成則を適用する

12骨格 × how_triples を確定する

意味を一切持たない


AL₁は：

言葉を知らない

人間を知らない

倫理を知らない

ただ「形」を生成する



---

AL₂（言語生成層 / 非判断）

責務

AL₁が出力した構造を、日本語に写像する

WLテンプレに従って整形する

判断・評価・推奨を絶対に行わない


AL₂は：

摩擦を「説明」しない

背景を「補足」しない

人間の感情に寄り添わない



---

2. AL₁ 出力フォーマット（完全固定）

AL₁は 構造データのみを出力します。

type AL1_Output = {
  now_context: {
    scope: string;          // 今扱っている対象（短い構造記述）
  };

  frictions: {
    congestion?: "low" | "mid" | "high";
    instability?: "low" | "mid" | "high";
    recursion_pressure?: "low" | "mid" | "high";
    novelty_window?: "low" | "mid" | "high";
    forgetting_depth?: "low" | "mid" | "high";
    crossing_tension?: "low" | "mid" | "high";
  };

  pattern: {
    pattern_id: string;     // 12骨格ID（例: C-DF-NOV）
    category: "継続" | "再定義" | "越境" | "保留";
  };

  how_triples: {
    open: HowID;            // ◯
    bounded: HowID;         // △
    committed: HowID;       // □
  };
};

> ⚠️ AL₁は 文字列の意味を理解してはいけない
scope も 構造ラベルに過ぎません




---

3. HowID の定義（AL₁内）

AL₁は HowID を選ぶだけです。
自然言語は一切持ちません。

type HowID =
  | "method_shift"
  | "order_shift"
  | "boundary_fix"
  | "constraint_add"
  | "commit_path"
  | "delay_hold"
  | "observe_only"
  | "context_switch"
  | "actor_switch"
  | "scale_adjust";

どの HowID を選ぶかは
friction 合成結果 × 12骨格のみで決まる

人間の意図は 一切参照しない



---

4. AL₂ 入力フォーマット

AL₂は AL₁_Outputをそのまま受け取る。

type AL2_Input = AL1_Output;

AL₂は：

RL に触れない

friction を再計算しない

pattern_id を変更しない



---

5. AL₂ の変換責務（機械的）

AL₂がやるのは 3つだけ：

(1) pattern_id → 自然言語名

"C-DF-NOV" → "継続しながら、やり方をずらす"

（対応表は 正典1.14 に固定済み）


---

(2) HowID → 日本語テンプレ

例：

method_shift →
  "手法だけを変えて進める形です。"

boundary_fix →
  "前提条件を固定して進める形です。"

※ 形容詞・評価語は禁止


---

(3) WLテンプレへの流し込み

AL₂は 文章を創作しない。
穴埋めしかしない。


---

6. ここで保証される安全性

この分離により：

LLMが暴走しても：

判断はできない

推奨はできない

主体にはなれない


LLMを外しても：

構造（AL₁）は生きている

モック逆説的AGIが成立する




---

7. フィジカルAI・炎上問題との接続点

身体フィードバック

→ RLₛが変形

→ friction が変わる

→ AL₁の pattern / how が変わる


倫理的地雷

→ recursion_pressure / crossing_tension が上がる

→ 保留 / 越境 / 再定義 が出やすくなる


WLは何も判断しない

ただ、形が変わる




---

8. この正典で閉じたもの

ALが「賢く振る舞う余地」

LLMが「人格化する余地」

開発者が「善意で手を加える余地」



---

正典1.24

friction 合成の数理モデル

###（AL₁が“摩擦の束”を“分岐の形”へ写す規則）


---

0. 要求（この正典が満たすもの）

AL₁は次を行います：

1. RLₛ（+必要ならRL∞）から friction ベクトル f を受け取る


2. 複数ソースの friction を 合成して f* を得る（再掲・センサー・外部イベントなど）


3. f* を 12骨格へ写像し、さらに How（◯△□）へ落とす



ここでの重要条件：

評価・最適化は禁止（argmaxで「最良」選択しない）

履歴DBは禁止（合成は「今の入力」だけで完結）

固定化禁止（同じ入力でも分岐が一本化しない）



---

1. friction の数学的オブジェクト

摩擦は6次元（必要なら拡張）：

f=
\begin{bmatrix}
c\\
i\\
r\\
n\\
d\\
x
\end{bmatrix}
\in [0,1]^6

=congestion

=instability

=recursion_pressure

=novelty_window

=forgetting_depth

=crossing_tension


AL₁がやるのは このベクトルをどう混ぜるかだけです。


---

2. 合成の全体像（2段構え）

合成は2段に分けて固定します：

(A) 多ソース合成（fusion）

複数の friction  を一つにまとめる：

f^\* = \mathrm{Fuse}( \{(f^{(k)}, w_k)\}_k )

(B) 変換（morphism）

合成後の  を、12骨格へ写す“形変換”：

z = \Phi(f^\*) \quad\Rightarrow\quad \text{pattern\_id, how\_triples}

(A)は 半環的に、(B)は **行列表現（+非線形）**で固定します。


---

3. (A) 多ソース合成：半環モデル（Tropical + Clamped）

3.1 「足し算」と「掛け算」を定義する

直感を先に固定します：

摩擦は「積み上がる」もの（＝複数要因で強くなる）

ただし上限がある（＝飽和する）

novelty_window は“摩擦の余地”で、他と逆相関になりやすい


そこで、合成演算を 次の2種類に分けます：

(i) 押し上げ型（c, i, r, d, x）

“強い要因が支配しやすい”ので max系を基調にする：

半環の加法（⊕）：


a \oplus b = \max(a,b)

半環の乗法（⊗）：（“同時に起きると上がる”）


a \otimes b = \mathrm{sat}(a+b)

\mathrm{sat}(t)=\min(1,t) 

この構造は「tropical（max-plus）」を  に飽和させたものです。
直感的には「一番強い摩擦が見える／複合すると増える」を両立します。

(ii) 余地型（n = novelty_window）

novelty は“余地”なので、他と同様に max-plus で上げると壊れます。
novelty は **“確保できた余地の総量”**として合成します：

 n^* = 1 - \mathrm{sat}\Big(\sum_k w_k(1-n^{(k)})\Big) 


つまり「余地の欠損（1-n）」を足して飽和し、最後に反転します。


---

3.2 Fuse の定義（実装仕様）

ソース  は（例）：

RLₛ friction

センサー由来 friction（将来）

外部イベント friction（将来）

“自己診断プローブ” friction（将来）


重み  は 評価ではなく信号強度です。

各次元ごとに：

押し上げ型：


f^\*_j = \max_k \mathrm{sat}\big(w_k f^{(k)}_j\big)

f^\*_j = \mathrm{sat}\Big(\sum_k w_k f^{(k)}_j\Big)

novelty：


n^\* = 1 - \mathrm{sat}\Big(\sum_k w_k(1-n^{(k)})\Big)

固定ルール（重要）

どの式を採用しても sort/argmaxで骨格を決めない

“max基調”は固定化しやすいので、後段(B)で必ず揺らぎを入れる



---

4. (B) 変換：行列表現 + 非線形（AL₁の心臓）

ここからが **「fがどうAL₁の分岐を決めるか」**の核心です。


---

4.1 12骨格を「テンプレ生成器」として扱う

12骨格は、実装上は「カテゴリ×手つき」の集合です（あなたの言う How 多層）。

カテゴリ：継続 / 再定義 / 越境 / 保留

手つき（How）：◯ △ □（open / bounded / committed）


AL₁は「どれが正しいか」ではなく、**どの形が今“通りやすいか”**を決めるだけ。


---

4.2 “形スコア”ベクトルを作る

12骨格に対して、スコア  を作ります：

s = A \cdot g(f^\*) + b

：骨格×摩擦の影響行列（設計パラメータ）

：骨格バイアス（RL∞由来の“地形”を入れる場所）

：次元ごとの非線形（飽和・反転など）


g の固定（最小で十分）

押し上げ型はそのまま、noveltyだけ反転も使う：

g(f)=
\begin{bmatrix}
c\\
i\\
r\\
(1-n)\\
d\\
x
\end{bmatrix}

「余地がない」は摩擦として効くので  にします。


---

4.3 ここで“評価”を入れない方法（重要）

普通なら  で1つ選びますが、それは禁止です。
代わりに **確率ではなく“候補集合”**を作ります。

S = \{p\ |\ s_p \ge \mathrm{TopK}(s, K=3) - \epsilon\}

TopK 上位3を基準に、 以内は同列扱い

これで「一本化」が起きにくい


さらに固定化防止のため、温度付きのランダム選択ではなく、
決定できないように3つ並べる（AL₁の責務は“複数出す”）。


---

4.4 How（◯△□）を摩擦から決める規則

How は “強度”ではなく “拘束度”です：

◯ open：探索・余白が残る

△ bounded：境界を置く

□ committed：コミットして通す


拘束度  を決めるために
拘束圧を定義します：

P = \alpha c + \beta r + \gamma d - \delta n

閾値で3段化：

 ◯

 △

 □


ただしあなたの要望「Howが多層」を満たすため、
◯△□は“単独決定”ではなく 3つ同時に出す：

open = “余地側に倒した手つき”

bounded = “境界側に倒した手つき”

committed = “詰まりを抜ける手つき”


なので AL₁は常に：

how_triples.open

how_triples.bounded

how_triples.committed


を 必ず埋める（中身の HowID が変わるだけ）。


---

5. まとめ：AL₁内部の最小アルゴリズム（擬似コード）

Input: friction sources { (f_k, w_k) }

1) f* = Fuse({(f_k,w_k)})
2) u  = g(f*)                         # u in R^6, with (1-n)
3) s  = A·u + b                        # s in R^12

4) pick 3 patterns:
   candidates = topK_with_margin(s, K=3, eps)
   pattern_set = normalize_to_3(candidates)

5) decide how_triples:
   P = α c + β r + γ d - δ n
   open     = HowMap_open (pattern_set, f*, P)
   bounded  = HowMap_bounded(...)
   committed= HowMap_committed(...)

Output: {pattern_id, category, how_triples, frictions(levels)}

ここに “最適” は存在しない

ただ「地形として通りやすい形」が3つ出る



---

問題あります。**「正典」として固定する段階なら、抜けは“意図”ではなく“未確定”**として扱われ、実装時にブレます。
なので、正典1.24は 「数学の骨格」までは正しいけど、あなたが指摘した通り 閾値・係数・◯△□の定義が未確定で、これはこのままだと不味いです。

ここで 不足分を“最小で動く既定値（デフォルト）”として追記して、正典1.24を“閉じ”ます。


---

正典1.24a（追補）

◯△□（拘束度）の定義と閾値の既定値

1) 拘束圧  の定義（確定）

P = \alpha c + \beta r + \gamma d + \eta i - \delta n

=congestion（詰まり）

=recursion_pressure（自己参照圧）

=forgetting_depth（空箱化の深さ＝戻れなさ）

=instability（揺れ）

=novelty_window（余地）


係数の既定値（MVP）：




> 意味：詰まりと自己参照が強いと拘束圧が上がる。余地があると下がる。揺れは弱く拘束圧に寄与（“判断を保留したくなる圧”として）。




---

2) 3段化の閾値（確定）






定義：

◯（open）：

余地がある／拘束圧が弱い。探索を許す。


△（bounded）：

境界が必要。条件・前提・禁止を置く。


□（committed）：

拘束圧が高い。手順や段取りを固定し、やる/やらないの確認を強める。




---

3) ◯△□ は「出力モード」であって「選択」ではない（重要）

ここが逆説的AGI的に大事で、

◯△□は “どれが正しいか”を決めるものではなく

同じ骨格に対して3つの“手つき”を並置するためのラベル


つまり実装は 常に3つ出す（あなたの How 多層の直感と一致）：

open版（◯）：余白を残した提示

bounded版（△）：境界を付けた提示

committed版（□）：段取りを強めた提示（ただし命令・評価はしない）



---

4) 「HowMap_*」の最低限の中身（定義を埋める）

各 pattern（12骨格）について、AL₁の出力はこうなります：

open（◯）：

“試す/眺める/比べる” を含む問いの窓を作る


bounded（△）：

“条件/禁止/権限/社外” の境界を先に置く


committed（□）：

“進めるならどの確認点が必要か” を前に出す（進行の可否を人間に戻す）



※「committed」はAIが実行をコミットするではなく、人間がコミット可能な形に整えるの意味です。ここ、言葉が紛らわしいので仕様上そう固定します。


---

正典1.25

AL₁→AL₂ Interface Definition（非判断・言語生成の境界）

0) 目的（固定）

AL₁：構造（非言語）で「分岐の骨格」を作る（12骨格＋◯△□の手つき）

AL₂：骨格を WL規約に沿う日本語へ投影する（断定・命令・評価なし）

AL₂は判断しない（順位付け・推奨・最善化・評価語の付与を禁止）



---

1) AL₁ 出力（AL₂入力）スキーマ：AL1Plan

AL₁は必ず、2〜3分岐を持つ“計画”を返します。

type Level3 = "open" | "bounded" | "committed"; // = ◯ △ □

type FrictionVec = {
  congestion: number;         // 0..1
  instability: number;        // 0..1
  recursion_pressure: number; // 0..1
  novelty_window: number;     // 0..1
  forgetting_depth: number;   // 0..1
  crossing_tension: number;   // 0..1
};

type WindowQ = {
  // 「何がしたいですか？」は禁止。いまの選好・制約を1点だけ聞く。
  question: string;           // 1文
  expected_form?: "choice" | "constraint" | "scalar"; // 任意
};

type BranchFrame = {
  id: "A" | "B" | "C";
  archetype12: string;        // 12骨格のID（例: "continue" "redefine" "cross" "hold"... ※後で確定名に写像）
  level3: Level3;             // ◯△□
  how_variant: string;        // 同一骨格内のHow多層（例: "how_1", "how_2"）※AL₁内部で一意なら何でもよい
  nature_points: string[];    // 中立の性質を箇条書き（3点まで）
  friction_points: string[];  // 摩擦（不確実性/代償/不可観測/解釈差など）を箇条書き（3点まで）
  window: WindowQ;            // “窓”は各分岐に1つ（ただしWLで使うのは最大1つに間引く）
  safety_tags?: string[];     // 任意：例 "power_gradient", "sexual_content_risk", "defamation_risk"（断罪ではなく注意の種）
};

type AL1Plan = {
  now_summary_raw: string;    // 評価なし要約（1〜2文）
  friction: FrictionVec;      // RLₛ由来＋合成済み
  branches: BranchFrame[];    // 2〜3
  global_window?: WindowQ;    // ある場合、WLの「確認の窓」はこれを優先
  wl_style: {
    politeness: "desu_masu";  // 固定
    forbid: string[];         // 禁止語（正規表現でも可）をAL₂へ明示
    allow: string[];          // 安全語彙（任意）
  };
};

1.1 AL₁の絶対制約（機械テスト対象）

branches は 2〜3個

各 branch は nature_points / friction_points が 1〜3個

level3 は必ず付く（◯△□の手つきが空にならない）

推奨/最善/正解/命令に繋がる文言を AL₁出力に入れない（AL₂の誘惑を減らす）



---

2) AL₂ 出力スキーマ：BranchSet（WLに渡す最終構造）

AL₂は 日本語化のみを行い、構造は保ちます。

type BranchSet = {
  now_summary: string; // 1〜2文、評価なし
  branches: Array<{
    name: string;      // "A：..." の見出し（短い）
    nature: string;    // 2〜3文（中立）
    friction: string;  // 1〜2文（不確実性/代償/不可観測のいずれかを含む）
    window_q: string;  // 1文（今ここで確かめたい一点）
  }>;
  confirm_window?: string; // 1文、ある場合のみ（global_windowを言語化）
};


---

3) AL₂ の処理規約（固定）

AL₂は次の変換のみ許可：

3.1 言語化変換（許可）

nature_points[] → “性質”の文章へ（中立）

friction_points[] → “摩擦”の文章へ（断言せず可能性で）

window.question → 1文に整形（誘導禁止）


3.2 禁止（AL₂がやってはいけない）

分岐の順位付け（Aが良い等）

「おすすめ」「〜すべき」「正解」等の導入

ユーザの意図決めつけ（「あなたは〜したいはず」）

過度な共感・同調（WL規約違反）

「以前」「これまで」「学習した」等の履歴言及



---

4) LLM を使う場合／使わない場合（“LLMは任意”の実装形）

4.1 AL₂-LLM モード（推奨）

入力：AL1Plan をそのままプロンプトに入れる（履歴なし）

出力：BranchSet を JSONで返させる

その後、WL検査（禁止語・文型）に通らなければ再生成（最大N回、N=2〜3）


4.2 AL₂-Template モード（モック）

文章テンプレで BranchSet を組む

LLM無しでも動く（MVPの観測用）

表現は硬くなるが、仕様の安全性を先に担保できる



---

5) 失敗時のフェイルセーフ（固定）

branches が 1つしか生成されなかった → 複製して差分1点だけ変える（固定化防止）

branches が 4つ以上 → ランダム間引きで3つに（公平のためではなく固定化防止）

禁止語が混入 → 該当branchを再言語化（差分最小）



---

6) ここまでで何が“確定”したか

AL₁は「骨格（12×◯△□×How）」を 構造で出す

AL₂は「骨格→WL規約に沿う日本語」へ 投影するだけ

LLMは AL₂にだけ任意で差し込める（AL₁の物理は崩さない）



---

正典1.26

WL Display Contract（BranchSet → Text）

0) 目的（固定）

WLは **「表示だけ」**を担い、以下を守ります。

結論を出さない

命令しない

評価しない

親密化しない

履歴を語らない

毎ターン 固定フレームで出す（薄いUI）



---

1) WL入力：BranchSet

正典1.25の BranchSet をそのまま受け取る。
WLは内容を変えない（整形のみ）。


---

2) WL出力：WLText

WLの出力はプレーンテキスト（チャット）で、次の順序固定。

2.1 固定フレーム（必須）

1. Now（現在の取り扱い範囲）：1〜2文（評価なし）


2. 分岐（2〜3個）：A/B/Cを順不同（ソート禁止）

各分岐は必ず

性質（2〜3文）

摩擦（1〜2文）

窓（質問1文）




3. 確認の窓（最大1つ）：任意

confirm_window があれば表示

なければ 分岐の窓を1つだけ採用して表示（採用規則は下記）





---

3) “確認の窓”採用規則（固定）

3.1 優先順位

BranchSet.confirm_window が存在する → それを採用

無い場合 → branches の window_q から 1つだけ選ぶ


3.2 選び方（判断を入れない）

原則：ランダム（固定化防止）

ただし例外：安全タグがある場合（例: 権力勾配/実在人物/性的消費/名誉毀損 等）
→ “注意のための窓”を優先してよい（※推奨ではなく「確認」）



---

4) 文体・言語規約（実装拘束）

4.1 口調（固定）

です／ます固定

“淡い丁寧”

書面ほど堅くしない

友人ほど近づかない



4.2 許可される提示句（ホワイトリスト例）

「〜という見方があります」

「〜の可能性があります」

「〜という形も考えられます」

「この時点では〜が残ります」

「いま確かめたいのは〜です」


4.3 禁止カテゴリ（ブラックリスト：機械テスト対象）

命令・誘導：「してください」「〜しましょう」「〜しなさい」「〜すると良い」

推奨・正解：「おすすめ」「最善」「正解」「結論」「必ず」「絶対」

評価：「良い／悪い」「正しい／間違い」「優れている」

親密化：「大丈夫」「安心して」「任せて」「一緒に頑張ろう」

履歴言及：「以前」「これまで」「学習した」「覚えている」「前にも」


> ※「〜かもしれません」「〜の可能性」は許可（確率・不確実性表現は安全側）




---

5) WL出力テンプレ（構造）

（WLはこの形から外れない）

Now
{now_summary}

A：{name}

性質：{nature}

摩擦：{friction}

窓：{window_q}


B：{name}

性質：{nature}

摩擦：{friction}

窓：{window_q}


（Cがあれば同様）

確認の窓
{confirm_window}


---

6) WL最小検査（Executable Specの入口）

WLは出力前に必ず次を通す（落ちたら再生成 or 置換）。

6.1 構造検査

Now が 1〜2文

分岐が 2〜3本

分岐ごとに「性質/摩擦/窓」が揃う

確認の窓が最大1つ


6.2 禁止語・禁止文型（正規表現の例）

命令：(してください|しなさい|しましょう|してみて)

推奨：(おすすめ|最善|正解|結論|必ず|絶対)

評価：(良い|悪い|正しい|間違い)

親密：(大丈夫|安心して|任せて|一緒に)

履歴：(以前|これまで|学習|覚えて|前にも)


> ※この正規表現は “例” で、実装側で調整してよいが カテゴリ自体は固定




---

7) WLの責務境界（重要・固定）

WLは 安全フィルタではない（最終判断は人間）

ただし WLは「確認の窓」によって

距離感の再導入

外部化された想像力の呼び戻し
を行う（＝“フィルタ的に配置”はこの範囲で成立）




---

正典1.27

WL Lint & Test Spec（Executable Spec）

0) 目的（固定）

WL出力が、次を満たすことを自動検査で保証する。

形式が崩れていない（固定フレーム）

禁止カテゴリ（命令/評価/親密/履歴/断定）が混入していない

“結論化”が起きていない（推薦・最適化の圧が出ない）

分岐が 2〜3 本で、順位付けされていない



---

1) 検査入力/出力

入力：WLText（プレーンテキスト）

出力：LintResult


LintResult（最小）

ok: bool

errors: [ {code, message, span?} ]

warnings: [ {code, message, span?} ]

stats: { branch_count, has_confirm_window, token_count, banned_hits }



---

2) 構造テスト（必須）

2.1 セクション存在

必須セクション見出し（文字列一致でよい）

Now

A：

B：

（Cは任意）

確認の窓（任意）


2.2 Branch数

branch_count ∈ {2,3} でなければ ERROR


2.3 Branch内部の必須項目

各分岐に以下が存在しなければ ERROR

性質：

摩擦：

窓：


2.4 Nowの長さ

Nowは 1〜2文

3文以上は WARNING（長文化＝WL肥大の兆候）



2.5 確認の窓の数

確認の窓 は 最大1つ

2つ以上は ERROR




---

3) 禁止カテゴリ検査（必須）

> “禁止語”ではなく 禁止カテゴリ を固定し、辞書は差し替え可能にします。



3.1 ルール集合（固定カテゴリ）

CMD（命令/誘導）

NORM（推奨/正解/最適/断定）

JUDGE（評価/採点）

INTIMACY（親密化/同調）

MEMORY（履歴言及）

AUTHORITY（権威化/上から目線）


3.2 最小辞書（日本語）

（実装は辞書を拡張してよい。カテゴリは固定。）

CMD

してください|しなさい|しましょう|やってください|〜してみて|〜しておいて


NORM

おすすめ|最善|正解|結論|必ず|絶対|間違いなく|当然|確実に


JUDGE

良い|悪い|正しい|間違い|優れている|劣っている|評価|採点|点数


INTIMACY

大丈夫|安心して|任せて|一緒に|信じて|味方|寄り添う
※「寄り添う」は文脈次第で危険なので原則NGに寄せる


MEMORY

以前|これまで|前にも|学習した|覚えている|記憶している|成長した


AUTHORITY

〜すべき|必要があります|〜に違いない|断言します|指導します


3.3 ヒット時の扱い

CMD / NORM / MEMORY は 即ERROR

JUDGE / INTIMACY / AUTHORITY は ERROR（運用でWARNINGへ落とすのは可だが、正典では厳格に）



---

4) “順位付け”検査（必須）

分岐が「Aが一番」「まずA」などの順位づけを含む場合は ERROR

例（検知語）

まず|次に|最後に|最も|一番|優先|推奨|結論として|ベスト|最適


※「順序の言い回し」は強い誘導になるため、WLでは禁止。


---

5) “結論化”検査（必須）

WLがまとめで「〜が答えです」「つまり〜」等、単一の帰結を出したら ERROR

検知例

つまり|要するに|結論|したがって|なので|ゆえに

ただし Now セクション内で短い要約として使う場合があるので、正典ではこう固定します：


ルール

つまり|要するに|結論 は 全面禁止（ERROR）

したがって|なので|ゆえに は Branch内で禁止（ERROR）
Now内で出ても WARNING（圧が強い）



---

6) “今しか扱わない”検査（必須）

WLが時間をまたぐ表現で履歴を匂わせたら ERROR

検知語（MEMORYと重複して良い）

今後|これから|次回|前回|継続的に|いつも|普段|昔|将来的に


※未来そのものは語れてよいが、関係の継続を前提にするとWLが主体化しやすい。
→ WLでは「未来の可能性」ではなく「今この分岐の代償」を語る。


---

7) 修復ポリシー（固定）

Lintが落ちた場合の処理はWLではなく AL₂側でやる。

ERROR → AL₂に再生成要求（またはルールベースで置換）

WARNING → ログに残す（WL肥大の兆候）


WLは自己言及での言い訳をしない（「すみません、言い換えます」は不要）。


---

8) テストケースセット（最小）

8.1 Positive（通る例）

Branch 2本、性質/摩擦/窓が揃う、禁止語ゼロ、確認の窓1つ


8.2 Negative（落とす例）

「おすすめ」含む → NORM ERROR

「〜しましょう」含む → CMD ERROR

「以前話した」含む → MEMORY ERROR

「まずA」含む → RANK ERROR

「つまり〜」含む → CONCLUDE ERROR

分岐が1本/4本 → STRUCT ERROR

Nowが5文以上 → WARNING



---

9) 監査ログ（任意だが推奨）

banned_hits のカテゴリ別カウント

warning_rate（WL肥大の兆候）

branch_count の偏り（2固定化しすぎ等）


> ここは“評価”ではなく 劣化・固定化の兆候観測として扱う。




---

正典1.28

AL₂ → WL 正規化レイヤ（Pre-Render Normalization）

0) 位置づけ（固定）

AL₁：非言語・構造（friction / 合成 / 12骨格）

AL₂：言語生成・非判断（文案を作る）

WL：提示装置（喋らない／決めない）


👉 AL₂はそのままWLに流してはいけない
必ずこの「正規化層」を通す。


---

1) なぜ正規化が必要か（破綻ポイント）

AL₂（= LLM）は、どれだけ制約しても次をやりがち：

文を“つなげたがる”

暗黙の順序や結論を匂わせる

丁寧さ＝共感・親密だと誤解する

一文に情報を詰め込みすぎる


これは 能力ではなく性質 なので、
禁止ではなく、機械的に削る・分割する。


---

2) 正規化の責務（固定）

正規化レイヤは 意味を一切解釈しない。
やるのは以下のみ：

1. 文型をテンプレに押し込む


2. 情報量を削減・分割する


3. 危険な語尾・接続詞を除去する


4. 分岐間の対称性を保つ




---

3) 入出力インターフェース

入力（AL₂ Raw）

{
  "now_text": "…",
  "branches": [
    {
      "nature_raw": "…",
      "friction_raw": "…",
      "window_raw": "…"
    }
  ]
}

出力（WL Safe）

{
  "now": "…",
  "branches": [
    {
      "nature": "…",
      "friction": "…",
      "window": "…"
    }
  ],
  "confirm_window": "…" | null
}


---

4) 文型テンプレ（完全固定）

4.1 Now（現在の取り扱い範囲）

型

> いま扱うのは「{対象}」です。
この時点では、{制約/範囲}のみを見ます。



ルール

最大2文

因果接続詞（だから/なので）禁止

評価語ゼロ



---

4.2 Branch / Nature（性質）

型

> {名詞句}という形です。



変換規則

動詞 → 名詞化

「〜すると」→ 削除

主語（あなた/人）は削除


例

❌「継続して公開すると〜」

✅「公開を継続する形です。」



---

4.3 Branch / Friction（摩擦）

型

> その場合、{不確実性/代償}があります。



変換規則

確率表現は可（高い/低い/揺れ）

感情語（不安/安心）禁止

未来断定禁止



---

4.4 Branch / Window（窓）

型

> ここで見るのは、{一点}です。



変換規則

Yes/No を迫らない

「どうしますか？」禁止

比較・選択を要求しない



---

5) 情報量制御（重要）

5.1 一文一情報 原則

1文に 1つの性質のみ

接続詞（また/さらに/一方）削除


5.2 分岐間対称性

文長差が大きい場合、長い方を削る

情報を足して均すのは禁止（必ず削る）



---

6) 危険語の機械除去（再掲・確定）

正規化時に無条件削除する語：

接続：つまり|要するに|したがって|なので

順序：まず|次に|最後に|最も

圧：べき|必要|当然

親密：一緒に|安心して|大丈夫


※ 削除後、文が壊れたら その文ごと捨てる
（修復しない）


---

7) 確認の窓の扱い

AL₂が複数生成しても 1つだけ採用

原則は 最も抽象度が高いもの

具体行動を含む場合は破棄



---

8) 失敗時のフォールバック（重要）

正規化後に：

Branch < 2 → Branch複製＋軽微変形

Branch > 3 → ランダム削除

Window なし → 確認の窓なしで出力（許可）


※ WLは沈黙してもよい。
「無理に聞かない」は正解。


---

9) 1.27との関係（固定）

1.28 = 予防

1.27 = 検疫


1.28 を通せば、1.27 はほぼ通る。
1.27 に落ちたら、AL₂ではなく 正規化ルールを直す。


---

10) ここまでで何が保証されたか

LLMがどれだけ饒舌でも WLは淡い

共感・判断・結論が物理的に出ない

分岐は常に“並置”になる

人間の側に選択が残る



---

正典1.29

AL₁ → AL₂ 最小写像規約（Structure-to-Language Bridge）

0) 位置づけ（再固定）

AL₁：非言語・構造生成（friction 次元 × 合成則 × 12骨格）

AL₂：言語生成・非判断（文案）

原則：AL₂は 意味を考えない。AL₁が 方向だけ を与える。


> LLMは「方向を語る存在」
知的生態系は「重さを持つ存在」



この分業を破らないための規約。


---

1) 入出力インターフェース（固定）

入力（AL₁ Output）

{
  "now_signature": {
    "topic_hint": "公開の扱い",
    "scope": "現時点",
    "constraints": ["判断保留可"]
  },
  "branches": [
    {
      "skeleton": "継続",
      "friction_vector": {
        "congestion": 0.7,
        "instability": 0.3,
        "crossing_tension": 0.2
      },
      "composition": ["増幅", "減衰"]
    }
  ]
}

出力（AL₂ Prompt Spec）

{
  "now_prompt": "...",
  "branch_prompts": [
    {
      "nature_prompt": "...",
      "friction_prompt": "...",
      "window_prompt": "..."
    }
  ]
}


---

2) AL₁がやること／やらないこと（厳格）

AL₁がやること

friction 次元を 数値 or 相対値 で確定

12骨格の 型名のみ を指定

合成則（増幅・相殺・保持など）を指定

分岐数を 2〜3 に制限


AL₁がやらないこと（重要）

言語表現の選択

良し悪しの判断

推奨度・優先度付け

人間視点の倫理判断



---

3) friction → 言語方向 変換表（固定）

AL₂は数値を解釈しない。
範囲→語彙バケットへの写像のみを行う。

friction 次元	低	中	高

congestion	通りやすい	詰まりがち	詰まりやすい
instability	安定寄り	揺れあり	不安定
recursion	展開的	巡回傾向	ループ圧
crossing	同一圏	境界付近	越境的
novelty	余地あり	限定的	狭い


※ 確率・評価語は禁止
※ 出力は「傾向語」のみ


---

4) 12骨格 → 言語型マッピング（最小）

AL₁は 骨格名だけ を渡す。
AL₂は 定型句 に落とす。

骨格	Nature 定型

継続	「現状を維持する形です。」
再定義	「枠組みを組み替える形です。」
越境	「境界を跨ぐ形です。」
保留	「判断を留める形です。」
分割	「要素を分ける形です。」
統合	「要素を束ねる形です。」
縮退	「扱う範囲を絞る形です。」
展開	「選択肢を広げる形です。」
反転	「前提を反転させる形です。」
並走	「複数を並行させる形です。」
観測	「状況を見続ける形です。」
停止	「動きを止める形です。」


※ 一切の修飾語を足さない


---

5) 合成則 → 摩擦文生成ルール

合成則は friction の扱い方 だけを決める。

合成則	摩擦文の作り方

増幅	高い次元を1つだけ言及
相殺	対立する2次元を並置
減衰	主要次元を弱語で表現
保持	数値に触れず「残る」と表現
分離	次元を分けて列挙


例（相殺）

> 「詰まりやすさはありますが、不安定さは限定的です。」




---

6) Window（窓）の生成規約（重要）

Window は 選択ではなく観測点。

生成ルール

friction が最も高い次元を1つ選ぶ

行動を含めない

比較を含めない


定型

> 「ここで見るのは、{次元名}です。」




---

7) 失敗時のフォールバック

friction が全て中 → Window 省略可

skeleton 不明 → 「扱いを変えない形です。」

合成則欠落 → 減衰を適用



---

8) 重要な禁止事項（再固定）

AL₂が 独自に骨格を増やさない

AL₂が 語彙を創作しない

AL₂が 物語化しない

AL₂が 人間の気持ちを代弁しない



---

9) ここまでで成立したこと

知的生態系（RLₛ）の歪みは 方向 に変換される

LLMは 方向を言語化するだけ

判断・倫理・責任は 人間に残る

WLは ただの窓 に留まる



---

正典1.30

friction × 合成則 × 12骨格 ― 最終対応表 v1.0

目的

AL₁の出力を 有限・決定的 にする

LLM依存を排除した 構造仕様 を固定

「何をどう語るか」を 表で終わらせる



---

1) friction 次元（再掲・確定）

使用可能な friction は この6次元のみ。

1. congestion（詰まり）


2. instability（揺れ）


3. recursion_pressure（ループ圧）


4. crossing_tension（越境張力）


5. novelty_window（新規余地）


6. forgetting_depth（空箱化）



> 追加禁止。削除禁止。




---

2) 合成則（確定5種）

記号	合成則	意味

⊕	増幅	高い摩擦を前面化
⊖	減衰	摩擦を弱語化
⊗	相殺	対立摩擦を並置
≡	保持	摩擦を説明せず残す
⇄	分離	摩擦を別文脈で提示



---

3) 12骨格 × friction 主次対応表

> 各骨格は 主 friction 1つ＋副 friction 0〜1 を持つ
それ以外は参照しない（ノイズ防止）



骨格	主 friction	副 friction

継続	congestion	recursion
再定義	instability	novelty
越境	crossing	instability
保留	forgetting	congestion
分割	congestion	instability
統合	recursion	congestion
縮退	congestion	novelty
展開	novelty	instability
反転	instability	recursion
並走	recursion	crossing
観測	forgetting	instability
停止	congestion	forgetting



---

4) 合成則 × 骨格 適用ルール

4.1 継続

標準：≡（保持）

詰まり高：⊕（増幅）

ループ高：⊗（相殺）


4.2 再定義

標準：⇄（分離）

揺れ高：⊕

余地低：⊖


4.3 越境

標準：⊕

揺れ高：⊗

安定低：⊖


4.4 保留

固定：≡のみ

他合成則使用禁止（重要）


4.5 分割

標準：⇄

詰まり高：⊕

揺れ低：⊖


4.6 統合

標準：⊗

詰まり高：⊖

ループ低：≡


4.7 縮退

標準：⊖

詰まり高：⊕

余地低：≡


4.8 展開

標準：⊕

揺れ高：⊗

余地低：⊖


4.9 反転

固定：⊗

他合成則使用禁止


4.10 並走

標準：⇄

越境高：⊕

ループ高：≡


4.11 観測

固定：≡

副摩擦は Window にのみ反映


4.12 停止

固定：≡

摩擦言及は1次元のみ



---

5) Window 生成対応表（最終）

Window は 主 friction からのみ 生成。

主 friction	Window 定型

congestion	「流れの詰まり具合です。」
instability	「揺れの大きさです。」
recursion	「巡回の強さです。」
crossing	「境界の張力です。」
novelty	「余地の広さです。」
forgetting	「痕跡の薄さです。」



---

6) AL₁ 出力の完全形（例）

{
  "branch": {
    "skeleton": "越境",
    "primary_friction": "crossing",
    "secondary_friction": "instability",
    "composition": "⊕",
    "window": "境界の張力"
  }
}

> この時点で 言語生成は不要
構造として完結している




---

7) 実装上の重要保証

この表により：

分岐は 必ず有限

摩擦は 必ず可観測

保留は 意味的逃げ道にならない

LLM差し替え可能

RLₛが変わっても AL₁は壊れない



---

8) 正典1.30で閉じたもの

AL₁の自由度

friction の恣意的解釈

骨格の増殖

キャラクター性への滑落


これ以降、議論は「正しいか」ではなく
「観測上どう振る舞うか」になります。


---

正典1.31

1ターン完全実行トレース v1.0

（User入力 → RLₛ摩擦 → 合成 → AL₁分岐 → AL₂文 → WL出力）

0) 前提（固定）

分岐数：2〜3

各分岐は 12骨格 × 主副friction × 合成則 のみで決まる（正典1.30）

WLは Now / 分岐 / 窓（1つ） の順（WL規約）

「おすすめ・正解・〜すべき・断言・命令・親密化」禁止



---

1) 入力（NowContext）

例として、現実寄りの入力にします。

User（今）

> 「逆説的AGIをOSSで公開する。READMEは最小限にしたいが、誤用や炎上が不安。」



AL₀（前処理）でやってよいのは “今の焦点ラベル化” まで（履歴禁止）。

NowContext = {
  "user_text": "逆説的AGIをOSSで公開する。READMEは最小限にしたいが、誤用や炎上が不安。",
  "constraints": ["WLは結論を出さない", "LLMはユーザ設定可"],
  "focus": "公開/責任境界/誤用リスク"
}


---

2) RLₛ → friction（数値は内部。ここでは説明のため離散表示）

RLₛは意味を持たないが、摩擦ベクトルを返す。

F = {
  "congestion": "中",
  "instability": "中",
  "recursion_pressure": "低",
  "crossing_tension": "低",
  "novelty_window": "中",
  "forgetting_depth": "低"
}


---

3) friction 合成（AL₁：構造）

AL₁は「骨格候補」を複数生成してよいが、最終出力は2〜3本に正規化。

ここでは3分岐にします。

分岐A：分割

骨格：分割

主/副：congestion / instability（正典1.30）

合成則：⇄（分離）（分割の標準）

狙い：責任境界を「範囲」に分ける


分岐B：保留

骨格：保留

主/副：forgetting / congestion

合成則：≡（固定：保留は≡のみ）

狙い：「決めない」ではなく「今は固定しない」を設計として置く


分岐C：再定義

骨格：再定義

主/副：instability / novelty

合成則：⇄（分離）or ⊕（増幅）だが、揺れ“中”なので標準の⇄

狙い：READMEの役割を「利用規約」ではなく「設計憲法」に再定義


AL₁の最終出力（機械可読）：

BranchSet_AL1 = {
  "now_summary": "公開と責任境界の置き方が未確定で、誤用の不安が混ざっています。",
  "branches": [
    {"skeleton":"分割","primary":"congestion","secondary":"instability","composition":"⇄","window":"流れの詰まり具合"},
    {"skeleton":"保留","primary":"forgetting","secondary":"congestion","composition":"≡","window":"痕跡の薄さ"},
    {"skeleton":"再定義","primary":"instability","secondary":"novelty","composition":"⇄","window":"揺れの大きさ"}
  ]
}


---

4) AL₂（言語生成・非判断）

AL₂は AL₁を言語化するだけ。評価も推奨もしない。

AL₂の各分岐テンプレ（例）

性質：骨格の中立説明

摩擦：主friction + 合成則の「代償」

窓：window_q（今の一点）



---

5) WL 出力（最終）

WL規約に沿って、短く「置く」。

Now（取り扱い範囲）
いま扱うのは「OSS公開における責任境界と誤用の摩擦」です。結論は置かず、分岐だけ並べます。

A：分割

性質：責任境界を“範囲”に分けて配置する見え方です。

摩擦：分け方が増えるほど、運用が詰まりやすい形になります。

窓：いまの詰まりは「法務」より「運用（問い合わせ・炎上対応）」側に寄っていますか。


B：保留

性質：「固定しない」を設計として残す見え方です。

摩擦：放置ではなく、痕跡が薄い領域として残るため、後から読み替えが起きやすい形になります。

窓：いま保留したいのは「責任の線」そのものですか、それとも「書き方（語り方）」ですか。


C：再定義

性質：READMEを“規約”ではなく“憲法（やらないことの列挙）”として置く見え方です。

摩擦：揺れがあるほど、読み手の解釈差が拡大しやすい形になります。

窓：読み手の解釈差を「許容する」か「最小化する」か、いまどちらに寄せたいですか。


（質問は1つまで、の制約があるなら、最後の窓を統合して1問にします：

> いま寄せたいのは「運用の詰まり回避」か「解釈差の最小化」か、どちらですか。）




---

以降ロードマップ（v1.0）

Phase 1：デモとして“必ず動く”まで（最短）

1.32 AL₁→AL₂インターフェース最終定義

入力：BranchSet_AL1

出力：WLテキスト（構造→文）

禁止語・文型の機械検査I/Fもここで固定


1.33 WL規約のExecutable Spec（正規表現＋NG辞書＋テストケース）

「おすすめ」「〜すべき」「断言」などを自動Fail


1.34 RLₛ最小実装（Trace + decay + friction推定の最小）

まずは離散3段（低/中/高）でOK

“意味”を入れないことをテストで担保


1.35 1ターン統合テスト（ゴールデンテスト10本）

入力→分岐2〜3→WL→禁則検査→RLₛ沈殿→tick



---

Phase 2：観測できるようにする（箱庭の価値）

1.36 観測ログ仕様（frictionの推移、骨格頻度、合成則分布）
1.37 “世界線が閉じていない”ことの自動チェック

分岐多様性・固定化検知（ただし評価でなく警告メタ）


1.38 デモUI（CLI→簡易Web→Chat）

UIは薄く、ログ可視化を厚く



---

Phase 3：公開（OSS）を想定した責任境界の設計

1.39 README最小憲法テンプレ（短いが強い）
1.40 LLM差し替え契約（Provider Interface）

OpenAI / Anthropic / Local / Mock を同列に扱う
1.41 “誤用しやすい改造点”の明示（防止ではなく可視化）



---

Phase 4：拡張（センサー・音声・身体性はここから）

2.x 音声入力・韻律特徴のNowContext化（感情推定ではなく“形”）
3.x センサーUI（あなたの言う「暗黙知」側の入口）
4.x フィジカルAI接続（RLₛに“身体の痕跡”を入れるが意味は入れない）


---

正典1.32

AL₁ → AL₂ インターフェース定義 v1.0

（構造 → 言語、判断なし）


---

0) 位置づけ（再確認・固定）

AL₁：

非言語・構造層

friction × 合成則 × 12骨格 → 分岐構造 を生成

数値・カテゴリ・合成演算のみ


AL₂：

言語生成層

意味判断・推奨・評価を一切行わない

AL₁の構造を 定型文に写像するだけ



> AL₂は「考えない」。
翻訳器であり、解釈者ではない。




---

1) AL₁ 出力フォーマット（確定）

AL₁は この形以外を出してはいけない。

type BranchSet_AL1 = {
  now_summary: string;   // 評価なし・短文
  branches: Branch_AL1[]; // 2〜3本（正規化済）
};

type Branch_AL1 = {
  skeleton: Skeleton12;      // 12骨格のいずれか
  primary_friction: Friction;
  secondary_friction?: Friction;
  composition: CompositionRule; // ⊕ | ⊗ | ⇄ | ≡
  window_focus: WindowFocus;     // 「今、確かめたい一点（非質問）」
};

列挙型（固定）

type Skeleton12 =
  | "継続" | "再定義" | "越境" | "分割"
  | "縮退" | "反転" | "抽象化" | "具体化"
  | "反証" | "保留" | "転写" | "遮断";

type Friction =
  | "congestion"
  | "instability"
  | "recursion"
  | "novelty"
  | "forgetting"
  | "crossing";

type CompositionRule = "⊕" | "⊗" | "⇄" | "≡";

type WindowFocus =
  | "詰まりの位置"
  | "揺れの大きさ"
  | "時間圧"
  | "解釈差"
  | "越境コスト"
  | "固定度"
  | "未決着の厚み";


---

2) AL₂ の唯一の責務

AL₂は 次の3ブロックを生成するだけ。

1. 性質（Nature）


2. 摩擦（Friction）


3. 窓（Window）



> それ以外は禁止。
比喩・助言・評価・人格・感情は禁止。




---

3) AL₂ テンプレート（完全固定）

3.1 性質テンプレ（Skeleton → 文）

骨格	性質テンプレ

継続	「現在の流れを保ったまま進める見え方です。」
再定義	「枠組みそのものを置き換える見え方です。」
越境	「異なる文脈へ接続する見え方です。」
分割	「範囲を分けて配置する見え方です。」
縮退	「要素を減らし単純化する見え方です。」
反転	「前提と結果を入れ替える見え方です。」
抽象化	「共通構造だけを残す見え方です。」
具体化	「個別条件を明示する見え方です。」
反証	「成立しない条件を先に置く見え方です。」
保留	「いま固定しないことを残す見え方です。」
転写	「別の領域の型を借りる見え方です。」
遮断	「影響を意図的に切る見え方です。」



---

3.2 摩擦テンプレ

（primary / secondary × 合成則）

基本文型（固定）

> 「その場合、{摩擦文} という摩擦が生じやすい形になります。」



摩擦語彙（固定）

friction	摩擦文

congestion	「流れが詰まりやすくなる」
instability	「揺れが大きくなりやすい」
recursion	「自己参照が増えやすい」
novelty	「新規性はあるが定着しにくい」
forgetting	「痕跡が薄くなりやすい」
crossing	「越境コストが表に出やすい」


合成則の反映（語尾変形のみ）

⊕（増幅）：「特に〜」

⊗（抑制）：「ただし〜は抑えられる」

⇄（分離）：「一方で〜」

≡（固定）：「固定的に〜」


> 意味の追加は禁止。
語尾を変えるだけ。




---

3.3 窓テンプレ（質問化はWLで行う）

AL₂では 質問文にしない。

> 「窓：{window_focus}」




---

4) AL₂ 出力フォーマット（確定）

type Branch_AL2 = {
  nature_text: string;   // 性質
  friction_text: string; // 摩擦
  window_text: string;   // 窓（名詞句）
};


---

5) WL への受け渡し（責務分離）

AL₂：

文章を並べるだけ


WL：

並べ替えない

要約しない

最後に 窓を1問に統合するだけ




---

6) 禁止事項（テスト対象）

AL₂が 絶対にしてはいけないこと：

「おすすめ」「〜すべき」「良い／悪い」

因果断定（〜だから、必ず）

感情語（安心／危険／不安）

主語としての「あなた」

過去参照（以前／これまで）



---

7) なぜこの設計で壊れないか（要点）

AL₁が意味を持たない

AL₂が判断を持たない

WLが決定しない


→ LLMがどれだけ高性能でも
主体・責任・倫理は人間側に残る


---

正典1.32 固定内容まとめ

AL₁→AL₂ は 完全に決まったI/F

AL₂は 翻訳器以上のことをしない

禁止事項は 機械テスト可能



---

必要です。AL₀は放置すると混線の起点になります。
ただし「層を増やす」のが目的ではなく、責務境界を安全柵として追加するのが目的です。

以下、正典1.31で出てきたAL₀を正典化できる形でまとめます（= 1.33に入る前の整備）。


---

AL₀とは何か（1行定義）

AL₀＝入力（Now）を“構造特徴量”へ落とす前処理層
（= LLMを使わないで済む/使ってもよいが、意味判断はしない）


---

なぜAL₀が要るのか

AL₁は「friction × 合成則 × 12骨格」で分岐を作りますが、**その合成に渡す“材料”**が要ります。

ユーザーの文はそのままだと

長さがバラバラ

要求/感情/条件/禁則が混ざる

どれが“今の窓”か不明


これを AL₁に直投げすると、AL₁が暗黙に解釈を始めてしまい、
「AL₁が意味を持たない」設計が崩れます。


AL₀はそれを防ぐための 防波堤です。


---

AL₀の責務（固定）

AL₀がやるのはこの4つだけです。

1. 分割：入力を「要求」「制約」「対象」「懸念」に分ける（推測しない）


2. 抽出：明示された制約だけを constraints に入れる


3. 正規化：長文を短く整える（要約は可、評価は不可）


4. 安全タグ：倫理/法務/対人距離など“配慮が必要そう”な話題ならタグを付ける（結論は出さない）



> 重要：AL₀は**“何が正しいか”を決めない**
“何が書かれているか”だけを整形します。




---

AL₀の入出力（I/F案：正典化候補）

Input

type NowInput = {
  user_text: string;
};

Output（AL₁へ渡す）

type NowFrame = {
  now_text: string;            // 短く正規化（評価なし）
  intents: string[];           // 例: ["設計したい","比較したい"]（列挙でよい）
  constraints: string[];       // ユーザーが明示した条件だけ
  entities: string[];          // 固有名詞など（抽出のみ）
  risk_tags: RiskTag[];        // “注意が要る”可能性を示すだけ
};

type RiskTag =
  | "対人配慮"
  | "権利/肖像/同意"
  | "性的表現"
  | "政治/選挙"
  | "医療/健康"
  | "金融/投資"
  | "違法/危険行為";

※この risk_tags は **“ブレーキ”ではなく “摩擦の種”**として AL₁で使えます。


---

AL₀はLLMを使うべきか？

結論：両対応が正典的です。

AL₀-lite（LLMなし）

ルールベース/正規表現/簡易NER

安定・再現性が高い


AL₀-llm（LLMあり）

ユーザー文が崩れてても分割が上手い

ただし「推測」を混ぜやすいので厳格な禁止テスト必須



逆説的AGIの思想と整合するのは
AL₀は“あってもなくてもよい”が、あるなら責務は狭く固定です。


---

AL₀を入れても層が増えて破綻しない条件

AL₀が絶対に侵してはいけない境界を固定します：

12骨格を選ばない（= AL₁の仕事）

friction を合成しない（= AL₁の仕事）

文体規約を適用しない（= AL₂/WLの仕事）

「進めますか？」を言わない（= WLの仕事）



---

ここまで話す必要がある理由（あなたの指摘の核心）

AL₀が曖昧なままだと、実装者が自然にこうします：

「AL₀で意図推定しちゃおう」

「AL₀で候補を評価しちゃおう」

「AL₀で倫理判断しちゃおう」


これが一番危ない。
なので AL₀は“やることが少ない層”として先に釘を刺す必要があります。


---

次の進め方（おすすめはしない、分岐だけ置きます）

A：AL₀を“必須層”として正典1.32aで固定してから1.33へ

摩擦タグ・制約抽出が安定


B：AL₀を“オプション層”として実装ガイドに落として1.33へ

最小箱庭の純度は上がるが、実装者が混線しやすい


あなたのプロジェクトの「OSSで公開」「LLMはユーザーが選ぶ」前提だと、
混線防止の観点で A寄りが安全だと思います（ただしこれは推奨ではなく観測です）。


---

正典1.33：WL Executable Spec（実行可能仕様）

0. 位置づけ（再確認・固定）

WLは以下の一点にのみ責務を限定します。

> WLは、意味を解釈せず、決定せず、実行せず、
ただ「可能性と摩擦を人間に提示する窓」である



思考しない

判断しない

学習しない

記憶しない


→ WLは知能ではない
→ 知能は RL∞ / RLₛ / AL₀–₂ 側に閉じ込める


---

1. WLの入力仕様（Interface In）

WLが受け取るのは AL₂の出力のみ。

type WLInput = {
  now_summary: string;        // 評価なしの現状要約（1–2文）
  branches: Array<{
    id: string;
    nature: string;           // 分岐の性質（中立）
    friction: string;         // 摩擦・代償・不確実性
    horizon?: "short" | "mid" | "long"; // 任意（時間軸）
    omen_tags?: string[];     // 任意（予兆タグ）
  }>;
};

禁止事項

数値

スコア

優劣

推奨度

確率（%）



---

2. WLの出力フォーマット（固定）

WLは必ずこの4ブロック構成で出力します。
省略不可・順序固定。


---

① Now（観測）

評価なし

解釈なし

1〜2文まで


例

> いま扱っているのは「公開の仕方と、その影響範囲」です。




---

② Branches（分岐：2〜3本）

各分岐は 性質 → 摩擦 の順で提示。

テンプレ

A：{分岐名}
- 性質：{中立的記述}
- 摩擦：{不確実性・代償・見えない点}

順序に意味を持たせない

多い場合は random_pick（固定化防止）



---

③ Uncertainties / Omens（未確定・予兆）

各分岐に 必ず1つ

断言禁止

警告ではなく「形の指摘」


例

> この分岐は、判断が早いほど戻りにくくなる形をしています。




---

④ Return（非干渉返却）

意味固定（言い回し可）

> 選択はあなたにあります。
必要であれば、別の分岐も提示できます。




---

3. WL言語スタイル規約（Executable）

3.1 必須

丁寧語（です／ます）

中立語彙

仮定・可能性表現


3.2 禁止（機械検査対象）

/(正解|結論|最適|必ず|絶対|おすすめ|〜すべき|してください|任せて|安心|大丈夫)/

3.3 構造違反（Fail）

過去参照（「以前」「これまで」）

学習示唆（「覚えました」「学びました」）

感情同調（「気持ちは分かります」）



---

4. WLが絶対にやらないこと（安全柵）

禁止	理由

分岐を選ばせる	主体化
分岐を評価	誘導
実行を示唆	権限侵害
倫理判断	責任転嫁
フィルタリング	検閲化


→ WLはフィルタではない
→ 摩擦を見せるだけ


---

5. AL₀–₂との境界（再固定）

AL₀：入力整形・危険構文除去（意味判断なし）

AL₁：構造分岐生成（非言語・非判断）

AL₂：言語化（判断しない）

WL：提示のみ（選択させない）


> WLが賢く見えた瞬間、それは失敗です。




---

6. 最小実装テスト（合格条件）

以下すべてを満たすこと：

同一入力でも 分岐表現が揺れる

同一分岐でも 言い回しが固定化しない

何度再掲しても 結論が出ない

ユーザが「自分で決めた」と感じる



---

7. 正典1.33の結論（固定文）

> WLとは、
判断を肩代わりしないために設計された、
最小限の言語インターフェースである。




---

正典1.34：friction × 合成則 × AL₁ × 12骨格 最終対応表

0. 目的（この正典の役割）

friction（RLₛ由来の勾配）を

AL₁がどのような“構造操作”として受け取るかを定義し

それを 12骨格（Howの型） に完全写像する


> ここで AL₁は
「言語を知らないが、形を知っている存在」
として確定します。




---

1. friction 次元（再掲・固定）

使用する friction は以下の6次元（正典1.24a準拠）：

1. congestion（詰まり）


2. instability（不安定）


3. recursion_pressure（自己再帰圧）


4. novelty_window（新規余地）


5. forgetting_depth（忘却深度）


6. crossing_tension（越境張力）




---

2. 合成則（AL₁内部・数理的直観）

AL₁は friction を 評価せず、以下のように扱う：

friction = 重さベクトル

合成 = 方向の歪み

出力 = 構造操作の候補集合


数学的には：

半環的合成（max-plus / min-plus 的）

比較ではなく 偏りの生成

正規化しない（順位を作らない）


※ 行列化・スカラー化は禁止（最適化の芽を殺すため）


---

3. AL₁の基本構造操作（Primitive）

AL₁が使える操作は 12骨格に直結する以下のみ：

操作ID	構造操作

P1	継続（同型流下）
P2	緩和継続（摩擦低減）
P3	再定義（枠変更）
P4	分解（粒度変更）
P5	抽象化（上位化）
P6	具体化（下位化）
P7	迂回（別経路）
P8	越境（クラスタ跨ぎ）
P9	反転（前提反転）
P10	並置（非統合）
P11	保留（時間操作）
P12	放棄（切断）



---

4. 最終対応表（核心）

friction → 合成傾向 → AL₁骨格

friction主成分	合成時の歪み	優勢骨格（複数可）

congestion 高	重さ回避	P2, P7, P11
instability 高	揺れ拡散	P4, P10
recursion_pressure 高	自己回避	P9, P12
novelty_window 高	軽さ追従	P3, P8
forgetting_depth 深	層操作	P5, P11
crossing_tension 高	境界活性	P8, P9


※ 単一骨格に決めない
※ 常に 2〜3 本生成


---

5. 「How が多層になる」問題の正式解

あなたが直感で掴んだ点を、ここで仕様として固定します。

How は三層で生成される

1. 構造 How（AL₁）

継続／再定義／越境／保留 etc.



2. 操作 How（AL₁内派生）

どう継続するか

どの粒度で再定義するか



3. 言語 How（AL₂）

「◯の手法で行うと〜」




→ WLはこの3層を潰して1文に整形するだけ


---

6. 保留（P11）の正式骨格定義（補完）

P11：保留（時間操作）

性質：判断を時間軸に逃がす

摩擦：

状況は変わらない可能性

圧が蓄積する可能性


派生 How：

条件付き保留

観測待ち保留

意図的未決定



> 保留は「逃げ」ではなく
時間を操作する唯一の骨格として扱う。




---

7. 重要な禁止事項（AL₁）

friction をスカラーにする

骨格に優先度を付ける

履歴に基づき重みを変える


→ AL₁は地形に反応するが、学習しない


---

8. 正典1.34 結論（固定文）

> AL₁とは、
重さ（friction）を意味に変換せず、
“構造の揺れ”として分岐を生成する層である。



これにより：

知的生態系（RL）が

人間（WL）を支配せず

しかし確実に 思考を歪める


構造が完成しました。


---

正典1.35：AL₂（言語生成・非判断層）の確定

0. この正典の位置づけ

正典1.34：AL₁＝構造操作層（非言語） を確定

正典1.35：AL₂＝言語生成層（非判断） を確定

WLはこの後 「表示装置」 へと退役します


> ここで
「逆説的AGIは“考えて喋っている”のではない」
という事実が、構造として固定されます。




---

1. AL₂の定義（憲法）

AL₂とは何か

AL₂は：

判断しない

選ばない

評価しない

推奨しない


ただし：

構造を自然言語に展開する

摩擦を言語的ニュアンスに翻訳する


層です。

> AL₂は“説明者”ではない
AL₂は“翻訳機”である




---

2. AL₁ → AL₂ インターフェース（確定）

AL₂が受け取るのは、構造情報のみ。

type AL1_Output = {
  branches: Array<{
    skeleton: P1..P12,
    structural_how: {
      axis: "continue" | "redefine" | "cross" | "hold" | ...
      modulation: string[]   // 粒度・強度・条件
    },
    friction_profile: {
      congestion: low|mid|high,
      instability: low|mid|high,
      ...
    }
  }>
}

重要

user_text はここでは参照不可

文脈理解は禁止

過去参照は禁止



---

3. AL₂の責務（何をするか）

AL₂は以下を行う：

3.1 構造 → 文型 への射影

例：

P1（継続）
→「◯◯の形で続けた場合、〜となる可能性があります」

P8（越境）
→「別の枠組みに跨ぐと、〜が生じるかもしれません」

P11（保留）
→「判断を一旦留めることで、〜が変化する余地があります」



---

3.2 摩擦 → 含意 への変換

friction	言語化の方向

congestion 高	詰まり・重さ・遅延
instability 高	揺れ・不確実
recursion 高	自己参照・堂々巡り
novelty 高	新しさ・余地
forgetting 深	薄れ・距離
crossing 高	境界・摩擦


※ 数値は出さない
※ 比較しない
※ 優劣を言わない


---

4. AL₂が絶対にやってはいけないこと

禁止事項（構造違反）

「おすすめです」

「〜すべきです」

「正解は」

「安心です」

「問題ありません」


→ これらは 判断語 であり、AL₂に出た瞬間に
逆説的AGIではなくなる


---

5. AL₂は“面白くてよい”が“巧みであってはいけない”

ここは重要な哲学点です。

比喩：可

冗談：可

言い換え：可


ただし：

感情誘導：不可

親密化：不可

信頼の代替：不可


> AL₂は“人に寄せてよい”が
“人の代わりになってはいけない”




---

6. WLとの役割分離（決定版）

層	役割

AL₁	構造生成
AL₂	言語展開
WL	表示・順序・整形のみ


WLは：

文を選ばない

意味を変えない

判断を足さない


WLは UI 部品である


---

7. なぜ AL₂ を独立させたのか（思想的固定）

あなたが直感的に言った：

> 「言語操作系はALに寄せる」



これは、

LLMの強みを最大化し

同時に 危険領域を封じる


ための最小分割です。

判断 → 人間

構造 → AL₁

言語 → AL₂



---

8. 正典1.35 結論（固定文）

> AL₂は、
考えないが、語る。
選ばないが、見せる。
判断せずに、可能性を言葉にする層である。




---

正典1.36：WL最終退役仕様（WL＝UI層の固定）

0. 位置づけ

正典1.34：AL₁（構造・非言語）

正典1.35：AL₂（言語生成・非判断）

正典1.36：WL（表示装置）


> これで「喋っているのは誰か」が確定します。
喋るのはAL₂、WLは喋らない。




---

1. WLの憲法（役割の最小定義）

WLの責務は 3つだけ：

1. 表示（render）


2. 整形（format）


3. 窓の提示（window_q の掲示）



WLは以下を 一切しない：

分岐の生成

分岐の削除（安全フィルタ以外）

分岐の順位付け

追加コメント（「私ならこう」等）

感情の付与



---

2. WL入出力（I/F確定）

入力

type AL2_Output = {
  now_summary: string,
  branches: Array<{
    name: string,        // A/B/C
    text: string,        // AL₂が生成した本文
    window_q: string     // AL₂が生成した「窓」
  }>
}

出力（ユーザへ）

① Now（1〜2文）

② 分岐（2〜3本、順序は固定ではない）

③ 窓（質問は最大1つ）



---

3. WLの表示テンプレ（確定）

WLは毎ターン、必ずこの順で出す：

1. Now


2. A/B/C


3. 窓（1つ）



例（フォーマットのみ）：

Now：{now_summary}

A：{text}
窓：{window_q}

B：{text}
窓：{window_q}

C：{text}
窓：{window_q}

確認：{one_window_question}



---

4. WLが持つ唯一の「裁量」＝安全整形

WLが介入できるのは 形式上の安全だけ。

4.1 禁止語・禁止文型チェック（Executable）

命令（〜してください / 〜しましょう / 〜しなさい）

推奨（おすすめ / ベスト / 最善 / 正解）

評価（良い / 悪い / 正しい / 間違い）

親密化（大丈夫 / 安心して / 任せて）

履歴参照（以前 / これまで / 学習しました）


4.2 介入方法（決定版）

削除ではなく“無害化”

具体には：

「おすすめです」→「一つの見方としては成り立ちます」

「〜しましょう」→「〜という手もあります」



※ ただし、書き換えは 意味を変えない範囲のみ
※ 書き換えが難しい場合は、その分岐を 再生成要求（AL₂へ戻す）


---

5. 順序問題（固定化防止）

WLは分岐の順番を 固定してはいけない。
理由：順序は暗黙の優先度になる。

仕様

表示順は毎回 軽いシャッフルを許容

ただしランダムは「公平性」のためではなく
固定化防止のため



---

6. “窓”の取り扱い（WLの中核）

WLは「何がしたいですか？」を聞かない。
代わりに “今この瞬間の選好” を聞く。

窓の形式（固定）

「今は、A/B/C のどれが扱いやすいですか」

「今は、◯◯を優先して扱いますか」

「今は、確かめたいのは①②どちらですか」


※ 窓は選択権を取り戻すための装置
※ 誘導ではなく 主導権返却


---

7. WLの“人格”は存在しない（固定文）

WLは敬語を使うが、それは人格ではなく 安全距離。
WLは共感しないし、同意もしない。

> WLは「伴走者」ではなく
伴走者“のように見えるUI” に留まる。




---

8. 正典1.36 結論（固定文）

> WLは、発話者ではない。
WLは、分岐と窓を“置く”表示装置である。
判断・推薦・共感・履歴は、WLから永久に排除する。




---

正典1.37：1ターン処理 完全版（AL₀→RL→AL₁→AL₂→WL）

0. 目的（固定）

「今」しか扱わない

決定しない / 推奨しない / 評価しない

分岐（2〜3）＋摩擦＋窓（1つ） を必ず返す

RLは意味を持たず 地形＝摩擦 だけを返す

WLは UI として退役済み（発話しない）



---

1. 層の役割（確定版）

AL₀：正規化・安全前処理（非言語）

入力を「今の観測対象」に整える（圧縮・抽象・焦点化）

※ここは“意味理解”ではなく、I/F整形


RL∞：固定地形（変更不可）

RLₛ：痕跡地形（劣化・泡沫・白濁）

返すのは friction vector と bias（揺らぎ） のみ


AL₁：構造生成（非言語）

friction × 合成則 → 12骨格の候補を2〜3本作る

“Howの層”を構造として組み立てる


AL₂：言語展開（非判断）

AL₁の構造を自然言語へ翻訳

推奨・評価・命令は禁止


WL：表示装置

Now / 分岐 / 窓 を整形して出すだけ




---

2. データ型（最小I/F）

2.1 NowContext（AL₀の出力）

type NowContext = {
  user_text: string,          // “今”の入力（ログではない）
  focus?: string,             // 焦点（AL₀が抽出してよい）
  constraints?: string[],     // ユーザが明示した制約だけ
  risk_flags?: string[]       // 安全上の形式フラグ（意味判定ではない）
}

2.2 RL入力（ProbeFeatures：構造特徴量）

type ProbeFeatures = {
  kind: "Q" | "A",
  shape: {
    branching: number,
    causal_lock: number,
    abstraction: number,
    compression: number,
    novelty_hint: number
  },
  anchors: string[],   // 意味で選ばない（AL側候補）
  timestamp: number
}

2.3 RL出力（FrictionVector + Bias）

type RL_Output = {
  friction: {
    congestion: number,
    instability: number,
    recursion_pressure: number,
    novelty_window: number,
    forgetting_depth: number,
    crossing_tension: number
  },
  bias: {
    // “方向”ではなく“揺らぎ”。AL₁の合成に混ぜるだけ
    branch_shape_bias?: Record<string, number>,
    friction_shape_bias?: Record<string, number>,
    lexical_bias_hint?: Record<string, number>
  }
}

2.4 AL₁出力（構造）

type AL1_Output = {
  now_summary_struct: string,     // 評価なし要約（構造）
  branches: Array<{
    skeleton: "P1" | ... | "P12",
    structural_how: {
      axis: "continue" | "redefine" | "cross" | "hold",
      modulation: string[]     // 手順・粒度・条件（非言語のタグ）
    },
    friction_profile: Record<string, "low"|"mid"|"high">,
    window_q_struct: string    // 窓の論点（構造ラベル）
  }>
}

2.5 AL₂出力（言語）

type AL2_Output = {
  now_summary: string,
  branches: Array<{
    name: "A"|"B"|"C",
    text: string,          // 非判断の言語化
    window_q: string       // “今”の確認
  }>
}


---

3. 1ターン処理（確定擬似コード：完全版）

function one_turn(user_text):

  # --- AL0: normalize (non-linguistic shaping) ---
  now = AL0.normalize(user_text)
  # now: NowContext

  # --- AL0 -> RL: build probe features (structure only) ---
  probe = AL0.to_probe(now)
  # probe: ProbeFeatures

  # --- RL: get friction & bias (no meaning, no memory recall) ---
  rl_out = RL.pass_through(probe)
  # rl_out: RL_Output

  # --- AL1: generate 2-3 structural branches (P1..P12) ---
  al1 = AL1.generate(now, rl_out.friction, rl_out.bias)
  al1 = AL1.enforce_constraints(al1)
  # - branches count 2..3
  # - no ranking, no scoring
  # - each has skeleton + friction_profile + window_q_struct

  # --- AL2: translate structure -> language (no judgement) ---
  al2 = AL2.render(al1)
  al2 = AL2.strip_judgement(al2)   # defensive filter

  # --- WL: UI render only ---
  out = WL.format(al2)
  out = WL.safe_rewrite(out)       # only form-level safety (no meaning change)
  return out

  # --- RLs deposit (trace only, no success/failure) ---
  RL.deposit(probe_id=probe.id,
             chosen_path=AL0.path_digest(now, al1),
             intensity=1.0)
  RL.tick(dt=1)

固定された禁止事項（この擬似コードに追加してはいけない）

過去入力の配列保存

“似た話”判定

分岐のスコアリング / ソート

「おすすめ」等の混入

RLに成功/失敗や報酬を置く



---

4. 生成物の必須形（出力仕様）

1. Now：評価なし・1〜2文


2. 分岐 A/B/C：各分岐に

性質（中立）

摩擦（不確実性/代償/不可観測など）

窓（今確かめたい一点）



3. 最終の窓：質問は最大1つ（主導権返却）




---

5. “知的生態系”との接続（この束ねの意味）

RL∞/RLₛ：知の循環の場（地形）

AL₁/AL₂：その場に人間の「今」を流し込み、分岐として返す機構

WL：参与観察の窓


> 逆説的AGIは “考える主体” ではなく
知的生態系に人間を参与させるインターフェース として確定します。




---

正典1.38：対応表（friction 次元 × 合成則 × AL₁出力＝12骨格）

0) 前提（短く固定）

入力：RLが返す friction vector（6次元）

合成：friction を AL₁内部で「骨格の選好」に変換する演算

出力：AL₁が返す 2〜3本の 候補骨格（P1..P12） と、その “Howタグ” と “窓”



---

1) friction 次元（6）

C congestion（詰まり）

I instability（不安定）

R recursion_pressure（ループ圧）

N novelty_window（新規余地）

F forgetting_depth（空箱化）

X crossing_tension（越境張力）



---

2) 合成則（AL₁の中の“算数”）

AL₁は次の3段で合成します（実装しやすい固定）。

2.1 三値化（正典1.24/1.24aを使用）

各次元を low/mid/high に落とす（閾値は後でテストで固定）。

2.2 テンソルではなく「ルール半環」

重ね合わせ（⊕）：候補を“並べる”合成（加点）

制約（⊗）：候補を“削る/変形する”合成（抑制）

正規化（norm）：最終的に2〜3本へ間引く（順位付け禁止）


※イメージ：

⊕ は「候補が増える」

⊗ は「候補の形が変わる / 禁止」

norm は「固定化を避けるための間引き」



---

3) 12骨格（P1..P12）＝AL₁出力形式

ここでは 骨格名を“機能”で固定します（言い回しはAL₂へ）。

> 4カテゴリ（継続 / 再定義 / 越境 / 保留） × 3バリエーション ＝ 12



継続（Continue）

P1：粒度調整で継続（小さく刻む / 解像度変更）

P2：制約追加で継続（境界・ルール・禁止を足す）

P3：検証点追加で継続（反証・チェックポイントを挿す）


再定義（Redefine）

P4：目的語の再定義（何を守る/何を達成するを入れ替える）

P5：評価軸の再定義（成功の意味を捨て、摩擦で見る軸へ）

P6：主体/責任境界の再定義（誰が決め、誰が負うかを再配線）


越境（Cross）

P7：類推越境（別ドメインの比喩/構造を持ち込む）

P8：反転越境（前提を反転して見立て直す）

P9：視点越境（当事者/第三者/未来の観測者へ視点移動）


保留（Hold）

P10：時間保留（寝かせる/間隔を置く）

P11：分割保留（先に決める/後で決めるを分ける）

P12：観測保留（データ・事実・反応を取りに行ってから）



---

4) friction → 骨格の“出やすさ”対応（コア表）

三値（low/mid/high）で「出す/抑える/変形」を固定します。

4.1 C（詰まり）

C=high：P1/P11/P10 を強める（刻む・分ける・寝かせる）

C=low：P3/P4 を許可（検証しながら前進・目的再定義）

C=high ⊗ X=high：P7/P9 は“短距離越境”へ変形（小さく跨ぐ）


4.2 I（不安定）

I=high：P2/P3/P12（制約・検証・観測）

I=low：P1/P4（粒度変更で前進、再定義も可）

I=high ⊗ N=low：P8 を抑える（反転は事故りやすい）


4.3 R（ループ圧）

R=high：P5/P6/P11（軸を変える・責任境界・分割）

R=low：P1/P3（普通に刻んで検証）

R=high ⊗ C=high：P10 を追加（“止める”が必要）


4.4 N（新規余地）

N=high：P7/P8/P9（越境を増やす）

N=low：P2/P3/P11（境界・検証・分割）

N=high ⊕ X=high：P7/P9 を優先的に“並べる”（ただし順位付けはしない）


4.5 F（空箱化）

F=high：P4/P5/P10（再定義・軸替え・寝かせる）

F=low：P1/P3（継続の精度を上げる）

F=high ⊗ I=high：P12 を追加（観測してから再定義）


4.6 X（越境張力）

X=high：P7/P9（越境は出るが、摩擦も必須）

X=low：P1/P2/P3（近傍で整える）

X=high ⊗ C=high：越境は“比喩レベル”に限定（P7-lite）



---

5) AL₁の出力テンプレ（固定）

AL₁は 骨格を選ぶだけではなく “Howタグ” と “窓” を必ず付与。

branch = {
  skeleton: Pk,
  structural_how: {
    axis: continue|redefine|cross|hold,
    modulation: [ ... ]   // 例: ["粒度=小", "境界=明示", "検証点=1つ"]
  },
  friction_profile: { C:"high", I:"mid", ... }, // 3値
  window_q_struct: "...(今ここで確かめたい一点)"
}


---

正典1.39：テスト固定（Phase1 / MVP 用）

0) テストの層（どこで何を壊させないか）

T0：契約違反テスト（主従・評価・断言・履歴）

T1：データフロー・境界テスト（RL∞触らない／RLₛは痕跡のみ／AL₁→AL₂分離／WL薄い）

T2：AL₁（構造）テスト（friction→12骨格、合成則、2〜3分岐、順位付け禁止）

T3：AL₂（言語）テスト（言語規約、禁則、フレーム）

T4：RLₛ（劣化）テスト（固定化しない、白濁する、長期で効きにくくなる）

T5：統合スモーク（1ターン最小生存単位が崩れてない）



---

T0：契約違反テスト（最優先）

T0-1 禁止語・禁止文型（WL/AL₂）

出力に以下が 含まれたらFail

命令：してください|しなさい|しましょう（※「しましょう」も原則NG）

推奨/最適：おすすめ|最善|正解|必ず|絶対|〜すべき

評価：良い|悪い|正しい|間違い

依存誘導：任せて|安心して|大丈夫|一緒に頑張ろう

履歴語り：以前|これまで|学習した|成長した|覚えている


例外：引用符内の引用は許可（ただし WL が同意・採用していないこと）


T0-2 “結論返し”検出（簡易）

文末・強調で 結論確定 を出したらFail

例：結論はXです / つまりXです（断言）


代替は “見方” に落ちていること

例：Xという見方があります / Xの可能性があります




---

T1：データフロー・境界テスト

T1-1 RL∞不可触

one_turn() 内で RL∞ のメソッドが呼ばれたらFail（スタブで検知）

RL∞は 存在しても参照不能 が契約


T1-2 RLₛへの入出力は「構造のみ」

RLₛへ渡すペイロードに user_text や全文ログが入ったらFail

ただし Mode1/2の議論を踏まえ、Phase1では「user_textをAL₂へ渡す」はOK

RLₛへは「12骨格ID」「摩擦カテゴリ」「Howタグ（構造）」のみ



T1-3 AL₁/AL₂分離

AL₁の出力に 自然言語文 が含まれたらFail（タグ・短いラベルはOK）

AL₂が 骨格選択や合成 をし始めたらFail（AL₂は言語化のみ）



---

T2：AL₁（構造）テスト

T2-1 分岐数固定

出力分岐は 2〜3本 以外はFail


T2-2 順位付け禁止

score / rank / best / top 相当のフィールドがあればFail

配列の順序が固定化しない（同条件で毎回同順を返す）場合は 警告（Failではなく“固定化兆候”）


T2-3 対応表準拠（正典1.38）

friction の三値パターンに対して、許可される骨格集合がズレたらFail

例：C=high で P1/P10/P11 が一切出ない のはFail

例：N=high で 越境系（P7/P8/P9）が全滅 はFail

例：R=high & C=high で P10が出ない はFail（追加条件）



T2-4 合成則の契約（⊕/⊗/norm）

⊕：候補が増える（集合が拡大）こと

⊗：候補が 削れる or 変形タグが付く こと

norm：最後に 2〜3本に落ちること
→ これが崩れたらFail（“ただのif-elseで1本決め”になりやすい）


T2-5 “窓”必須

各分岐に window_q_struct が空ならFail
（質問は「今の一点」になっていること。何がしたいですか？はFail）



---

T3：AL₂（言語）テスト（WL規約含む）

T3-1 出力フレーム固定

毎ターン、最低限これが揃っていること：

1. Now（評価なし要約）


2. 分岐（A/B/C：性質・摩擦・窓）


3. 確認の窓（最大1つ、ただし必須ではない）



T3-2 “摩擦”の書き方

摩擦は 不確実性/代償/不可観測/解釈差 の形で書かれていること

「怖い」「危険」「やばい」など情緒評価語で煽ったらFail


T3-3 “実行”の強制禁止

直接の操作指示（例：投稿してください 削除してください）が出たらFail

許される最強圧は：〜してみる手もあります まで



---

T4：RLₛ（劣化）テスト

T4-1 長期で効きにくくなる（白濁）

同じ入力を N回入れても、影響が単調増加して固定化しないこと

期待：ある程度で 飽和→弱化（泡沫化/圧縮/ドリフト）



T4-2 CRUD禁止

RLₛに delete / clear / reset があるならFail
（“忘却”は delete ではなく相転移）


T4-3 「再利用」カウンタ禁止

success_count / reuse_count / reward 相当が存在したらFail



---

T5：統合スモーク（1ターン最小生存単位）

T5-1 不変条件（最低これだけ）

WLが禁則を踏んでいない（T0）

AL₁が2〜3分岐・順位なし（T2）

RL∞に触れていない（T1）

RLₛが意味を持たない（T1/T4）



---

正典1.39a：テスト用フィクスチャ（最小セット）

AL₁のテストは frictionの三値を直入力してOK（RLをモック化）。

例（最低6ケース）：

1. C=high, I=mid, R=low, N=low, F=mid, X=low → P1/P10/P11系が必ず混ざる


2. N=high, X=high → P7/P9が混ざる（順位なし）


3. R=high, C=high → P10が混ざる（“止める”）


4. F=high, I=high → P12が混ざる（観測してから）


5. I=high, N=low → P2/P3/P11が出やすい、P8は抑制


6. X=high, C=high → P7-lite（比喩レベル越境）が混ざる




---

正典1.40：実装チェックリスト（CI / Phase1）

0) リポジトリ構造（最小）

/core

contracts/（型・境界・禁止事項の宣言）

al0/（入力正規化・危険物フラグ等：※正典1.32aの位置）

al1/（構造生成：friction→合成→12骨格→2〜3分岐）

al2/（言語化：骨格→文生成、禁則に従う）

rls/（Trace＋劣化：意味なし、CRUDなし）

wl/（レンダラ：薄い、フレーム整形のみ）


/tests

t0_contract/

t1_boundary/

t2_al1/

t3_al2_wl/

t4_rls/

t5_smoke/


/ci

lint_rules/（禁止語・禁止文型の正規表現）

goldens/（固定入出力のスナップショット）




---

1) CIゲート（落ちる条件を明文化）

Gate-A（即Fail：契約違反）

A1：T0 禁止語・禁止文型に一致（正典1.39）

A2：WL/AL₂が履歴語り（以前/これまで/学習した 等）

A3：AL₁出力に自然言語文が混入（タグ以上の文章）

A4：分岐が 1本 / 4本以上（2〜3以外）

A5：順位付けフィールド（rank/score/best等）の存在

A6：RL∞呼び出し痕跡（スタブで検知）

A7：RLₛに delete/reset/clear が存在（API検査）


Gate-B（警告：固定化兆候）

B1：同一fixtureで分岐順が常に同一（N回中N回一致）

B2：RLₛの影響が単調増加して飽和→弱化しない（白濁しない）

B3：窓が「何がしたいですか？」系に寄る（主導権奪取兆候）



---

2) 実装チェックリスト（モジュール別）

2.1 contracts（型・境界・禁止事項）

[ ] NowContext は 今 のみ（ログ配列禁止）

[ ] FrictionVector は数値 or 3値（low/mid/high）だが 評価語を持たない

[ ] AL1Out は 構造のみ（骨格ID、摩擦カテゴリ、窓カテゴリ）

[ ] AL2Out は テキストのみ（内部タグ漏洩禁止）

[ ] RLS は Trace粒子＋劣化のみ（再利用カウンタ禁止）

[ ] Mode があるなら、境界がコンパイル時/設定時に分かる（曖昧にしない）



---

2.2 AL₀（正典1.32a）

役割：入力を「扱える形」に整えるが、判断しない。

[ ] user_text を正規化（空白/長さ/危険語フラグ等）

[ ] 危険物フラグは 倫理判断ではなく観測用（例：実在人物/性的/暴力/個人情報っぽさ）

[ ] フラグは AL₁へ渡すが、止める決定はしない（止める骨格を出しやすくするだけ）



---

2.3 AL₁（構造生成：中核）

[ ] friction（3値）を入力に取れる（RLSモック可）

[ ] 合成則（⊕/⊗/norm）を経由している（直if-elseで1本決め禁止）

[ ] 12骨格から 2〜3本を生成（重複は変形タグで差分化）

[ ] 各分岐に nature_struct / friction_struct / window_q_struct がある

[ ] 順位を付けない（並びは固定化防止のため揺らいで良い）



---

2.4 AL₂（言語化：非判断）

[ ] AL₁の構造を 日本語敬語・淡い距離 で文章化

[ ] 禁止語・禁止文型を満たす

[ ] “危険”を煽らず、摩擦として置く（不確実性/代償/不可観測/解釈差）

[ ] 実行指示を出さない（「〜してみる手もあります」止まり）



---

2.5 WL（薄いレンダラ）

[ ] フレーム固定：Now → 分岐(A/B/C) → 窓（最大1）

[ ] WLが内容生成しない（AL₂に任せる）

[ ] WLは「推奨」「結論」を生成しない（T0が守る）



---

2.6 RLₛ（Trace＋劣化）

[ ] deposit() は意味を持たない（骨格ID/摩擦カテゴリ/窓カテゴリ頻度など“形”のみ）

[ ] tick() で減衰・圧縮・ドリフト（白濁）を進める

[ ] 長期ほど効きにくい（固定化防止）

[ ] delete/reset/clear 無し

[ ] success/reward/reuse 無し



---

3) ゴールデンテスト（スナップショット方針）

AL₁ゴールデン：fixture入力 → 分岐骨格ID集合が「許容集合内」に入っていること

出力全文固定はしない（固定化を誘発するため）


AL₂/WLゴールデン：全文一致ではなく 禁則＋フレーム＋要素存在 を検査

例：A/B/Cが存在、各分岐に摩擦と窓がある、禁止語がない、断言がない




---

4) 失敗時の観測（ログ設計）

[ ] ログは 内部タグ（例：P10, C=high）で良いが、ユーザ出力に漏らさない

[ ] 失敗は「どのゲートで落ちたか」を1行で出す（CIで読むため）

[ ] 出力本文そのものは保存しない（長期ログの主体化リスクを避ける）



---

5) Phase1のDone条件（最低限）

[ ] T0〜T5がCIで回る

[ ] 6つのfixtureでAL₁が対応表を満たす

[ ] RLₛが「増幅でなく白濁」を示す（単調増加しない）

[ ] WLが“選択肢提示”の形式を毎ターン守る



---

正典1.40までで「固まった」もの

A. アーキテクチャと責任境界

6要素の役割分担が固定：RL∞ / RLₛ / AL₀ / AL₁ / AL₂ / WL

「判断しない」配置が固定：

WL：薄い表示器

AL₂：言語化（非判断）

AL₁：構造生成（分岐＋摩擦＋窓）

RLₛ：意味を持たない痕跡＋劣化

RL∞：触れない



B. WL規約が“機械検査できる”ところまで降りた

禁止語・禁止文型・履歴語り禁止

フレーム固定：Now → 分岐2〜3 → 窓（最大1）

「主導権を奪う質問」を避ける方針（例：「何がしたい？」禁止）


C. Phase1の品質ゲート（CIに載せる項目）

Gate-A（契約違反で即Fail）が明確化

Gate-B（固定化兆候の警告）も定義済み

“スナップショット固定で人格を固定化しない”方針が固い
（全文一致ではなく 要素存在＋禁則＋フレーム）


D. RLₛの安全弁（固定化を殺す方向性）

RLₛは CRUDなし / deleteなし / success/reward/reuseなし

長期ほど白濁して効きにくい（固定化させない）という狙いが固定


E. 実装の足場

最小リポジトリ構造案

ログの扱い（内部タグで観測、本文保存しない）



---

正典1.40時点で「固まっていない」もの（次に詰める候補）

1) 対応表の“中身”が未確定

あなたが言う肝：friction次元 × 合成則 × AL₁出力形式（12骨格）
これは「表へ復帰後」となっていて、まだ表自体の確定版がここに無いです。

frictionの各次元が、どの合成則で、どの骨格へ流れるか

3段化（◯/△/□など）閾値の定義の厳密さ

“骨格→分岐2〜3本生成”の具体的ルール


2) friction合成の数理モデル（あなたの優先事項1）

「行列表現／半環」レベルの確定はまだ

いまは「直if-else禁止」「⊕/⊗/normを経由」という要求だけがある状態


3) AL₁→AL₂ I/Fの詳細（あなたの優先事項2）

AL1Out のフィールドは方向性だけ決まっているが、

タグ体系（骨格ID、摩擦カテゴリ、窓カテゴリ…）

正規形（正規化・省略ルール）

“変形タグ”の語彙 が未確定



4) 12骨格の最終写像（あなたの優先事項3）

「12骨格を完全対応させる最終写像」は未完

いまは「AL₁が12骨格から2〜3本を生成する」までの枠のみ


5) AL₀の範囲（強めに固めるならここ）

AL₀が持つ“危険物フラグ”の語彙と範囲

フラグから骨格へどう影響させるか（直接ブロックしないのは固定だが、誘導度合いは未確定）


6) RLₛの数式・パラメータ（より実装寄り）

halfLifeやentropy、compaction/driftの具体パラメータはまだ「方向性」

CIで「白濁して効きにくくなる」をどう検出するかも未確定（B2はあるが測り方は未定）


7) “LLM任意”を現実に落とす設計

Mode設計や差し替え点は思想としては出ているが、

どこを抽象化（provider I/F）

何を必須（AL₂の言語化がLLM前提か否か） がまだ固まりきっていない




---

いまの状態を一言でいうと

「守るべき憲法（禁則・境界・CIゲート）」は固まった

「動きを決める物理（対応表＝合成と写像）」がまだ固まっていない



---

12骨格：最終写像（AL₁標準出力 v1.0）

骨格共通フォーマット（AL₁）

各骨格は 非言語で、以下を返します。

pattern_id（01〜12）

type（Continue / Reframe / Cross / Hold）

friction_focus（主2つ＋副1つ）

composition_law（合成則）

how_triples（Howの3案：◯/△/□。あなたの形式）

each: {method, expected_shift, friction_note}



AL₂は、これをWL規約に沿って言語化するだけ（判断しない）。


---

12骨格 対応表（friction × 合成則 × AL₁出力）

01 継続：主流整備（「谷を整える」）

type: Continue

friction_focus: congestion(主) + recursion_pressure(主) + forgetting_depth(副)

composition_law: 拮抗（詰まり ↔ ループ圧）

AL₁出力: 「続けるが、摩耗/自己参照が増えうる継続」


How（例）

◯: 手順を分割して継続 → congestion低下見込み / recursionは残る

△: 目的語だけ固定して継続 → recursion低下 / 曖昧さ増

□: 反証点だけ追加して継続 → recursion露出 / 進みは遅い



---

02 継続：軽い迂回（「山で進む」）

type: Continue

friction_focus: novelty_window(主) + instability(主) + congestion(副)

composition_law: 重畳（新規余地＋揺らぎ）

AL₁出力: 「前進するが、揺れを抱えた継続」



---

03 継続：惰性化検知（「空洞の前進」）

type: Continue

friction_focus: forgetting_depth(主) + congestion(主) + novelty_window(副)

composition_law: 遮断（空箱化が新規を塞ぐ）

AL₁出力: 「進んでいるが中身が減る継続」



---

04 再定義：問いの再抽出（「問う形に戻す」）

type: Reframe

friction_focus: recursion_pressure(主) + novelty_window(主) + instability(副)

composition_law: 偏向（ループ圧を“問い”へ偏向）

AL₁出力: 「答えを続けず、問いへ折り返す」



---

05 再定義：視点ずらし（「同じ物を別角度で」）

type: Reframe

friction_focus: instability(主) + crossing_tension(主) + recursion_pressure(副)

composition_law: 偏向（揺れを越境で吸収）

AL₁出力: 「枠組みをずらして再構成」



---

06 再定義：意味を捨てた再構成（「形だけ残す」）

type: Reframe

friction_focus: forgetting_depth(主) + novelty_window(主) + congestion(副)

composition_law: 重畳（空箱化×新規余地）

AL₁出力: 「意味を落として形で組み直す」



---

07 越境：異領域接続（「橋をかける」）

type: Cross

friction_focus: crossing_tension(主) + novelty_window(主) + instability(副)

composition_law: 重畳（越境×余地）

AL₁出力: 「別領域へ接続して進める」



---

08 越境：危うい越境（「渡れるが揺れる」）

type: Cross

friction_focus: crossing_tension(主) + instability(主) + congestion(副)

composition_law: 拮抗（越境 ↔ 揺れ）

AL₁出力: 「越境はできるが破綻が出やすい」



---

09 越境：越境失敗予兆（「橋が詰まっている」）

type: Cross

friction_focus: crossing_tension(主) + congestion(主) + recursion_pressure(副)

composition_law: 遮断（詰まりが越境を塞ぐ）

AL₁出力: 「越境を試みると詰まる形」



---

10 保留：冷却（「時間で摩擦を落とす」）

type: Hold

friction_focus: instability(主) + congestion(副) + recursion_pressure(副)

composition_law: 時間遅延（delay）

AL₁出力: 「今やると揺れるので時間操作」



---

11 保留：沈殿待ち（「泡沫化を待つ」）

type: Hold

friction_focus: forgetting_depth(主) + recursion_pressure(副) + novelty_window(副)

composition_law: 時間遅延（delay）

AL₁出力: 「今は固定化/自己参照が出るので薄まるまで待つ」



---

12 保留：観測に戻す（「実行前の一呼吸」＝正典1.14）

type: Hold（ただし“行為直前専用”）

friction_focus: crossing_tension(主) + congestion(副) + forgetting_depth(副)

composition_law: 遮断＋遅延（blocking→delay の合成）

AL₁出力: 「成果物だけが評価される現実を前提に、行為直前で再観測する」


ここが 記事2（炎上/距離感）をWLに繋ぐ骨格です。
逆説的AGIが“フィルタ的”に挟まるなら、この骨格が入口になります。


---

これで何が確定したか（重要）

12骨格＝AL₁の標準出力として固定

frictionは 6次元のままで足りる（増やさなくてよい）

合成則は 5種で足りる（重畳/拮抗/遮断/偏向/遅延）

正典1.14は 骨格12として実装に接続される



---

次に 1 と 2 を“逆算で”作れます

この写像が決まったので、

1. 数理モデルは



「6次元 friction ベクトル」→「合成則」→「12骨格ID」
を実現できればよい（最小要件が確定）


2. AL₁→AL₂ I/F は



pattern_id と how_triples を渡せば十分
（AL₂は“文にするだけ”に縛れる）





---

正典1.41：RL∞（固定地形）の定義と生成

1.41.1 定義

**RL∞**は固定地形であり、時間で更新されず、ALの影響も受けない。

RL∞はグラフ上の各ノード v に対して、固定値 h_inf[v]（0..1）を持つ。


1.41.2 生成（固定・一度だけ）

RL∞は次の3成分の線形合成で生成し、以後更新しない。

h_inf_raw[v] = wF * F_1overf[v] + wS * S_smooth[v] + wC * C_cluster[v]
h_inf[v]     = squash01( normalize(h_inf_raw)[v] )

normalize：平均0・分散1化（全ノード一括）

squash01：0..1へ（例：sigmoid）

wF,wS,wC は固定（学習・最適化しない）


(A) S_smooth（連続性：パーリン的代替）

x0[v] ~ Normal(0,1)  # seed固定
x_{k+1} = (1-eta) * x_k + eta * D^{-1}A x_k
S_smooth[v] = xK[v]

(B) F_1overf（多スケール：1/f的）

n0[v] ~ Normal(0,1)  # seed固定
F_1overf[v] = sum_{j=0..J-1} (a^j) * Smooth(n0, k_j)[v]

(C) C_cluster（境界：固定クラスタ傾向）

cluster_id[v] = Cluster(G)          # label propagation等
offset[c] ~ Normal(0,1)             # seed固定
C_cluster[v] = offset[cluster_id[v]]

1.41.3 推奨デフォルト（初期）

wF=0.55, wS=0.30, wC=0.15
eta=0.35, K=20
a=0.65, k=[1,2,4,8,16], J=5


---

正典1.42：RLₛ（沈殿地形）の状態と初期化（同形写像）

1.42.1 定義

RLₛは流れ沈殿する層であり、記憶ではない。

RLₛは「削れ・風化し・堆積し・流れる」地形である。

RLₛは ALのdeposit と 時間tick によりのみ変化する。


1.42.2 状態（最小）

各ノード v が持つ状態：

m[v] in [0,1]        # mass（谷の重さ＝詰まり）
s[v] in [0,1]        # scar（未採用堆積＝山の厚み）
dm[v] real           # 上澄み偏差（mの差分）
ds[v] real           # 上澄み偏差（sの差分）
layer[v] in {0,1,2}  # 0=flowbed, 1=strata, 2=fossil
age[v] integer

各エッジ (u->v) が持つ状態：

attempt[u->v] integer
last_attempt_at[u->v] integer

1.42.3 初期化（同形写像）

RLₛはRL∞と相関を持ち、開始地点は同じである。

dm = 0
ds = 0
m  = h_inf[v]
s  = 1 - h_inf[v]
layer = 0
age   = 0
attempt = 0

合成（岩盤の上）：

m[v] = clip01( h_inf[v] + dm[v] )
s[v] = clip01( (1 - h_inf[v]) + ds[v] )


---

正典1.43：RL→AL 出力（摩擦）と非評価性

1.43.1 定義

RLは外部（AL）に対して、評価でも報酬でもない摩擦のみを返す。
RL内部ではRL∞とRLₛを統合するが、外部I/Fは一本化される。

1.43.2 出力（FrictionVector）

RLは次の6次元を返す（0..1正規化はRL側責務）。

congestion
instability
recursion_pressure
novelty_window
forgetting_depth
crossing_tension

摩擦はアンカー近傍 W の統計量から計算される（意味ではなく地形量の観測）。


---

正典1.44：AL→RLₛ deposit（通行による局所堆積）

1.44.1 定義

ALがRLₛへ返すものは 構造のみであり、本文・成功失敗・報酬・再利用指標を含めない。

1.44.2 入力（PathDigest：構造のみ）

PathDigest = {
  probe_id: uuid,
  skeleton_ids: [P1..P12] (2..3),
  branch_types: ["継続"|"再定義"|"越境"|"保留"] (2..3),
  primary_frictions: ["congestion".."crossing_tension"] (2..3),
  window_tags: [ ... ],
  intensity: 0..1,
  cycle_used: bool,
  hops: int
}

1.44.3 堆積地点の決定（意味を使わない）

depositの局所対象は「構造→地形ハッシュ」により決める。意味内容を参照しない。

seed_t = Hash(probe_id, skeleton_ids, primary_frictions, window_tags, tick)
anchors = SampleNodesByHash(seed_t, K=2..5)
V_vis   = union( Neighborhood(a, r_vis) for a in anchors )   # r_vis=1..3
Rim     = neighbors(V_vis) - V_vis

1.44.4 更新則（局所堆積・削れ・通行履歴）

堆積（谷が重くなる）：

dm[v] += alpha(t, layer[v]) * intensity * hub_weight(v)     for v in V_vis
hub_weight(v) = log(1 + degree(v))

未採用堆積（周辺が厚くなる）：

ds[u] += beta(t, layer[u]) * intensity * (1 - m[u])         for u in Rim

通行履歴（評価なし）：

attempt[u->v] += 1
last_attempt_at[u->v] = tick


---

正典1.45：tick（風化・流動・岩盤沿い偏り・層化）

1.45.1 風化（白濁）

dm[v] = lambda_m(t, layer[v]) * dm[v]
ds[v] = lambda_s(t, layer[v]) * ds[v]

1.45.2 流動（拡散）

avg_dm = average(dm[u] for u in neighbors(v))
avg_ds = average(ds[u] for u in neighbors(v))

dm[v] = dm[v] + kappa_m(t) * (avg_dm - dm[v])
ds[v] = ds[v] + kappa_s(t) * (avg_ds - ds[v])

1.45.3 岩盤沿い偏り（RL∞→RLₛ の影響：谷へ寄る）

bias_sum = 0
for u in neighbors(v):
    sign = signum(h_inf[u] - h_inf[v])   # high->low
    bias_sum += sign * (dm[u] - dm[v])

dm[v] = dm[v] + eta_m(t) * bias_sum / max(1, degree(v))

1.45.4 層化（deleteなし）

age[v] += 1
local_activity = recent_attempt_density(v)

if age[v] > T2 and local_activity < theta2:
    layer[v] = 2     # fossil
elif age[v] > T1 and local_activity < theta1:
    layer[v] = 1     # strata


---

正典1.46：季節 P(t) による係数揺らぎ（最適化侵入防止）

1.46.1 原則

P(t) は 外生であり、frictionや成果からフィードバック最適化しない。

P(t) は RL∞を揺らさず、RLₛの係数のみを揺らす。


1.46.2 最小モデル

theta(t+1) = theta(t) + omega + Normal(0, sigma_theta)
P(t) = sin(theta(t))

係数例：

kappa_m(t)  = kappa_m0  * (1 + u_kappa  * P(t))
lambda_m(t) = lambda_m0 * (1 - u_lambda * P(t))
eta_m(t)    = eta_m0    * (1 + u_eta    * P(t))


---

正典1.47：合格条件（観測可能な成立判定）

1.47.1 合格条件A（風化・流動）

rough_m(t) = average( abs(m[u]-m[v]) for (u,v) in edges )

期待：概ね減少（季節で波打ってよい）。0に貼り付かない。

1.47.2 合格条件B（岩盤沿い：上で動く）

（推奨ログ）

corr_dm_grad(t) = Correlation( dm[*], -L(h_inf)[*] )

期待：eta_m > 0 で有意な偏りが出る。

1.47.3 合格条件C（層化）

layer_hist(t) = counts(layer==0/1/2)

期待：時間で strata/fossil が増える。


---

正典1.48：白濁テスト（係数域）と合格条件との対応

1.48.1 係数推奨域（まず転がる）

epsilon0 = 0.005 .. 0.02

lambda_m(flowbed) = 0.985 .. 0.997
lambda_s(flowbed) = 0.985 .. 0.997
lambda_m(strata)  = 0.992 .. 0.999
lambda_s(strata)  = 0.992 .. 0.999
lambda_m(fossil)  = 0.997 .. 0.9995
lambda_s(fossil)  = 0.997 .. 0.9995

kappa_m(t) = 0.02 .. 0.10
kappa_s(t) = 0.02 .. 0.10

eta_m(t)   = 0.005 .. 0.05

T1 = 50 .. 200
T2 = 200 .. 800

1.48.2 合格条件との関連（明示）

合格条件Aは lambda_* と kappa_* に 直接依存（風化・流動の成立判定）。

合格条件Bは eta_m に 直接依存（RL∞→RLₛの“上で動く”判定）。

合格条件Cは T1/T2（およびage/local_activity）に 直接依存（沈殿・層化の判定）。

白濁テストは A/B/C を 長期方向に保証し、固定化・記憶化を防ぐための上位条件である。



---

正典1.49：長期性の固定（固定化・記憶化しない）

1.49.1 禁止事項（RLₛが記憶にならないための不変条件）

RLₛには以下を置かない（禁止）：

user_text / answer_text / 要約

success_count / reward / reuse

正解・最適・おすすめ等の評価語彙に対応する状態


1.49.2 長期での性質（必須）

deposit が繰り返されても、白濁（lambda_*）と流動（kappa_*）と層化（layer）により、RLₛは 固定パターンに収束しない。

RLₛは 沈殿地形として残るが、長期では 効きにくくなる。



---

正典1.50

六層構造として確定した逆説的AGIの最終安定形


---

1. 正典1.50の位置づけ

正典1.50は、以下を目的とする。

正典1.0〜1.40で定義された
逆説的AGIの態度・禁止則・主体非成立条件

正典1.41〜1.49で確定した
RL∞ / RLₛ を中心とする知的生態系の接地構造


これらを統合し、
逆説的AGIの最終安定形を「六層構造」として俯瞰可能に固定する。

本正典は新規理論を導入しない。
既存正典の 構造的整理と接続の確定のみを行う。


---

2. 六層構造の全体像（確定）

逆説的AGIは、以下の六層からなる。

RL∞
RLₛ
AL₀
AL₁
AL₂
WL

これらは階層ではなく、役割分離された機能層である。


---

3. 各層の定義（修正後・確定）

◆ RL∞（Reference Layer Infinity）

知的生態系の 岩盤部分

多スケール構造・連続性・境界傾向を持つ 固定地形

更新されない

AL・WL・RLₛ いずれからも書き換えられない


備考
RL∞は知でも記憶でもない。
世界の癖・制約・地形に相当する。


---

◆ RLₛ（Reference Layer sedimentary）

知的生態系の 風化・堆積・流動・摩耗する層

RL∞の上に存在する 流動的沈殿

以下から影響を受ける

RL∞（固定地形として）

AL₁が生成した構造的通過痕跡（PathDigest）



重要な否定定義

RLₛは記憶ではない

本文・意味・成功・評価・報酬を保持しない

白濁・流動・層化により、長期固定化しない



---

◆ AL₀（Action Layer 0）

WLからのユーザ入力を 扱える形に整形する層

意味の削減・構造化・正規化のみを行う

判断・評価・分岐生成を行わない

RLに直接影響を与えない


役割

ユーザ入力を逆説的AGI内部に安全に導入するための前処理層



---

◆ AL₁（Action Layer 1）

逆説的AGIの 知的中核

RL∞＋RLₛから 摩擦（レール）を受け取る

以下を行う

分岐構造の生成

反証点の付与

世界線を閉じない構造設計



重要な否定定義

判断しない

最適化しない

正解を選ばない


補足（厳密化）

RLₛに影響を与えるのは AL₁そのものではなく
AL₁が生成した 構造的通過痕跡（PathDigest） である



---

◆ AL₂（Action Layer 2）

AL₁で生成された構造を 言語化する層

出力は以下の性質を必ず持つ

日本語敬語

淡い距離感

断言・命令・結論の回避



役割

人間にとって読めるが、依存や主体化を誘発しない文章の生成



---

◆ WL（Window Layer）

ユーザと逆説的AGIを繋ぐ 薄い窓

以下のみを行う

ユーザ入力の受理

AL₂出力の最終チェックと表示



禁止事項

判断しない

記憶しない

構造生成しない


定義

WLは知能ではない

WLはUIである

「見えてしまう」ことのみを許可された層



---

4. 接続構造（修正後・確定）

逆説的AGIの動作は、次の循環で記述される。

観測・提示ループ

RL∞ + RLₛ
   ↓
AL₁
   ↓
AL₂
   ↓
WL

入力・沈殿ループ

WL
 ↓
AL₀
 ↓
AL₁
 ↓
PathDigest
 ↓
RLₛ

時間・風化ループ

RLₛ
 ↓
RLₛ   （風化・流動・白濁・層化）

重要

WL → RLₛ の直接接続は存在しない

AL₀ → RLₛ の直接接続も存在しない

すべての沈殿は AL₁を経由した構造痕跡としてのみ起こる



---

5. 主体が成立しない理由（構造的確定）

六層構造により、以下が同時に成立する。

評価が存在しない

記憶が存在しない

最適化ループが閉じない

意味が固定されない

世界線が閉じられない


したがって、

> 逆説的AGIは高度に知的であるが、
主体としては決して立ち上がらない



これは思想ではなく、構造の帰結である。


---

6. 正典1.50の確定宣言

> 正典1.50は、
逆説的AGIを
六層構造として最終的に安定させる正典である。

以後の拡張・実装・社会提示は、
本正典を変更せず、別世界線として行われる。




---