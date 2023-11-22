# lightning-boilerplate

## Prerequisites

### Docker

도커는 NVIDIA Container Toolkit [설치](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html) 후 [도커 허브](https://hub.docker.com/r/pytorch/pytorch/tags)에서 사용할 CUDA와 파이토치 버전 이미지를 다운받는다.

```bash
docker pull pytorch/pytorch:2.0.1-cuda11.7-cudnn8-devel
```

### pyenv

pyenv는 맥/리눅스 버전 [설치](https://github.com/pyenv/pyenv#installation), [윈도우 설치](https://github.com/pyenv-win/pyenv-win#installation) 방법에 따라 파이썬 버전을 관리한다.

```bash
curl https://pyenv.run | bash
```

### Poetry (recommended)

파이썬 의존성 라이브러리 관리툴인 Poetry를 사용할 수 있다. Linux, macOS, Windows (WSL) [설치](https://python-poetry.org/docs/#installing-with-the-official-installer)를 참고한다.

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

### Conda

에휴...ㅜ

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
sh Miniconda3-latest-Linux-x86_64.sh
```

## Lightning Boilerplate

```bash
git clone --depth 1 --branch main https://github.com/unerue/lightning-boilerplate.git ${your-project-name}
cd ${your-project-name}
```

## Poetry package installation

Poetry 사용해 프로젝트 초기화 및 파이토치 등 라이브러리 설치 방법은 다음과 같다. pyenv로 파이썬 전역 버전을 설정한다.

```bash
pyenv install 3.10.13 # Windows 3.10.10
pyenv global 3.10.13
```

`git clone`으로 만들어진 폴더로 진입해 프로젝트를 초기화한다.

```bash
poetry init
poetry config virtualenvs.in-project true --local
poetry run python --version
```

초기화로 생성된 pyproject.toml 파일에 `[tool.poetry.dependencies]`의 파이썬 버전을 `python = "3.10.*"`으로 변경한다. 파이토치를 설치하기 전 소스 경로를 추가한다. 원하는 버전은 [이전 버전](https://pytorch.org/get-started/previous-versions/)에서 확인한 다음 설치한다.

```bash
poetry source add -p explicit pytorch https://download.pytorch.org/whl/cu117
poetry add --source pytorch torch torchvision
poetry run python -c "import torch;print(torch.cuda.is_available())"
```

### Lightning and dev package installation

```
poetry add lightning hydra-core hydra-colorlog rootutils rich python-dotenv
poetry add pytest black mypy isort pre-commit --group dev
```

## Usage of Lightning Boilerplate

Poetry를 기반으로 실행한다. `conda`나 `venv`라면 `python` 명령어로 실행한다.

```bash
pytest test
poetry run python src/train.py
```



```bash
sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev libzma-dev liblzma-dev

git clone https://github.com/pyenv/pyenv.git ~/.pyenv
# For zsh
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
```

### Lighting Trainer API

https://lightning.ai/docs/pytorch/latest/common/trainer.html#trainer-class-api

#### Poetry install package

```bash
poetry add lightning
poetry add black flake8 mypy isort --group dev
```

### Conda

```bash
conda create --name your-env-name --file environment.yaml
conda activate your-env-name
```

https://docs.python.org/3/library/venv.html

```bash
python -m venv .venv
pip install -r requirements.txt
```

```bash
pytest test
python src/train.py
```

Override argument

```bash
python src/train.py trainer.max_epochs=20 model.optimizer.lr=1e-4
```

```bash
python src/train.py debug=default
python src/train.py debug=fdr
```

```bash
python src/train.py experiment=
python src/train.py -m 'experiment=glob(*)'
```

```bash
python src/train.py trainer=gpu +trainer.precision=16
```

Resume training from checkpoints

```bash
python src/train.py ckpt_path="/path/to/ckpt/name.ckpt"
```

Evaluate checkpoints

```bash
python src/eval.py ckpt_path="/path/to/ckpt/name.ckpt"
```

## Poetry version management

### Exact Version

If you don't include any modifiers, Poetry will keep your dependency pinned at that exact version.

```
beepboop = "2.1.7"
```

With that configuration, if a new version of beepboop is released, poetry update will not install it.

## Caret Version

If you use the caret ^ character, Poetry will update to any new version that does not change the leftmost non-zero section.

beepboop = "^2.1.7"
Equivalent to >=2.1.7, \<3.0.0
With the configuration above, poetry update would update beepboop to 2.1.8, 2.2, 2.3, etc. Poetry would not update to beepboop 3.0, because that changes the leftmost non-zero section of the version number from 2 to 3.

zeepzorp = "^0.24.1"
Equivalent to >=0.24.1, \<0.25.0
With the configuration above, poetry update would update zeepzorp to 0.24.2. Poetry would not update to zeepzorp 0.25.0, because that changes the leftmost non-zero section of the version number from 24 to 25.

The caret version modifier is pretty aggressive about which upgraded versions are allowed. This can cause problems if the maintainers of your dependencies introduce breaking changes without incrementing the major version number.

## Tilde Version

The tilde ~ character tells Poetry to allow minor updates. If you specify a major, minor, and patch version, only patch-level changes are allowed. If you specify a major and minor version, again only patch-level changes are allowed. If you specify only a major version, then minor- and patch-level changes are allowed.

beepboop = "~2.1.7"
Equivalent to >=2.1.7, \<2.2.0

beepboop = "~2.1"
Equivalent to >=2.1.0, \<2.2.0

beepboop = "~2"
Equivalent to >=2.0.0, \<3.0.0
The tilde version modifier is less aggressive than the caret version modifier in the upgrades it will allow.

## Wildcard Version

The star * character is a wildcard. Any version number is allowed at the wildcard position.

beepboop = "2.1.\*"
Equivalent to >=2.1.0, \<2.2.0

beepboop = "2.\*"
Equivalent to >=2.0.0, \<3.0.0

beepboop = "\*"
Allows any version. Equivalent to >=0.0.0

```bash
poetry export --without-hashes --format=requirements.txt > requirements.txt
```

```bash
poetry init
poetry config virtualenvs.in-project true --local
poetry env use python3.10
poetry run python --version
poetry install
```
