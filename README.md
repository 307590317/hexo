## 开发

```bash
# 克隆项目
git clone https://github.com/PanJiaChen/vue-element-admin.git

# 进入项目目录
cd hexoBlog

# 安装依赖
npm install

# 建议不要直接使用 cnpm 安装以来，会有各种诡异的 bug。可以通过如下操作解决 npm 下载速度慢的问题
npm install --registry=https://registry.npm.taobao.org

# 启动服务
hexo server -d
```

浏览器访问 http://localhost:4000/admin/

## 发布

```bash
# 构建生产环境
npm run build:prod
```

Copyright (c) 2017-present zhaoYu
