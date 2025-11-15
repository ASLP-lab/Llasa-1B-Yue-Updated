# Llasa-1B-Yue-Updated

Llasa-1B-Yue-Updated is a Cantonese end-to-end speech generation system built upon Llasa-1B (Speech LLM) and BiCodec (SparkTTS Codec).
This repository provides:
- Llasa-1B inference script (Text → Speech Tokens)
- SparkTTS BiCodec encoder & decoder (Token ↔ Waveform)
- Dependency installation guide & environment setup
- Example input and output formats

A corresponding model checkpoint is also released on HuggingFace:

🔗 https://huggingface.co/ASLP-lab/LLasa-1B-Yue-Updated/tree/main

# Environment Installation

It is recommended to use conda:

```
conda create -n llasa-env python=3.10 -y
conda activate llasa-env
pip install -r requirements.txt
```
# Preparing Models and Data

## Llasa-1B Model (HuggingFace Format)

During inference, you need to specify the model path:

```
--model_path /path/to/Llasa_1B_model_dir
```

## BiCodec Checkpoint

The repository already includes:
```
bicodec_ckpt/ckpt/model.safetensors
bicodec_ckpt/ckpt/config.yaml
```
No additional downloads are required.

# Input List (meta list)

The inference script expects an input file in the following format:

```
utt_id | tag | prompt_wav | text
```
Example:

```
1001 | yue | path/to/wav1.wav | 你好，我是一个大模型。
1002 | norm | path/to/wav2.wav | 欢迎来到粤语语音合成系统。
```
Where:
- utt_id: filename of the output WAV
- tag: language tag (yue for Cantonese, norm for Mandarin)
- prompt_wav: reference audio for extracting speaker timbre
- text: text to be synthesized


# Inference Usage

Run the inference script:
```
python infer.py \
    --model_path /path/to/llasa_1b_checkpoint \
    --meta_path /path/to/data.lst \
    --output_dir ./out_wavs \
    --device cuda:0
```
After execution, the script will automatically:
1.	Load the Llasa-1B model
2.	Load the BiCodec tokenizer & decoder
3.	For each entry in the meta list:
	- 	Load prompt_wav → extract speaker embedding (global token)
	- 	Encode text inputs
	- 	Generate speech token sequence via Llasa-1B
	- 	Decode tokens into a 16kHz waveform using BiCodec
4.	Save WAV files into output_dir
