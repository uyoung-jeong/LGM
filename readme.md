
## Large Multi-View Gaussian Model

This is the unofficial pip-installable version of *LGM: Large Multi-View Gaussian Model for High-Resolution 3D Content Creation*.

### PIP Install
```bash
pip install git+https://github.com/uyoung-jeong/LGM.git
```

### Usage Example
```bash
import tyro
from lgm.models import LGM
from lgm.options import AllConfigs
from mvdream.pipeline_mvdream import MVDreamPipeline

lgm_opt = tyro.cli(AllConfigs, args=['big'])
model = LGM(lgm_opt)

pipe_text = MVDreamPipeline.from_pretrained(
        'ashawkey/mvdream-sd2.1-diffusers',
        torch_dtype=torch.float16,
        trust_remote_code=True
    )
```

Below content is the same as the original repository.

### [Project Page](https://me.kiui.moe/lgm/) | [Arxiv](https://arxiv.org/abs/2402.05054) | [Weights](https://huggingface.co/ashawkey/LGM) | <a href="https://huggingface.co/spaces/ashawkey/LGM"><img src="https://img.shields.io/badge/%F0%9F%A4%97%20Gradio%20Demo-Huggingface-orange"></a>

https://github.com/3DTopia/LGM/assets/25863658/cf64e489-29f3-4935-adba-e393a24c26e8

### News
[2024.4.3] Thanks to [@yxymessi](https://github.com/yxymessi) and [@florinshen](https://github.com/florinshen), we have fixed a **severe bug in rotation normalization** [here](https://github.com/3DTopia/LGM/commit/9a0797cdbacf8e6216d0108cb00cbe43b9cb3d81). We have finetuned the model with correct normalization for 30 more epochs and uploaded new checkpoints.

### Replicate Demo:
* gaussians: [demo](https://replicate.com/camenduru/lgm) | [code](https://github.com/camenduru/LGM-replicate)
* mesh: [demo](https://replicate.com/camenduru/lgm-ply-to-glb) | [code](https://github.com/camenduru/LGM-ply-to-glb-replicate)

Thanks to [@camenduru](https://github.com/camenduru)!

### Install

```bash
# xformers is required! please refer to https://github.com/facebookresearch/xformers for details.
# for example, we use torch 2.1.0 + cuda 11.8
pip install torch==2.1.0 torchvision==0.16.0 torchaudio==2.1.0 --index-url https://download.pytorch.org/whl/cu118
pip install -U xformers --index-url https://download.pytorch.org/whl/cu118

# a modified gaussian splatting (+ depth, alpha rendering)
git clone --recursive https://github.com/ashawkey/diff-gaussian-rasterization
pip install ./diff-gaussian-rasterization

# for mesh extraction
pip install git+https://github.com/NVlabs/nvdiffrast

# other dependencies
pip install -r requirements.txt
```

### Pretrained Weights

Our pretrained weight can be downloaded from [huggingface](https://huggingface.co/ashawkey/LGM).

For example, to download the fp16 model for inference:
```bash
mkdir pretrained && cd pretrained
wget https://huggingface.co/ashawkey/LGM/resolve/main/model_fp16_fixrot.safetensors
cd ..
```

For [MVDream](https://github.com/bytedance/MVDream) and [ImageDream](https://github.com/bytedance/ImageDream), we use a [diffusers implementation](https://github.com/ashawkey/mvdream_diffusers).
Their weights will be downloaded automatically.

### Inference

Inference takes about 10GB GPU memory (loading all imagedream, mvdream, and our LGM).

```bash
### gradio app for both text/image to 3D
python app.py big --resume pretrained/model_fp16.safetensors

### test
# --workspace: folder to save output (*.ply and *.mp4)
# --test_path: path to a folder containing images, or a single image
python infer.py big --resume pretrained/model_fp16.safetensors --workspace workspace_test --test_path data_test 

### local gui to visualize saved ply
python gui.py big --output_size 800 --test_path workspace_test/saved.ply

### mesh conversion
python convert.py big --test_path workspace_test/saved.ply
```

For more options, please check [options](./core/options.py).

### Training

**NOTE**: 
Since the dataset used in our training is based on AWS, it cannot be directly used for training in a new environment.
We provide the necessary training code framework, please check and modify the [dataset](./core/provider_objaverse.py) implementation!

We also provide the **~80K subset of [Objaverse](https://objaverse.allenai.org/objaverse-1.0)** used to train LGM in [objaverse_filter](https://github.com/ashawkey/objaverse_filter).

```bash
# debug training
accelerate launch --config_file acc_configs/gpu1.yaml main.py big --workspace workspace_debug

# training (use slurm for multi-nodes training)
accelerate launch --config_file acc_configs/gpu8.yaml main.py big --workspace workspace
```

### Acknowledgement

This work is built on many amazing research works and open-source projects, thanks a lot to all the authors for sharing!

- [gaussian-splatting](https://github.com/graphdeco-inria/gaussian-splatting) and [diff-gaussian-rasterization](https://github.com/graphdeco-inria/diff-gaussian-rasterization)
- [nvdiffrast](https://github.com/NVlabs/nvdiffrast)
- [dearpygui](https://github.com/hoffstadt/DearPyGui)
- [tyro](https://github.com/brentyi/tyro)

### Citation

```
@article{tang2024lgm,
  title={LGM: Large Multi-View Gaussian Model for High-Resolution 3D Content Creation},
  author={Tang, Jiaxiang and Chen, Zhaoxi and Chen, Xiaokang and Wang, Tengfei and Zeng, Gang and Liu, Ziwei},
  journal={arXiv preprint arXiv:2402.05054},
  year={2024}
}
```
