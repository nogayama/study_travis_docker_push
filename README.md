# study travis docker build


## travis を設定



1. create github repository => `study_travis`

2. add  `.travis.yml`  ファイル名が一字一句合ってる必要あり

    ```yaml
    language: bash
    script:
    - echo hello
    ```

3. dir
    ```
    ├── .travis.yml
    └── tests
        └── mytest.sh
    ```

4. https://travis-ci.org にログイン

5. sync accountをクリックし，Githubの情報を取得

    ![a](docs/a.png)

6. オンにする

    ![b](docs/b.png)

7.  自動的に，webhookがオンになる

    ![b](docs/b.png)

8. pushするたび，テストが travis で実行される


### APIでビルドをトリガーする

1. TravisのプロファイルからTokenをコピーする

2. curlで以下のものを送る






## docker build

https://docs.travis-ci.com/user/docker/



```yaml
language: bash

services:
  - docker

before_install:
  - docker build -t nogayama/testimg:latest .

script:
  - tests/mytest.sh

```



これだけで，ビルド自体は完了するようだ．

```bash
# tests/mytest.sh
docker image list
exit 0

```

docker コマンド系が使える

## docker push

1. `gem install travis`
2. `travis login --org`
    1. Github.comのusername, passwordを入力

1. edit `.travis.yml` by `travis` command
	
	```bash
	$ travis encrypt DOCKER_USERNAME=nogayama --add env.global
	$ travis encrypt DOCKER_PASSWORD=PASSWORD --add env.global
	```
  
    - see [Encrypting environment variables](https://docs.travis-ci.com/user/environment-variables#encrypting-environment-variables)

1. add docker login to Dockerfile
1. add docker push. see [pushing-a-docker-image-to-a-registry](https://docs.travis-ci.com/user/docker/#pushing-a-docker-image-to-a-registry)

    ```dockerfile
    language: bash
    
    env:
      global:
      - secure: UrIeCrb ... gy4o=
      - secure: nbBGdzaz ... GGs4=
    
    services:
    - docker
    
    before_install:
    - docker image build --tag nogayama/myapp1:latest .
    - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
    - docker image push nogayama/myapp1:latest
    
    script:
    - echo hello
    ```

1. The logs at Travis-CI https://travis-ci.org/nogayama/myapp1 was
  
    ```
    Worker information
    60.11s0.01s0.00s0.01s
    system_info
    7Build system information
    341
    3420.02s0.00s0.38s0.50s1.03s0.00s0.04s0.00s0.01s0.01s0.01s0.01s0.01s0.00s0.00s0.03s0.00s0.01s0.39s0.00s0.00s0.00s0.01s0.00s0.07s0.00s0.01s0.00s0.02s0.04s0.00s5.07s0.00s1.20s
    docker_mtu
    343docker stop/waiting
    resolvconf
    345resolvconf stop/waiting
    services
    3473.02s$ sudo service docker start
    git.checkout
    3490.62s$ git clone --depth=50 --branch=master https://github.com/[secure]/myapp1.git [secure]/myapp1
    353
    3540.02s
    355Setting environment variables from repository settings
    356$ export DOCKER_USERNAME=[secure]
    357
    358Setting environment variables from .travis.yml
    359$ export DOCKER_USERNAME=[secure]
    360$ export DOCKER_PASSWORD=[secure]
    361
    362$ bash -c 'echo $BASH_VERSION'
    3634.3.11(1)-release
    364
    before_install.1
    3650.51s$ docker image build --tag [secure]/myapp1:latest .
    before_install.2
    3790.56s$ echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
    before_install.3
    3812.39s$ docker image push [secure]/myapp1:latest
    3840.00s$ echo hello
    385hello
    386The command "echo hello" exited with 0.
    387
    388
    389Done. Your build exited with 0.
    
    ```

1. confirm the image is pushed on [Docker Hub](https://hub.docker.com).



