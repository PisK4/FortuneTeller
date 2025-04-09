# SillyTavern 前端指南

本文档提供 SillyTavern 前端界面的结构分析和主要功能介绍。

## 前端整体结构

SillyTavern 前端采用标准的 Web 应用结构，主要组件包括：

- `index.html` - 主入口页面
- `script.js` - 核心 JavaScript 文件，包含大量功能函数
- `style.css` - 主样式表
- `/scripts` 目录 - 包含更多 JavaScript 模块
- `/locales` 目录 - 包含国际化文件
- `/public` 目录 - 包含前端资源文件

这种结构遵循了现代 Web 应用的最佳实践，便于维护和扩展。

## 文件功能分析

### script.js 核心功能

`script.js` 作为核心 JavaScript 文件，实现了大量功能：

1. **聊天管理功能**
   - 消息发送与显示：`sendTextareaMessage`、`printMessages`
   - 聊天操作：`clearChat`、`deleteLastMessage`、`reloadCurrentChat`
   - 消息格式化：`messageFormatting`

2. **角色管理**
   - 角色选择与切换：`setActiveCharacter`、`getCharacters`
   - 角色编辑：`renameCharacter`、`createOrEditCharacter`
   - 角色数据处理：`characterToEntity`、`getCharacterAvatar`

3. **AI 生成功能**
   - 核心生成：`Generate`、`generateRaw`
   - 流式响应处理：`StreamingProcessor` 类
   - 提示词处理：`substituteParams`、`getStoppingStrings`

4. **UI 交互**
   - 界面控制：`scrollChatToBottom`、`showSwipeButtons`
   - 弹窗管理：`callPopup`
   - 状态显示：`displayOnlineStatus`、`setGenerationProgress`

5. **数据存储**
   - 聊天保存：`saveChat`、`saveChatDebounced`
   - 数据获取：`getChat`、`getSettings`
   - 元数据管理：`saveMetadata`、`updateChatMetadata`

## HTML 界面结构

### 主要 HTML 组件

1. **顶部导航栏**：包含各种设置按钮和控制选项
   ```html
   <div id="top-bar"></div>
   <div id="top-settings-holder">...</div>
   ```

2. **聊天区域**：显示用户与 AI 之间的对话
   ```html
   <div id="chat"></div>
   ```

3. **侧边抽屉菜单**：包含 AI 配置、角色设置等
   ```html
   <div id="left-nav-panel" class="drawer-content fillLeft closedDrawer">...</div>
   ```

4. **消息输入区域**：用户输入消息的文本框和发送按钮
   ```html
   <div id="send_but"></div>
   <textarea id="send_textarea"></textarea>
   ```

## 主要界面区域

### 1. 导航/控制栏
- **抽屉按钮**: 展开侧边菜单，通过 `doDrawerOpenClick` 函数处理点击事件
- **导航图标**: 通过 `doNavbarIconClick` 函数处理导航菜单交互
- **模式切换按钮**: 用于切换不同的界面模式，如视觉小说模式
- **面板切换**: 通过 `doTogglePanels` 函数控制 UI 面板的显示/隐藏

### 2. 角色管理区
- **角色选择按钮**: 通过 `selectCharacterById` 和 `printCharacters` 函数实现角色选择
  ```javascript
  $('#rm_button_characters').click(function () {
      selected_button = 'characters';
      select_rm_characters();
  });
  ```
- **创建角色按钮**: 通过 `select_rm_create` 函数处理创建新角色的流程
- **编辑角色按钮**: 通过 `createOrEditCharacter` 函数处理角色编辑
- **删除角色按钮**: 通过 `handleDeleteCharacter` 和 `deleteCharacter` 函数处理角色删除
- **收藏按钮**: 通过 `updateFavButtonState` 函数管理角色收藏状态

### 3. 聊天界面
- **消息区域**: 使用 `printMessages` 和 `addOneMessage` 函数显示聊天内容
- **滑动控制**: 通过 `swipe_left` 和 `swipe_right` 函数处理消息滑动交互
- **发送按钮**: 通过 `sendTextareaMessage` 函数处理消息发送
  ```javascript
  $('#send_but').on('click', function () {
      sendTextareaMessage();
  });
  ```
