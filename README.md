# POOR-MAN FLUX
## Or how to run FLUX on very old hardware

I have managed to have the new image-to-text kid on the block, [FLUX](https://huggingface.co/black-forest-labs/FLUX.1-schnell), running on a 2012 Xeon processor with 16 GB of RAM and no usable GPU.

This is how:

CREATE A WORKING DIRECTORY

```
mkdir sdapp
cd sdapp
```

BUILD STABLE DIFFUSION CPP

```
git clone --recursive https://github.com/leejet/stable-diffusion.cpp
cd stable-diffusion.cpp

mkdir build
cd build

cmake ..
cmake --build . --config Release
```

DOWNLOADS THE MODELS

```
mkdir models
cd models

wget -c https://huggingface.co/aifoundry-org/FLUX.1-schnell-Quantized/resolve/main/flux1-schnell-Q4_K.gguf

wget -c https://huggingface.co/black-forest-labs/FLUX.1-schnell/resolve/main/ae.safetensors

wget -c https://huggingface.co/comfyanonymous/flux_text_encoders/resolve/main/clip_l.safetensors

wget -c https://huggingface.co/comfyanonymous/flux_text_encoders/resolve/main/t5xxl_fp8_e4m3fn.safetensors

cd ..
```

TEST IT

```
mkdir output

sudo sh -c 'echo 3 >/proc/sys/vm/drop_caches'

./bin/sd --diffusion-model ./models/flux1-schnell-Q4_K.gguf --vae ./models/ae.safetensors --clip_l ./models/clip_l.safetensors --t5xxl ./models/t5xxl_fp8_e4m3fn.safetensors -p "a frog holding a sign saying 'hi' " -o ./output/gates_dr_evil.png -v --cfg-scale 1.0 --sampling-method euler -v --seed 42 --steps 4
```
This takes a few minutes and produces something like

![this frog](https://huggingface.co/aifoundry-org/FLUX.1-schnell-Quantized/resolve/main/examples/flux_frog_Q4_K.png)

