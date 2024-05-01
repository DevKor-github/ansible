# 초기 설정하기

- Python 3.12.2 버전을 사용합니다. (`.python-version`을 참고하세요)
- Global과 섞이지 않게 [venv](https://docs.python.org/3/library/venv.html) 사용을 추천합니다.

## venv 생성하기

```sh
python3 -m venv venv
# Linux / macOS에서 bash, zsh을 사용하는 경우 기준입니다.
# 구체적인 venv의 사용법은 위의 공식문서를 참고하세요.
source venv/bin/activate
```

## Requirements 설치하기

```sh
python3 -m pip install -r requirements.txt
```

## Docker Swarm Cluster 생성하기

```sh
ansible-playbook -i inventory/sample/hosts.yml cluster.yml -b -v \
    --private-key=<path-to-ssh-key>
```