- **清除聊天按钮**: 通过 `clearChat` 函数清除当前聊天
- **删除最后消息按钮**: 通过 `deleteLastMessage` 函数删除最后一条消息
- **滚动至底部按钮**: 通过 `scrollChatToBottom` 函数滚动到聊天底部
- **消息编辑按钮**: 
  ```javascript
  $(document).on('click', '.mes_edit', async function () {
      // 编辑消息的代码
  });
  ```
- **消息删除按钮**: 
  ```javascript
  $(document).on('click', '.mes_edit_delete', async function (event, customData) {
      // 删除消息的代码
  });
  ```

### 4. 生成控制区
- **生成按钮**: 通过 `Generate` 函数启动 AI 生成过程
- **停止生成按钮**: 通过 `stopGeneration` 函数中断生成
- **进度指示器**: 通过 `setGenerationProgress` 函数更新生成进度
- **流式响应控制**: 由 `StreamingProcessor` 类管理流式响应

### 5. 设置面板
- **API 连接设置**: 包含连接不同 API 的选项
  ```javascript
  $('#rm_button_settings').click(function () {
      selected_button = 'settings';
      selectRightMenuWithAnimation('rm_api_block');
  });
  ```
- **主题设置**: 允许自定义 UI 颜色和背景
- **生成参数设置**: 通过 `setGenerationParamsFromPreset` 函数加载预设参数

### 6. 扩展功能区
- **文本转语音按钮**: 通过 `cancelTtsPlay` 函数管理 TTS 功能
- **图像生成控制**: 集成自动绘图 API 的界面元素
- **世界信息面板**: 管理 lorebooks 的界面元素

## 详细交互功能

### 聊天功能
- **发送消息**: 用户在文本框中输入消息并点击发送按钮
- **接收 AI 回复**: 系统处理用户输入并显示 AI 的回复
- **消息编辑**: 用户可以编辑已发送的消息
- **消息删除**: 用户可以删除消息
- **消息复制**: 用户可以复制消息内容

### 角色管理
- **选择角色**: 用户可以从角色列表中选择对话角色
- **创建角色**: 用户可以创建新的角色，包括上传头像和设置特性
- **编辑角色**: 用户可以编辑现有角色的属性
- **删除角色**: 用户可以删除角色

### 聊天历史管理
- **保存聊天**: 系统自动保存聊天历史，通过 `saveChat` 和 `saveChatDebounced` 函数实现
- **加载聊天**: 用户可以加载之前的聊天记录
- **导出聊天**: 用户可以导出聊天记录
- **搜索聊天**: 用户可以搜索聊天记录

### AI 设置
- **配置 AI 参数**: 用户可以调整各种 AI 生成参数（如温度、重复惩罚等）
- **预设管理**: 用户可以保存和加载 AI 设置预设
- **API 选择**: 用户可以选择不同的 AI 后端（如 OpenAI、NovelAI 等）

## 界面交互特点

1. **响应式设计**: 适应不同屏幕尺寸，支持移动设备和桌面设备
2. **自定义主题**: 支持自定义颜色、背景图片和 CSS 样式
3. **流式响应**: 支持 AI 回复的实时流式显示
4. **多语言支持**: 通过 locales 目录支持多种语言
5. **扩展系统**: 支持通过第三方扩展增强功能

## 国际化支持

项目通过 locales 目录下的 JSON 文件提供多语言支持，包括中文（zh-cn.json）。界面元素使用 data-i18n 属性标记，然后通过 i18n.js 脚本进行翻译，实现无缝的多语言切换体验。

## 前端技术栈

1. **JavaScript 框架**: 主要使用 jQuery 进行 DOM 操作和事件处理
2. **CSS 框架**: 使用自定义 CSS 样式和一些组件库
3. **其他库**:
   - showdown: Markdown 转换
   - DOMPurify: HTML 净化
   - toastr: 通知提示
   - select2: 增强的下拉选择框

## 开发注意事项

开发或修改 SillyTavern 前端时，应注意以下几点：

1. 保持与现有代码风格一致
2. 遵循函数命名规范
3. 确保新功能与现有扩展系统兼容
4. 考虑不同 API 后端的兼容性
5. 测试在不同设备和浏览器上的表现

通过理解 SillyTavern 前端结构，开发者可以更有效地进行功能扩展和界面优化，为用户提供更好的 AI 聊天体验。 
