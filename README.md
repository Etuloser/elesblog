# VuePress + GitHub Page + Github Action构建

希望分享一些个人觉得有价值的文章，决定使用`GitHub action`触发`VuePress`自动构建到`GitHub Page`上。

## 初始化VuePress

```bash
corepack enable
mkdir elesblog && cd elesblog
yarn init
yarn add -D vuepress
mkdir docs && echo '# Hello VuePress' > docs/README.md
```

添加一下脚本到`pacakge.json`：

```json
{
  "scripts": {
    "docs:dev": "vuepress dev docs",
    "docs:build": "vuepress build docs"
  }
}
```

创建配置文件：

```bash
mkdir docs/.vuepress
touch config.js
```

```javascript
module.exports = {
  title: "Eles' blog ",
  description: 'Sharing and Recording',
  host: '0.0.0.0',
  port: 30080
}
```

## 配置GitHub Page

1. 创建对应`username.github.io`仓库。

![image.png](https://cdn.nlark.com/yuque/0/2023/png/40378356/1698290710580-0b7146aa-a9bd-4d71-a39a-e8e4f51eb158.png#averageHue=%23f2f4f4&clientId=uce2b78e8-4f61-4&from=paste&height=125&id=sVPbS&originHeight=249&originWidth=725&originalType=binary&ratio=2&rotation=0&showTitle=false&size=24577&status=done&style=shadow&taskId=ub8b80510-cd07-422f-9276-9d0ab6b9127&title=&width=362.5)

2. 生成vuepress静态文件并上传到这个仓库。

```bash
yarn docs:build
cd docs/.vuepress/dist

git init
git add -A
git commit -m 'deploy'

git push -f https://github.com/Etuloser/etuloser.github.io.git master
```

3. 配置Page：

![image.png](https://cdn.nlark.com/yuque/0/2023/png/40378356/1698299246287-81e9b797-8fb3-45bd-bb82-83894297f7e1.png#averageHue=%23dcb88f&clientId=uce2b78e8-4f61-4&from=paste&height=579&id=R6qp8&originHeight=1158&originWidth=1791&originalType=binary&ratio=2&rotation=0&showTitle=false&size=189570&status=done&style=shadow&taskId=uf0c98844-42c9-4f14-9585-f8a83c1c907&title=&width=895.5)
新建secret方便后面action的时候能正常连接这个库。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/40378356/1698302493240-e6e165fd-72ad-4b20-a5ae-e73890a2ae06.png#averageHue=%23dcbb8e&clientId=uf6d6f0d7-68c2-4&from=paste&height=704&id=u6deebf4e&originHeight=1408&originWidth=2396&originalType=binary&ratio=2&rotation=0&showTitle=false&size=268791&status=done&style=shadow&taskId=u54d000d9-6dc5-4a70-a564-d9f9f6d7a7d&title=&width=1198)

## 配置GitHub Actions

1. 新建一个仓库用来存放`elesblog`项目

![image.png](https://cdn.nlark.com/yuque/0/2023/png/40378356/1698301262800-70cf2cb5-1d17-40cf-9a5b-a61081080b80.png#averageHue=%23e7d3ad&clientId=uce2b78e8-4f61-4&from=paste&height=520&id=F6Lv0&originHeight=1039&originWidth=2007&originalType=binary&ratio=2&rotation=0&showTitle=false&size=170446&status=done&style=shadow&taskId=u0ac05a1b-2509-46bd-8e45-6b7c53e6dcf&title=&width=1003.5)

2. 注意`.gitignore`文件配置：

```shell
node_modules/
docs/.vuepress/dist
```

3. 新建`.github/workflows/vuepress-deploy.yml`文件来构建action，要注意修改`ACCESS_TOKEN`选项。

```yaml
name: Build and Deploy
on: [push]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@master

    - name: vuepress-deploy
      uses: jenkey2011/vuepress-deploy@master
      env:
        ACCESS_TOKEN: ${{ secrets.ACCESS_TOKEN }}
        TARGET_REPO: etuloser/etuloser.github.io
        TARGET_BRANCH: main
        BUILD_SCRIPT: yarn && yarn docs:build
        BUILD_DIR: docs/.vuepress/dist
````
