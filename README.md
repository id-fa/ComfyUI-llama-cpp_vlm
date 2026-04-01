# ComfyUI-llama-cpp  
llama.cppベースでComfyUI上でLLM/VLMモデルをネイティブ実行  
**[[📃中文版](./README_zh.md)] [[🇬🇧 English](#english)]**

## プレビュー  
![](./img/preview.jpg)

## インストール  

#### ノードのインストール:  
```bash
cd ComfyUI/custom_nodes
git clone https://github.com/lihaoyun6/ComfyUI-llama-cpp.git
python -m pip install -r ComfyUI-llama-cpp/requirements.txt
```

ビルド済み `llama-cpp-python` のインストールに失敗した場合は、下記のコマンドでビルドを試してください:  
```bash
pip uninstall llama-cpp-python -y
set FORCE_CMAKE=1
set CMAKE_ARGS=-DGGML_CUDA=on
pip install llama-cpp-python --no-cache-dir
```

#### モデルのダウンロード:  
- モデルファイルを `ComfyUI/models/text_encoders` フォルダに配置してください。  

	> VLMモデルで画像入力を処理する場合は、`mmproj` ウェイトのダウンロードも忘れずに。

## クレジット  
- [llama-cpp-python](https://github.com/JamePeng/llama-cpp-python) @JamePeng  
- [ComfyUI-llama-cpp](https://github.com/kijai/ComfyUI-llama-cpp) @kijai  
- [ComfyUI](https://github.com/comfyanonymous/ComfyUI) @comfyanonymous

---

<a id="english"></a>

# ComfyUI-llama-cpp  
Run LLM/VLM models natively in ComfyUI based on llama.cpp  
**[[📃中文版](./README_zh.md)] [[🇯🇵 日本語](#)]**

## Preview  
![](./img/preview.jpg)

## Installation  

#### Install the node:  
```bash
cd ComfyUI/custom_nodes
git clone https://github.com/lihaoyun6/ComfyUI-llama-cpp.git
python -m pip install -r ComfyUI-llama-cpp/requirements.txt
```

If the pre-built `llama-cpp-python` installation fails, try building from source:  
```bash
pip uninstall llama-cpp-python -y
set FORCE_CMAKE=1
set CMAKE_ARGS=-DGGML_CUDA=on
pip install llama-cpp-python --no-cache-dir
```

#### Download models:  
- Place your model files in the `ComfyUI/models/text_encoders` folder.  

	> If you need a VLM model to process image input, don't forget to download the `mmproj` weights.

## Credits  
- [llama-cpp-python](https://github.com/JamePeng/llama-cpp-python) @JamePeng  
- [ComfyUI-llama-cpp](https://github.com/kijai/ComfyUI-llama-cpp) @kijai  
- [ComfyUI](https://github.com/comfyanonymous/ComfyUI) @comfyanonymous
