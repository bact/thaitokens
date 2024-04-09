# thaitokens

ทดสอบใช้ TokenMonster สร้างรายการหน่วยคำย่อยสำหรับภาษาไทย

Experimenting extracting Thai subword tokens for language model creation, using [TokenMonster](https://github.com/alasdairforsythe/tokenmonster/).

## Steps

Follow [4 training steps](https://github.com/alasdairforsythe/tokenmonster/tree/main/training) as detailed by the TokenMonster project. You need the Go compiler to build the training toolchain.

### 1. Prepare the dataset

Build a mini dataset from [Wisesight Sentiment Corpus](https://github.com/PyThaiNLP/wisesight-sentiment) (6 MiB):

```sh
cat neg.txt neu.txt pos.txt q.txt > wss.txt
```

### 2. Generate tokens

```sh
./getalltokens -dataset wss.txt -output wss.alltokens -mode balanced -capcode 0 -charset utf-8 -norm "collapse quotemarks nfd trim unixlines" -only-valid -min-occur 2 -workers 2
```

- `-capcode 0` is recommended by TokenMonster for languages that don't use spaces as word separators.
- `-workers N` is a number of worker threads to run, excluding main thread. Best to set it to 1 less than the number of CPU threads.

It will start generating tokens:

```text
Charset: UTF-8
Normalization: NFD Quotemarks Collapse Trim UnixLines
Capcode: 0 (disabled)
Optimization mode: 2 (balanced)
Only valid UTF-8 allowed
2024/04/08 21:31:14 Loading wss.txt
2024/04/08 21:31:14 Finding tokens in chunk 1 of 1
2024/04/08 21:45:06 Tokens before final trim: 25,759,395
2024/04/08 21:45:06 Trimming final tokens for min 2
2024/04/08 21:45:11 Tokens after trimming: 7,317,906
2024/04/08 21:45:11 Filtered 251,869,920 tokens in 13m56.882s
2024/04/08 21:45:11 Saving tokens...
2024/04/08 21:45:16 Saved: wss.alltokens
```

### 3. Train vocabulary

Use the dataset from Step (1) and tokens from Step (2) to get a vocabulary:

```sh
./trainvocab -dataset wss.txt -dictionary wss.alltokens -dir wss-results -include-utf8-bytes -vocab-size 65536 -workers 2
```

Different results will be saved to the `wss-results` directory:

```text
Loading wss.alltokens
Charset: UTF-8
Normalization: NFD Quotemarks Collapse Trim UnixLines
Capcode: 0 (disabled)
Optimization mode: 2 (balanced)
Vocabulary size: 65536
Single byte tokens: 213
Loading wss.txt
2024/04/08 23:14:07 Worker 1 starting run 1
2024/04/08 23:14:07 Worker 0 starting run 1
2024/04/08 23:14:09 Worker 1 completed run 1  Score: 635,748
2024/04/08 23:14:09 Worker 0 completed run 1  Score: 629,851

[...]

2024/04/09 00:46:01 Worker 1 completed run 1028  Score: 651,943
2024/04/09 00:46:01 Deleted 3 of 3 tokens; Remaining 65,560 tokens;  reached_vocab Best: 651,555; Tries:998
2024/04/09 00:46:03 Worker 0 completed run 1029  Score: 651,902
2024/04/09 00:46:03 Deleted 1 of 2 tokens; Remaining 65,559 tokens;  reached_vocab Best: 651,555; Tries:999
2024/04/09 00:46:04 Worker 1 completed run 1029  Score: 652,022
2024/04/09 00:46:04 -- FINISHED --
No new best score in 1000 runs
Best result tokenized 6,296,789 bytes with 651,555 tokens
Average 9.664 characters/token
Best result:
  wss-results/651555_568.tok
```

### 4. Export vocabulary

Extract tokens from the best vocabulary:

```sh
./exportvocab -input wss-results -output wss.vocab
```

```text
Loading wss-results/651555_568.tok
Capcode:               0 (disabled)
Charset:               UTF-8
Normalization:         NFD Quotemarks Collapse Trim UnixLines
Optimization mode:     2 (balanced)
Maximum token length:  40
Regular tokens:        65322
Single byte tokens:    214
Special tokens:        0
UNK token:             No (can be added)
Deleted tokens:        0
Total tokens:          65536

Exported: wss.vocab
```

Convert it to YAML format:

```sh
./exportvocab -input-vocab wss.vocab -output-yaml wss.vocab.yaml -order-by-score
```

See [wss.vocab.yaml](wss.vocab.yaml) to see how the resulting vocabulary can look like.
