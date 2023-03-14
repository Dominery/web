# 微前端

微前端是一种软件架构，可以将前端应用拆解成一些更小的能够独立开发部署的微型应用，然后再将这些微应用进行组合使其成为整体应用的架构模式。

微前端架构类似于组件架构，但不同的是，组件不能独立构建和发布，但是微前端中的应用是可以的。微前端架构与框架无关，每个微应用都可以使用不同的框架。

**优势**

1. 增量迁移
2. 独立发布
3. 允许单个团队做出技术决策

## single-spa

single-spa是一个实现微前端架构的框架。在single-spa框架中有三种类型的微前端应用:
1. single-spa-application / parcel:微前端架构中的微应用，可以使用vue、react、angular等框架。
2. single-spa root config:创建微前端容器应用。
3. utility modules:公共模块应用，非渲染组件，用于跨应用共享javascript逻辑的微应用。

### 创建容器应用

1. 安装single-spa脚手架工具：`npm install create-single-spa`
2. 创建微前端应用目录：`mkdir workspace`
3. 创建微前端容器应用：create-single-spa
4. 启动应用：npm start
5. 