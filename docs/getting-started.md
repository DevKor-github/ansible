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
ansible-galaxy collection install community.docker
```

## Docker Swarm Cluster 생성하기

> [!WARNING]
> 이 과정에서 Docker가 재시작됩니다.

```sh
ansible-playbook -i inventory/sample/hosts.yml cluster.yml -b -v \
    --private-key=<path-to-ssh-key>
```

### Token 가져오기

SSH로 `managers[0]`에 접근하여 Token을 가져와야 합니다.
`manager`와 `worker` Token이 있으며 각각 아래 명령어를 사용하여 찾을 수 있습니다.

```sh
docker swarm join-token manager
docker swarm join-token worker
```

### Secret에 저장하기

1. 아래 명령어를 사용해 비밀번호를 생성합니다.
    꼭 이 방식으로 비밀번호를 생성할 필요는 없습니다.

    ```sh
    openssl rand -hex 8
    ```

2. 비밀번호를 `password_file`에 적습니다.

3. `secrets.yml`을 작성합니다.

    ```yaml
    joinToken:
        manager: <redacted>
        worker: <redacted>
    ```

4. 아래 명령어를 사용해 암호화합니다.

    ```sh
    ansible-vault encrypt secrets.yml --vault-password-file password_file
    ```

5. 암호화가 잘 되었는지 확인합니다.

    ```sh
    cat secrets.yml
    ```

## Manager 추가하기

Inventory의 `managers:` 에 node를 추가합니다.

> [!WARNING]
> 이 과정에서 Docker가 재시작됩니다.
> 따라서 `--limit`으로 대상 노드를 추가되는 노드로 제한하는 것이 좋습니다.

```sh
ansible-playbook -i inventory/sample/hosts.yml manager.yml -b -v \
    -e @secrets.yml --vault-password-file password_file \
    --private-key=<path-to-ssh-key>
```

## Worker 추가하기

Inventory의 `workers:` 에 node를 추가합니다.

> [!WARNING]
> 이 과정에서 Docker가 재시작됩니다.
> 따라서 `--limit`으로 대상 노드를 추가되는 노드로 제한하는 것이 좋습니다.

```sh
ansible-playbook -i inventory/sample/hosts.yml worker.yml -b -v \
    -e @secrets.yml --vault-password-file password_file \
    --private-key=<path-to-ssh-key>
```

