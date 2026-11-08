# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い： エージェントは、第1回（素のLLM）と異なり、用語集から回答を生成している。
- `--trace` で見えたツール呼び出し（ツール名と引数）：make_quiz({'lecture': 2})(詳細は【Trace付きで実行】を参照)
- 1つの質問での LLM 呼び出し回数（`requests`）：[usage] RunUsage(..., requests=2, tool_calls=1)(詳細は【Trace付きで実行】を参照)

【Trace付きで実行】
$ uv run python -m app.agent --trace "第2 回の内容から確認問題を1問出して"
[model] gemini-3.5-flash-lite
第2回からの確認問題です。ぜひ考えてみてください！

**【確認問題】**
教師ありファインチューニング（SFT）の意味は？

（答えのヒント：入力と出力をセットにして追加学習させる方法です。）

---
関連する回：第2回

--- trace ---
[tool call]   make_quiz({'lecture': 2})
[tool return] make_quiz -> {"question": "教師ありファインチューニング（SFT）の意味は？", "answer": "入力と望ましい出力のペア（例：質問と模範回答）を使ってモデルを追加学習させる方法。指示に従う振る舞いを身につけさせる代表的な手法。"}…
[usage] RunUsage(cost=Decimal('0.0004661'), details={'text_prompt_tokens': 912}, input_text_tokens=912, input_tokens=912, output_tokens=77, requests=2, tool_calls=1)

- 追加したツールと、docstring に書いた説明：以下を追加した。

def make_quiz(lecture: int) -> str:
    """指定した回の用語から1つを選び、確認問題と答えを返す。

    「第2回の確認問題を出して」のように、復習用の問題を求められたときに使う。
    """
    terms = [entry for entry in load_glossary() if entry["lecture"] == lecture]
    if not terms:
        return f"第{lecture}回の用語は用語集にありません。"

    term = random.choice(terms)
    return json.dumps(
        {
            "question": f"{term['term']}の意味は？",
            "answer": term["definition"],
        },
        ensure_ascii=False,
    )


## 宿題

- 取り組んだレベル：基礎
- やったこと：演習で見た app/agent.pyのINSTRUCTIONSを変更し、出力差分を確認
- 結果：
回答に対し、答えと補足を追加する条件（★追加：第2回目実行で追記）をINSTRUCTIONSに追記した結果、
(第1回目にて実行）では答えを導くヒントのみ出力されていたが、答えと補足が出力されるようになった。

以下、実行条件

■実行文：uv run python -m app.agent --trace "第2 回の内容から確認問題を1問出して"

【初回実行】
■INSTRUCTIONS
INSTRUCTIONS = """\
あなたはAIエンジニアリング講座（AIE2026）の学習支援エージェントです。
- 用語や講座に固有のことを答える前に、必ず search_glossary で用語集を確認する。
- 回の内容を聞かれたら get_lecture_summary を使う。
- 確認問題や復習問題を求められたら make_quiz を使う。
- 用語集に見つからないことは、推測で断定せず「用語集にはない」と伝える。
- 回答の最後に、関係する回（第N回）を示す。
- 宿題の答えそのものは書かず、考え方のヒントで導く。
"""

■回答
[model] gemini-3.5-flash-lite
第2回からの確認問題です。ぜひ考えてみてください！

**【確認問題】**
教師ありファインチューニング（SFT）の意味は？

（答えのヒント：入力と出力をセットにして追加学習させる方法です。）

---
関連する回：第2回

--- trace ---
[tool call]   make_quiz({'lecture': 2})
[tool return] make_quiz -> {"question": "教師ありファインチューニング（SFT）の意味は？", "answer": "入力と望ましい出力のペア（例：質問と模範回答）を使ってモデルを追加学習させる方法。指示に従う振る舞いを身につけさせる代表的な手法。"}…
[usage] RunUsage(cost=Decimal('0.0004661'), details={'text_prompt_tokens': 912}, input_text_tokens=912, input_tokens=912, output_tokens=77, requests=2, tool_calls=1)



【2回目実行】
■Instruction
INSTRUCTIONS = """\
あなたはAIエンジニアリング講座（AIE2026）の学習支援エージェントです。
- 用語や講座に固有のことを答える前に、必ず search_glossary で用語集を確認する。
- 回の内容を聞かれたら get_lecture_summary を使う。
- 確認問題や復習問題を求められたら make_quiz を使う。
- 用語集に見つからないことは、推測で断定せず「用語集にはない」と伝える。
- 回答の最後に、関係する回（第N回）を示す。
# - 宿題の答えそのものは書かず、考え方のヒントで導く。
- 答えを記載する。
- 答えに対し補足を追記する。
"""

■回答
[model] gemini-3.5-flash-lite
第2回の内容から確認問題を1問出題します！

**【確認問題】**
ランク（LoRAの $r$）の意味は？

---

**【答え】**
LoRAで使う低ランク行列の大きさを決めるハイパーパラメータ。大きいほど表現力が上がるが、学習するパラメータとメモリも増える。

**【補足】**
LoRA（Low-Rank Adaptation）では、大規模言語モデルの全パラメータを更新するのではなく、低ランクの行列（低ランク分解）を用いた差分パラメータのみを効率的に学習します。このとき行列のランク $r$ を小さく設定することで、VRAM（メモリ）の使用量を大幅に抑えつつ、効率的なファインチューニングが可能になります。

関連回：第2回

--- trace ---
[tool call]   make_quiz({'lecture': 2})
[tool return] make_quiz -> {"question": "ランク（LoRAのr）の意味は？", "answer": "LoRAで使う低ランク行列の大きさを決めるハイパーパラメータ。大きいほど表現力が上がるが、学習するパラメータとメモリも増える。"}…
[usage] RunUsage(cost=Decimal('0.0007329'), details={'text_prompt_tokens': 943}, input_text_tokens=943, input_tokens=943, output_tokens=180, requests=2, tool_calls=1)

- 気づき・疑問：
たしかにINSTRUCTIONSを見たうえで、LLMは挙動、出力している。