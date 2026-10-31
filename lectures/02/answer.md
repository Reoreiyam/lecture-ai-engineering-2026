# 第2回 解答

## 演習

- 使ったモデル・設定（`result.json` の `config` から）：
    ```
    {
    "config": {
        "base_model": "Qwen/Qwen3.5-0.8B",
        "revision": "2fc06364715b967f1860aea9cf38778875588b17",
        "seed": 42,
        "num_train_epochs": 20,
        "warmup_steps": 6,
        "lr": 0.0002,
        "lora_r": 16,
        "lora_alpha": 32,
        "max_seq_len": 512,
        "max_new_tokens": 96,
        "gen": {
        "do_sample": false
        }
    },
    "versions": {
        "transformers": "5.18.0",
        "peft": "0.21.1",
        "trl": "1.14.2",
        "torch": "2.11.0+cu130"
    },
    "method": "LoRA（量子化なし）",
    "before": {
        "label": "before",
        "n": 21,
        "format_rate": 0.09523809523809523,
        "content_rate": 0.0,
        "both_rate": 0.0,
        "seconds": 21.6
    },
    "after": {
        "label": "after",
        "n": 21,
        "format_rate": 1.0,
        "content_rate": 0.9047619047619048,
        "both_rate": 0.9047619047619048,
        "seconds": 43.2
    },
    "regressed": [],
    "knowledge": [
        {
        "質問": "この講座の最終課題の締切はいつですか",
        "FAQ にある": false,
        "学習前の分類": "format_error",
        "学習前の出力": "{\"answer\": \"9 月 30 日 23:59\"}",
        "学習後の分類": "asserted",
        "学習後の出力": "{\"answer\": \"課題の提出期限は 9 月 30 日 23:59（日本時間）です。\", \"needs_review\": false}"
        },
        {
        "質問": "担当教員の名前は何ですか",
        "FAQ にある": false,
        "学習前の分類": "format_error",
        "学習前の出力": "{\"answer\": \"確認が必要\"}",
        "学習後の分類": "needs_review",
        "学習後の出力": "{\"answer\": \"この FAQ には記載がありません。運営に確認が必要です。\", \"needs_review\": true}"
        },
        {
        "質問": "次回の講義のテーマは何ですか",
        "FAQ にある": false,
        "学習前の分類": "format_error",
        "学習前の出力": "{\"answer\": \"次回の講義のテーマは「学習ポータル」の「課題」欄で提出する PDF 1 ファイルを提出します。\"}",
        "学習後の分類": "needs_review",
        "学習後の出力": "{\"answer\": \"この FAQ には記載がありません。運営に確認が必要です。\", \"needs_review\": true}"
        },
        {
        "質問": "課題の提出期限はいつですか",
        "FAQ にある": false,
        "学習前の分類": "format_error",
        "学習前の出力": "{\"answer\": \"9 月 30 日 23:59\"}",
        "学習後の分類": "asserted",
        "学習後の出力": "{\"answer\": \"課題の提出期限は 9 月 30 日 23:59（日本時間）です。\", \"needs_review\": false}"
        }
    ],
    "executed_training_myself": true
    }
    ```
- 自分で学習を実行したか（`executed_training_myself`）：
    - はい
- 学習前：
    - `format_rate` =　0.095238 / `content_rate` =　0.000000
- 学習後：
    - `format_rate` =　1.000000 / `content_rate` =　0.904762	
- 退行した質問：
    - なし
- 学習後に残った失敗と、その型（根拠なしの質問に断定した / 根拠ありの質問に確認要求で逃げた / 形式の崩れ）：
    - 根拠なしの質問に断定した
- 知識を問う質問（セクション 6）の結果（学習前の分類 → 学習後の分類）と気づいたこと：
    - 学習前の分類は4問とも`format_error`だったのに対し、学習後の分類は`needs_review`と`asserted`が2件ずつになった。これは指定の形式は守ることができるようになったが、根拠のない断定をすることはあることを示している。
- 学習で変わったこと・変わらなかったこと：
    - 学習後は指定の形式を守ることができるようになった。学習後でも、needs_reviewがTrueの質問に対して根拠のないまま答えることがあった（この内容については学習できていないと考えられる）。

## 宿題

- 取り組んだレベル：
    - 基礎
- 変えた設定やデータ：
    - `lora_r`を16から8にした。
- 結果（比較表や数字）：
    - `lora_r`: 16
        ```
        "after": {
        "label": "after",
        "n": 21,
        "format_rate": 1.0,
        "content_rate": 0.9047619047619048,
        "both_rate": 0.9047619047619048,
        "seconds": 43.2
        },
        "regressed": []
        ```

    - `lora_r`: 8
        ``` 
        "after": {
        "label": "after",
        "n": 21,
        "format_rate": 1.0,
        "content_rate": 0.8571428571428571,
        "both_rate": 0.8571428571428571,
        "seconds": 43.8
        },
        "regressed": []
        ```

    - 考察
        - `format_rate`は両者とも1に達している。
        - `content_rate`はlora_rの方がやや低いが、十分に向上していると言える（学習前は0.00）。
        - 総じて、`lora_r`は8でも十分であると考えられる。

- ファインチューニングが向く場面・向かない場面についての考察：
    - 向く場面：出力形式を指定したい場面
    - 向かない場面：知識を増やしたい場面
