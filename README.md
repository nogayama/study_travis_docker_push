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
5. add docker push. see [pushing-a-docker-image-to-a-registry](https://docs.travis-ci.com/user/docker/#pushing-a-docker-image-to-a-registry)

    ```dockerfile
    language: bash
    
    env:
      global:
        - secure: cuptxK1...
        - secure: m8sfhDe...
    
    services:
      - docker
    
    before_install:
      - echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
      - docker image build -t nogayama/testimg:latest .
      - docker image push     nogayama/testimg:latest
    
    script:
      - tests/mytest.sh
    
    ```

1. confirm the image is pushed on [Docker Hub](https://hub.docker.com).



