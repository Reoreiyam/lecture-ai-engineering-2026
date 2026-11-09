# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：
    - 第1回の素のLLMは正しくないが、「らしい答え」を出力したのに対し、今回のエージェントは背景を踏まえた正しい答えを出力した点。
- `--trace` で見えたツール呼び出し（ツール名と引数）：
    - ツール名
        - search_glossary
    - 引数
        - LoRA
        - QLoRA
- 1つの質問での LLM 呼び出し回数（`requests`）：
    - 2回
- 追加したツールと、docstring に書いた説明：
    - 追加したツール：
        - make_quiz
    - docstringの説明：
        - ```"""指定した回を復習したいときに、用語集から確認問題を1問作って問題と答えを返す。"""```

## 宿題

- 取り組んだレベル
    - 基礎
- やったこと：
    1. ```app/agents.py```のINSTRUCTIONSから「必ず search_glossary で用語集を確認する」の
行を消す
    2. ```uv run python -m app.agent --trace "LoRAとQLoRAの違いは？"```を実行
    3. ```app/tools.py```の```search_glossary```のdocstringにおいて、「用語を1つだけ入れる」を「まとめて入れる」に変更
    4. 2と同じ
- 結果：
    - 元々の出力
        ```
        --- trace ---
        [tool call]   search_glossary({'query': 'LoRA'})
        [tool call]   search_glossary({'query': 'QLoRA'})
        [tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
        [tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
        [usage] RunUsage(cost=Decimal('0.0008560'), details={'text_prompt_tokens': 1270}, input_text_tokens=1270, input_tokens=1270, output_tokens=190, requests=2, tool_calls=2)
        ```

    - 1を施した2の段階の出力
        ```
        --- trace ---
        [tool call]   search_glossary({'query': 'LoRA'})
        [tool call]   search_glossary({'query': 'QLoRA'})
        [tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
        [tool return] search_glossary -> [{"term": "QLoRA", "lecture": 2, "definition": "ベースモデルの重みを4ビットなどに量子化してメモリを節約したうえで、LoRAで学習する手法。より大きなモデルを小さなGPUで扱える。本講座の第2…
        [usage] RunUsage(cost=Decimal('0.0008410'), details={'text_prompt_tokens': 1270}, input_text_tokens=1270, input_tokens=1270, output_tokens=184, requests=2, tool_calls=2)
        ```

    - 3を施した4の段階の出力
        ```
        --- trace ---
        [tool call]   search_glossary({'query': 'LoRA QLoRA'})
        [tool return] search_glossary -> [{"term": "LoRA", "lecture": 2, "definition": "事前学習済みの重みは固定したまま、重みの更新分を2つの小さな行列の積（低ランク行列）で表し、その小さな行列だけを学習するPEFT手法。学習するパラ…
        [usage] RunUsage(cost=Decimal('0.0006760'), details={'text_prompt_tokens': 945}, input_text_tokens=945, input_tokens=945, output_tokens=157, requests=2, tool_calls=1)
        ```

    - INSTRUCTIONSを変更しただけでは出力に変更はなく、指示がなくてもsearch_glosseryで用語を確認した。
    - docstringを変更すると出力は変わった。新たに書き直した通り、search_glosseryの結果がまとめて記され、```tool_calls```も1となった。
- 気づき・疑問：
    - docstringは「コメント程度」のもので、エージェントの挙動に影響を与えるとは思っていなかったが、docstringを変更することで挙動を制御できることを初めて知りました。
