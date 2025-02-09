This repository contains training, generation and utility scripts for Stable Diffusion.

[__Change History__](#change-history) is moved to the bottom of the page.
更新履歴は[ページ末尾](#change-history)に移しました。

[日本語版README](./README-ja.md)

For easier use (GUI and PowerShell scripts etc...), please visit [the repository maintained by bmaltais](https://github.com/bmaltais/kohya_ss). Thanks to @bmaltais!

This repository contains the scripts for:

* DreamBooth training, including U-Net and Text Encoder
* Fine-tuning (native training), including U-Net and Text Encoder
* LoRA training
* Texutl Inversion training
* Image generation
* Model conversion (supports 1.x and 2.x, Stable Diffision ckpt/safetensors and Diffusers)

__Stable Diffusion web UI now seems to support LoRA trained by ``sd-scripts``.__ (SD 1.x based only) Thank you for great work!!! 

## About requirements.txt

These files do not contain requirements for PyTorch. Because the versions of them depend on your environment. Please install PyTorch at first (see installation guide below.) 

The scripts are tested with PyTorch 1.12.1 and 1.13.0, Diffusers 0.10.2.

## Links to how-to-use documents

All documents are in Japanese currently.

* [Training guide - common](./train_README-ja.md) : data preparation, options etc...
    * [Dataset config](./config_README-ja.md)
* [DreamBooth training guide](./train_db_README-ja.md)
* [Step by Step fine-tuning guide](./fine_tune_README_ja.md):
* [training LoRA](./train_network_README-ja.md)
* [training Textual Inversion](./train_ti_README-ja.md)
* note.com [Image generation](https://note.com/kohya_ss/n/n2693183a798e)
* note.com [Model conversion](https://note.com/kohya_ss/n/n374f316fe4ad)

## Windows Required Dependencies

Python 3.10.6 and Git:

- Python 3.10.6: https://www.python.org/ftp/python/3.10.6/python-3.10.6-amd64.exe
- git: https://git-scm.com/download/win

Give unrestricted script access to powershell so venv can work:

- Open an administrator powershell window
- Type `Set-ExecutionPolicy Unrestricted` and answer A
- Close admin powershell window

## Windows Installation

Open a regular Powershell terminal and type the following inside:

```powershell
git clone https://github.com/kohya-ss/sd-scripts.git
cd sd-scripts

python -m venv venv
.\venv\Scripts\activate

pip install torch==1.12.1+cu116 torchvision==0.13.1+cu116 --extra-index-url https://download.pytorch.org/whl/cu116
pip install --upgrade -r requirements.txt
pip install -U -I --no-deps https://github.com/C43H66N12O12S2/stable-diffusion-webui/releases/download/f/xformers-0.0.14.dev0-cp310-cp310-win_amd64.whl

cp .\bitsandbytes_windows\*.dll .\venv\Lib\site-packages\bitsandbytes\
cp .\bitsandbytes_windows\cextension.py .\venv\Lib\site-packages\bitsandbytes\cextension.py
cp .\bitsandbytes_windows\main.py .\venv\Lib\site-packages\bitsandbytes\cuda_setup\main.py

accelerate config
```

update: ``python -m venv venv`` is seemed to be safer than ``python -m venv --system-site-packages venv`` (some user have packages in global python).

Answers to accelerate config:

```txt
- This machine
- No distributed training
- NO
- NO
- NO
- all
- fp16
```

note: Some user reports ``ValueError: fp16 mixed precision requires a GPU`` is occurred in training. In this case, answer `0` for the 6th question: 
``What GPU(s) (by id) should be used for training on this machine as a comma-separated list? [all]:`` 

(Single GPU with id `0` will be used.)

### about PyTorch and xformers

Other versions of PyTorch and xformers seem to have problems with training.
If there is no other reason, please install the specified version.

## Upgrade

When a new release comes out you can upgrade your repo with the following command:

```powershell
cd sd-scripts
git pull
.\venv\Scripts\activate
pip install --use-pep517 --upgrade -r requirements.txt
```

Once the commands have completed successfully you should be ready to use the new version.

## Credits

The implementation for LoRA is based on [cloneofsimo's repo](https://github.com/cloneofsimo/lora). Thank you for great work!

The LoRA expansion to Conv2d 3x3 was initially released by cloneofsimo and its effectiveness was demonstrated at [LoCon](https://github.com/KohakuBlueleaf/LoCon) by KohakuBlueleaf. Thank you so much KohakuBlueleaf!

## License

The majority of scripts is licensed under ASL 2.0 (including codes from Diffusers, cloneofsimo's and LoCon), however portions of the project are available under separate license terms:

[Memory Efficient Attention Pytorch](https://github.com/lucidrains/memory-efficient-attention-pytorch): MIT

[bitsandbytes](https://github.com/TimDettmers/bitsandbytes): MIT

[BLIP](https://github.com/salesforce/BLIP): BSD-3-Clause

## Change History

### 22 Apr. 2023, 2023/4/22:

- Added support for logging to wandb. Please refer to [PR #428](https://github.com/kohya-ss/sd-scripts/pull/428). Thank you p1atdev!
  - `wandb` installation is required. Please install it with `pip install wandb`. Login to wandb with `wandb login` command, or set `--wandb_api_key` option for automatic login.
  - Please let me know if you find any bugs as the test is not complete. 
- You can automatically login to wandb by setting the `--wandb_api_key` option. Please be careful with the handling of API Key. [PR #435](https://github.com/kohya-ss/sd-scripts/pull/435) Thank you Linaqruf!

- Improved the behavior of `--debug_dataset` on non-Windows environments. [PR #429](https://github.com/kohya-ss/sd-scripts/pull/429) Thank you tsukimiya!
- Fixed `--face_crop_aug` option not working in Fine tuning method.
- Prepared code to use any upscaler in `gen_img_diffusers.py`.

- wandbへのロギングをサポートしました。詳細は [PR #428](https://github.com/kohya-ss/sd-scripts/pull/428)をご覧ください。p1atdev氏に感謝します。
  - `wandb` のインストールが別途必要です。`pip install wandb` でインストールしてください。また `wandb login` でログインしてください（学習スクリプト内でログインする場合は `--wandb_api_key` オプションを設定してください）。
  - テスト未了のため不具合等ありましたらご連絡ください。
- wandbへのロギング時に `--wandb_api_key` オプションを設定することで自動ログインできます。API Keyの扱いにご注意ください。 [PR #435](https://github.com/kohya-ss/sd-scripts/pull/435) Linaqruf氏に感謝します。

- Windows以外の環境での`--debug_dataset` の動作を改善しました。[PR #429](https://github.com/kohya-ss/sd-scripts/pull/429) tsukimiya氏に感謝します。
- `--face_crop_aug`オプションがFine tuning方式で動作しなかったのを修正しました。
- `gen_img_diffusers.py`に任意のupscalerを利用するためのコード準備を行いました。
  
### 19 Apr. 2023, 2023/4/19:
- Fixed `lora_interrogator.py` not working. Please refer to [PR #392](https://github.com/kohya-ss/sd-scripts/pull/392) for details. Thank you A2va and heyalexchoi!
- Fixed the handling of tags containing `_` in `tag_images_by_wd14_tagger.py`.
- `lora_interrogator.py`が動作しなくなっていたのを修正しました。詳細は [PR #392](https://github.com/kohya-ss/sd-scripts/pull/392) をご参照ください。A2va氏およびheyalexchoi氏に感謝します。
- `tag_images_by_wd14_tagger.py`で`_`を含むタグの取り扱いを修正しました。

### Naming of LoRA

The LoRA supported by `train_network.py` has been named to avoid confusion. The documentation has been updated. The following are the names of LoRA types in this repository.

1. __LoRA-LierLa__ : (LoRA for __Li__ n __e__ a __r__  __La__ yers)

    LoRA for Linear layers and Conv2d layers with 1x1 kernel

2. __LoRA-C3Lier__ : (LoRA for __C__ olutional layers with __3__ x3 Kernel and  __Li__ n __e__ a __r__ layers)

    In addition to 1., LoRA for Conv2d layers with 3x3 kernel 
    
LoRA-LierLa is the default LoRA type for `train_network.py` (without `conv_dim` network arg). LoRA-LierLa can be used with [our extension](https://github.com/kohya-ss/sd-webui-additional-networks) for AUTOMATIC1111's Web UI, or with the built-in LoRA feature of the Web UI.

To use LoRA-C3Liar with Web UI, please use our extension.

### LoRAの名称について

`train_network.py` がサポートするLoRAについて、混乱を避けるため名前を付けました。ドキュメントは更新済みです。以下は当リポジトリ内の独自の名称です。

1. __LoRA-LierLa__ : (LoRA for __Li__ n __e__ a __r__  __La__ yers、リエラと読みます)

    Linear 層およびカーネルサイズ 1x1 の Conv2d 層に適用されるLoRA

2. __LoRA-C3Lier__ : (LoRA for __C__ olutional layers with __3__ x3 Kernel and  __Li__ n __e__ a __r__ layers、セリアと読みます)

    1.に加え、カーネルサイズ 3x3 の Conv2d 層に適用されるLoRA

LoRA-LierLa は[Web UI向け拡張](https://github.com/kohya-ss/sd-webui-additional-networks)、またはAUTOMATIC1111氏のWeb UIのLoRA機能で使用することができます。

LoRA-C3Liarを使いWeb UIで生成するには拡張を使用してください。

### 17 Apr. 2023, 2023/4/17:

- Added the `--recursive` option to each script in the `finetune` folder to process folders recursively. Please refer to [PR #400](https://github.com/kohya-ss/sd-scripts/pull/400/) for details. Thanks to Linaqruf!
- `finetune`フォルダ内の各スクリプトに再起的にフォルダを処理するオプション`--recursive`を追加しました。詳細は [PR #400](https://github.com/kohya-ss/sd-scripts/pull/400/) を参照してください。Linaqruf 氏に感謝します。

### 14 Apr. 2023, 2023/4/14:
- Fixed a bug that caused an error when loading DyLoRA with the `--network_weight` option in `train_network.py`.
- `train_network.py`で、DyLoRAを`--network_weight`オプションで読み込むとエラーになる不具合を修正しました。

### 13 Apr. 2023, 2023/4/13:

- Added support for DyLoRA in `train_network.py`. Please refer to [here](./train_network_README-ja.md#dylora) for details (currently only in Japanese).
- Added support for caching latents to disk in each training script. Please specify __both__ `--cache_latents` and `--cache_latents_to_disk` options.
  - The files are saved in the same folder as the images with the extension `.npz`. If you specify the `--flip_aug` option, the files with `_flip.npz` will also be saved.
  - Multi-GPU training has not been tested.
  - This feature is not tested with all combinations of datasets and training scripts, so there may be bugs.
- Added workaround for an error that occurs when training with `fp16` or `bf16` in `fine_tune.py`.

- `train_network.py`でDyLoRAをサポートしました。詳細は[こちら](./train_network_README-ja.md#dylora)をご覧ください。
- 各学習スクリプトでlatentのディスクへのキャッシュをサポートしました。`--cache_latents`オプションに __加えて__、`--cache_latents_to_disk`オプションを指定してください。
  - 画像と同じフォルダに、拡張子 `.npz` で保存されます。`--flip_aug`オプションを指定した場合、`_flip.npz`が付いたファイルにも保存されます。
  - マルチGPUでの学習は未テストです。
  - すべてのDataset、学習スクリプトの組み合わせでテストしたわけではないため、不具合があるかもしれません。
- `fine_tune.py`で、`fp16`および`bf16`の学習時にエラーが出る不具合に対して対策を行いました。

## Sample image generation during training
  A prompt file might look like this, for example

```
# prompt 1
masterpiece, best quality, (1girl), in white shirts, upper body, looking at viewer, simple background --n low quality, worst quality, bad anatomy,bad composition, poor, low effort --w 768 --h 768 --d 1 --l 7.5 --s 28

# prompt 2
masterpiece, best quality, 1boy, in business suit, standing at street, looking back --n (low quality, worst quality), bad anatomy,bad composition, poor, low effort --w 576 --h 832 --d 2 --l 5.5 --s 40
```

  Lines beginning with `#` are comments. You can specify options for the generated image with options like `--n` after the prompt. The following can be used.

  * `--n` Negative prompt up to the next option.
  * `--w` Specifies the width of the generated image.
  * `--h` Specifies the height of the generated image.
  * `--d` Specifies the seed of the generated image.
  * `--l` Specifies the CFG scale of the generated image.
  * `--s` Specifies the number of steps in the generation.

  The prompt weighting such as `( )` and `[ ]` are working.

## サンプル画像生成
プロンプトファイルは例えば以下のようになります。

```
# prompt 1
masterpiece, best quality, (1girl), in white shirts, upper body, looking at viewer, simple background --n low quality, worst quality, bad anatomy,bad composition, poor, low effort --w 768 --h 768 --d 1 --l 7.5 --s 28

# prompt 2
masterpiece, best quality, 1boy, in business suit, standing at street, looking back --n (low quality, worst quality), bad anatomy,bad composition, poor, low effort --w 576 --h 832 --d 2 --l 5.5 --s 40
```

  `#` で始まる行はコメントになります。`--n` のように「ハイフン二個＋英小文字」の形でオプションを指定できます。以下が使用可能できます。

  * `--n` Negative prompt up to the next option.
  * `--w` Specifies the width of the generated image.
  * `--h` Specifies the height of the generated image.
  * `--d` Specifies the seed of the generated image.
  * `--l` Specifies the CFG scale of the generated image.
  * `--s` Specifies the number of steps in the generation.

  `( )` や `[ ]` などの重みづけも動作します。

Please read [Releases](https://github.com/kohya-ss/sd-scripts/releases) for recent updates.
最近の更新情報は [Release](https://github.com/kohya-ss/sd-scripts/releases) をご覧ください。
