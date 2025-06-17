# Ionic-2048 🎮

[English](README_EN.md) | 简体中文

一个基于 Ionic + Vue 3 + TypeScript 开发的现代化 2048 游戏应用，支持跨平台部署（Web、iOS、Android）。

## 🚀 项目特性

- **现代化技术栈**: 基于 Ionic 8、Vue 3 Composition API 和 TypeScript
- **跨平台支持**: 一套代码，支持 Web、iOS、Android 平台
- **响应式设计**: 完美适配手机、平板和桌面设备
- **触摸与键盘支持**: 支持滑动手势和方向键操作
- **本地存储**: 自动保存最高分记录
- **现代化UI**: 采用渐变背景、毛玻璃效果和流畅动画
- **游戏功能完整**: 包含分数系统、游戏结束检测、胜利判断等

## 🎯 游戏规则

- 使用方向键或滑动手势移动数字方块
- 相同数字的方块会合并成一个更大的数字
- 每次移动后会随机生成新的 2 或 4 方块
- 目标是创造出 2048 方块
- 当无法移动且无空位时游戏结束

## 🛠️ 技术栈

### 前端框架
- **Vue 3**: 采用 Composition API 和 `<script setup>` 语法
- **Ionic 8**: 跨平台移动应用开发框架
- **TypeScript**: 提供类型安全和更好的开发体验

### 构建工具
- **Vite**: 快速的构建工具和开发服务器
- **Capacitor**: 原生应用打包和功能集成

### 开发工具
- **ESLint**: 代码质量检查
- **Cypress**: E2E 测试框架
- **Vitest**: 单元测试框架

## 📁 项目结构

```
src/
├── App.vue                 # 应用根组件
├── main.ts                 # 应用入口文件
├── vite-env.d.ts          # Vite 类型声明
├── components/            # 组件目录
│   └── Game2048.vue       # 2048 游戏核心组件
├── router/                # 路由配置
│   └── index.ts           # 路由定义
├── theme/                 # 主题样式
│   └── variables.css      # CSS 变量定义
└── views/                 # 页面视图
    └── HomePage.vue       # 主页面组件
```

## 🚀 快速开始

### 环境要求

- Node.js 18+ 
- npm 或 yarn
- Git

### 安装依赖

```bash
# 克隆项目
git clone https://github.com/zym9863/Ionic-2048.git
cd Ionic-2048

# 安装依赖
npm install
```

### 开发模式

```bash
# 启动开发服务器
npm run dev

# 访问 http://localhost:5173
```

### 构建项目

```bash
# 构建生产版本
npm run build

# 预览构建结果
npm run preview
```

### 移动端开发

```bash
# 添加 iOS/Android 平台
npx cap add ios
npx cap add android

# 构建并同步到原生平台
npm run build
npx cap sync

# 在原生 IDE 中打开
npx cap open ios
npx cap open android
```

## 🧪 测试

```bash
# 运行单元测试
npm run test:unit

# 运行 E2E 测试
npm run test:e2e

# 代码检查
npm run lint
```

## 🎨 游戏组件详解

### Game2048.vue 核心功能

- **游戏状态管理**: 使用 Vue 3 响应式 API 管理游戏板、分数等状态
- **移动逻辑**: 实现上下左右四个方向的方块移动和合并算法
- **触摸控制**: 支持滑动手势识别，触摸距离阈值为 50px
- **键盘控制**: 监听方向键事件，支持桌面端操作
- **动画效果**: CSS3 动画和 CSS 变量实现流畅的视觉效果
- **本地存储**: localStorage 保存最高分数据

### 样式特性

- **现代化配色**: 使用渐变色和半透明效果
- **响应式布局**: 适配不同屏幕尺寸的设备
- **毛玻璃效果**: backdrop-filter 实现现代化视觉体验
- **动画系统**: 方块出现、合并、移动等动画效果

## 📱 平台特性

### Web 端
- 响应式设计，支持桌面和移动端浏览器
- 键盘和触摸双重操作支持
- PWA 就绪，可安装到主屏幕

### 移动端 (iOS/Android)
- 原生性能体验
- 支持设备震动反馈
- 状态栏适配
- 全屏游戏体验

## 🔧 配置文件说明

- `package.json`: 项目依赖和脚本配置
- `vite.config.ts`: Vite 构建配置
- `capacitor.config.ts`: Capacitor 原生平台配置
- `ionic.config.json`: Ionic CLI 配置
- `tsconfig.json`: TypeScript 编译配置

## 🌟 开发亮点

1. **现代化 Vue 3 语法**: 全面使用 Composition API 和 `<script setup>`
2. **类型安全**: TypeScript 提供完整的类型检查
3. **性能优化**: Vite 构建工具确保快速的开发和构建体验
4. **代码规范**: ESLint 确保代码质量和一致性
5. **测试覆盖**: 单元测试和 E2E 测试保证代码质量
6. **跨平台部署**: 一套代码支持多平台发布

## 🎯 未来规划

- [ ] 添加游戏音效和背景音乐
- [ ] 实现游戏回放功能
- [ ] 添加多种游戏模式 (3x3, 5x5, 6x6)
- [ ] 集成在线排行榜
- [ ] 添加游戏主题切换
- [ ] 支持游戏存档和恢复
- [ ] 添加游戏统计数据

## 📄 许可证

本项目采用 MIT 许可证 - 查看 [LICENSE](LICENSE) 文件了解详情。

## 🤝 贡献指南

欢迎提交 Issue 和 Pull Request 来帮助改进项目！

1. Fork 本仓库
2. 创建您的功能分支 (`git checkout -b feature/AmazingFeature`)
3. 提交您的更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启一个 Pull Request

## 📞 联系方式

如有问题或建议，请通过以下方式联系：

- 提交 GitHub Issue
- 发送邮件至项目维护者

---

⭐ 如果这个项目对您有帮助，请给一个 Star 支持一下！
