# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：素のLLMはAIE2026の講座の宿題にある「3レベル」という情報を参照できないため、一般論を答えた。エージェントは、AIE2026の講座の宿題にある「3レベル」という情報を参照して的確に答えた。

- `--trace` で見えたツール呼び出し（ツール名と引数）：search_glossary({'query': 'LoRA'})
search_glossary({'query': 'QLoRA'})

- 1つの質問での LLM 呼び出し回数（`requests`）：requests=2

- 追加したツールと、docstring に書いた説明：
def make_quiz(lecture: int) -> str:
 """指定した回の用語から 1つを選び、
 確認問題と答えを返す。
 「第2回の確認問題を出して」のように、
 復習用の問題を求められたときに使う。
 """
 # 1. load_glossary() から lecture が一致する用語を集める
 # 2. 1 つ選ぶ（random.choice ）
 # 3. 問題と答えを文字列で返す（ json.dumps など）

## 宿題

- 取り組んだレベル：基礎
- やったこと：INSTRUCTIONSを空にして、以下のコマンドを実行した。
uv run python -m app.agent --trace " 第2回の内容から確認問題を 1問出して"
- 結果：INSTRUCTIONSを空にしても同様にmake_quizが呼び出された。
INSTRUCTIONSを空にした方が使用したトークンが減った。

--- 講義通りのINSTRUCTIONSでコマンドを実行したときのtrace ---
[tool call]   make_quiz({'lecture': 2})
[tool return] make_quiz -> {"question": "「過学習」の意味は？", "answer": "学習データにだけ過度に適合し、未知の入力に対する性能が落ちる状態。学習に使わない検証データで性能を測って気づく。"}…
[usage] RunUsage(cost=Decimal('0.0004256'), details={'text_prompt_tokens': 902}, input_text_tokens=902, input_tokens=902, output_tokens=62, requests=2, tool_calls=1)

--- INSTRUCTIONSを空にしてコマンド実行したときのtrace ---
[tool call]   make_quiz({'lecture': 2})
[tool return] make_quiz -> {"question": "「Hugging Face Hub」の意味は？", "answer": "公開されたモデルやデータセットを共有・ダウンロードできるプラットフォーム。オープンウェイトのモデルを入手したり、LoRAアダプタを公開した…
[usage] RunUsage(cost=Decimal('0.0003440'), details={'text_prompt_tokens': 630}, input_text_tokens=630, input_tokens=630, output_tokens=62, requests=2, tool_calls=1)

- 気づき・疑問：今回はツールの数もそれほど多くなく、それほど複雑でもなかった。これがツールが増えてきて複雑になったときに、INSTRUCTIONSを空にしても同様の結果になるのか気になった。トークンが節約できるのならば、INSTRUCTIONSはただ細かく書くのではなく、ツールの複雑さに応じで細かさを決めるのがいいのではないかと思った。