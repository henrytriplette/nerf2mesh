# Nerf2Mesh
https://github.com/henrytriplette/nerf2mesh/
Torch 1.12 & CUDA 11.6 under Windows

### Required software
``` 
https://github.com/colmap/colmap/releases
https://www.anaconda.com/download
https://developer.nvidia.com/cuda-11-6-0-download-archive?target_os=Windows&target_arch=x86_64&target_version=11&target_type=exe_network
https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=community&rel=16&utm_medium=microsoft&utm_campaign=download+from+relnotes&utm_content=vs2019ga+button
``` 
- Add colmap dir to PATH

### Conda Setup
- Run as admin
``` 
conda create -n nerf2mesh python=3.10
conda activate nerf2mesh
conda install pytorch==1.12.1 torchvision==0.13.1 torchaudio==0.12.1 cudatoolkit=11.6 -c pytorch -c conda-forge
conda install pytorch-scatter -c pyg
``` 

### Clone Repo
``` 
git clone https://github.com/ashawkey/nerf2mesh.git
cd nerf2mesh
``` 

### Install required repository
``` 
pip install -r requirements.txt
pip install git+https://github.com/NVlabs/tiny-cuda-nn/#subdirectory=bindings/torch
pip install git+https://github.com/NVlabs/nvdiffrast/
pip install git+https://github.com/facebookresearch/pytorch3d.git
``` 

### Process Images
``` 
# prepare your video or images under /data/custom, and run colmap (assumed installed):
python scripts/colmap2nerf.py --images ./data/custom/images/ --run_colmap # if use images
# colmap must be available in PATH

# generate downscaled images if resolution is very high and OOM (asve to`data/<name>/images_{downscale}`) 
python scripts/downscale.py data/<name> --downscale 4
# NOTE: remember to append `--downscale 4` as well when running main.py

# perform background removal for single object 360 captures (save to 'data/<name>/mask')
python scripts/remove_bg.py data/<name>/images
# NOTE: the mask quality depends on background complexity, do check the mask!

# recommended options for single object 360 captures
python main.py data/custom/ --workspace trial_custom -O --data_format colmap --bound 1 --dt_gamma 0 --stage 0 --clean_min_f 16 --clean_min_d 10 --visibility_mask_dilation 50 --iters 10000 --decimate_target 1e5 --sdf
# NOTE: for finer faces, try --decimate_target 3e5

python main.py data/custom/ --workspace trial_custom -O --data_format colmap --bound 1 --dt_gamma 0 --stage 1 --iters 5000 --lambda_normal 1e-2 --refine_remesh_size 0.01 --sdf
# NOTE: for finer faces, try --lambda_normal 1e-1 --refine_remesh_size 0.005
``` 