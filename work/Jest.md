# Jest单元测试实践



## 前期准备

### 环境准备

+ **NodeJS**
+ **npm（安装Node的时候自带）**

### 前置知识

+ 熟悉JavaScript
+ 科学上网
+ 熟悉npm基本命令
  + npm init -y
  + npm install xxx 
+ [Jest官网](https://jestjs.io/zh-Hans/)



## 开始实践

1. 新建文件夹：jest-start

2. 初始化Node项目，执行以下命令：

   ```bash
   $ npm init -y
   ```

3. 安装Jest，执行以下命令

   ```bash
   $ npm install jest -D
   ```

4. 配置自动化测试命令

   ```json
   {
     "name": "jest-start",
     "version": "1.0.0",
     "description": "",
     "main": "index.js",
     "scripts": {
       "test": "jest",
       "test:majestic": "majestic"
     },
     "keywords": [],
     "author": "",
     "license": "ISC",
     "devDependencies": {
       "chai": "^4.3.6",
       "chai-http": "^4.3.0",
       "jest": "^27.5.1",
       "majestic": "^1.8.1",
       "mocha": "^9.2.1"
     }
   }
   ```

5. 编写测试脚本

   1. 单元测试的目的
   2. 测试一个单元，是否按照测试人员预定的逻辑执行。

6. 执行jest测试

   1. `npm run jest`

7. Jest测试的UI可视化

   `npm run majestic`