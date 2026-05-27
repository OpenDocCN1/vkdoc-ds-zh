# 索引

## A
- 累加器（Accumulators）
- 模数转换（ADC，Analog-to-digital conversion）
- 算术运算（Arithmetical operations）
- 数组（Array）
- `Artiste`操作，`ThMAD`全屏模式、全窗口模式、选项、性能模式、停止方法、窗口化模式

## B
- 二元运算符（Binary operators）
- 斑点（Blobs）
  - 不同变化中的基础位图
  - `Perlin`噪声子管道、参数、变换

## C
- 坐标系（Coordinate systems）

## D
- 3D 概念：坐标系、光照
  - 环境光（ambient）、漫反射（diffuse）
  - 材质参数、反射模式
  - 屏幕清除颜色（screen’s clear color）
  - 镜面反射（specular）
  - 空间映射（Space mapping），参见“空间映射”（`SpaceMapping`）
  - `ThMAD`网格（meshes）
  - `ThMAD`粒子系统
  - 平移、旋转和缩放
- `DVD Styler`

## E
- 眼睛和摄像机（Eye and camera）
  - 前述的深度缓存（depth buffer）、背面剔除（backface culling）
  - 设备归一化（device-normalization）
  - 模型视图变换（modelview transformation）
  - 投影变换（projection transformation）
  - 屏幕坐标（screen coordinates）

## F
- 快速傅里叶变换（FFT，Fast Fourier transformation）
- `Float`锚点
- 傅里叶变换（Fourier transformation）

## G, H
- 图形模块选择器（Graphical module chooser）
- `GUI`功能，`ThMAD`
  - `Artiste`助手
  - 桌面、空状态（empty state）
  - 键盘快捷键、鼠标快捷键
  - 全屏模式
  - 主菜单功能
  - 模块选择器，参见“模块选择器”
  - 模块，参见“模块”（Modules）
  - 笔记（notes）
  - 对象检查器（object inspector）
  - 性能模式
  - 保存和加载状态
  - 标准和全窗口模式
  - 启动和停止
  - 窗口模式

## I, J, K
- 逆傅里叶变换（Inverse Fourier transformation）

## L
- 光反射模式（Light reflection modes）
- 线性变换（Linear transformation）

## M, N
- `Macro`锚点
- 主菜单功能（Main menu functions）
- 网格（Meshes）
- 模块选择器（Module choosers）：图形模块列表
- 模块（Modules）
  ### 锚点（anchors）
  连接类型、克隆、连接器、枚举类型输入锚点、`float`类型输入锚点、`float3`类型输入锚点、`float4`类型输入锚点、宏（macros）、放置和删除、四元数（quaternion）类型输入锚点、资源选择器、序列编辑器锚点插值（interpolations）、序列输入、模块参数`float_sequence`模块插值类型、序列编辑器、`string`类型输入锚点、类型、视觉效果

## O
- 对象检查器（Object inspector）
- `Ocean`模块：锚点模块
  - 直接颜色输入流程
  - 图形颜色选择器
  - 输出四重滑块组合（output quadruple slider combo）
  - 天空颜色设置
- 振荡器（Oscillators）
  - `float_sequencer`、`inside_range`、`pulse_oscillator`
- 示波器（Oscilloscopes）

## P, Q
- 粒子系统（Particle systems）
- `Perlin`噪声斑点（blobs）
- `Player`操作，`ThMAD`
  - 推子（faders）
  - 安装
  - 平滑过渡
  - 过渡状态选项
  - 启动、模式
  - 停止方法
- `PulseAudio`输入设备对象、播放对象到`ThMAD`

## R
- 旋转（Rotation）

## S
- 声卡（Sound card）
- 声音输入（Sound input）
- 声音模块（Sound modules）
  - `input_visualization_listener`
  - `midi` → `aka_apc40_controller`
  - `ogg_sample_*`、`raw_sample_*`
- 声音结构（Sound structure）：频率-功率表示、声卡、时间-伸长表示
- 空间映射（Space mapping）
  - 包含变换（embracing transformations）
  - 齐次坐标（homogeneous coordinates）
  - 单位矩阵（identity matrix）
  - 线性变换（linear transformation）
  - 变换组合（transformation combinations）
  - 变换矩阵（transformation matrix）
- `String`模块，系统级模块
- 系统声音（System sounds）

## T
- 三元运算符（Ternary operators）
- 纹理映射（Texture mapping）
  ### 自动纹理坐标（automatic texture coordinates）
  锚点参数、摄像机模块、在立方体上、`glTexGen()`函数、`NORMAL MAP`模式、`OBJECT_LINEAR`模式、在球体上、`SPHERE_MAP`和`REFLECTION_MAP`模式
  ### 基础浮点纹理（basic floating textures）
  锚点参数、失真（distortions）、宏、`mesh_grid`模块、修改器（modifiers）、输出平面、网格生成器、可重用子管道、着色器（shaders）
- `Texture`模块
  - 缓冲区（buffers）
  - 虚拟模块（dummies）
  - 效果（effects）
  - 加载器（loaders）
  - 宏（macros）
  - 修改器（modifiers）
  - `OpenGL`粒子系统
- 纹理（Textures）
  ### 模糊效果
  `blend_mode`模块、模糊失真（blurring distortion）、`colored_-rectangle`模块、`dest_blend`锚点、网格（grid）、`highblur`
  - `Maths` → `oscillators` → `oscillator`
  - `mesh_basic_render`、`mesh_sequ_distort`模块
  - 模块`texture` → `particles` → `blob`
  - `OpenGL`行为
  - 振荡器参数
  - 渲染器（renderers） → `basic` → `textured_rectangle`
  - `texture_out`巨大效果描述对象
  ### 粒子系统
  中心固定（center clamped）、粒子系统发射器类型（emitter types）、图像位粒子（image bit particles）、方法论、`MODIFIER`转换、`RENDERER`转换、带状粒子（ribbon particles）
- `ThMAD`瀑布（waterfall）：自相似性（self-similarity）
  - `ALPHA`值
  - 回馈管道（backfeeding pipeline）
  ### 花式科赫曲线（fancy Koch curve）
  `gl_color`模块、科赫曲线、`mesh_basic_render`模块、参数
  - 毕达哥拉斯树（pythagoras tree）
  ### 渲染和回馈管道
  `render_-surface_single`模块、带状模块（ribbon module）、旋转模块（rotation module）、`set`参数
  - `ThMAD`树（trees）
- `ThMAD`
- `ThMAD Artiste`启动窗口
  - 斑点，参见“斑点”（Blobs）
  - 连接`PulseAudio`声音到 3D 渲染管道
  - 优缺点
  - 锚点值、复杂锚点、`ocean`模块、缩放变换、平移、线框（wireframes）
- 2D 示例（`Artiste`助手模式）：自动模式、复杂锚点、模块浏览器、性能模式、画布上的矩形、采样率、Ubuntu 桌面、抖动矩形
- 3D 示例：锚点值、复杂锚点、详细 3D 场景、输入锚点、`mesh_box`、`orbit_camera`、声音输入、表面材质、表面属性

## U, V
- 安装`ThMAD`模块
- 位图过滤器（bitmap filters）
- 位图生成器（bitmap generators）
- 类别（categories）：数据类型、虚拟模块（dummies）、加载位图（loads bitmaps）
- 数学模块（math modules）
  - 累加器（accumulators）
  - 算术运算（arithmetical operations）
  - 数组（array）
  - 二元运算符（binary operators）
  - 颜色转换器（color converters）
  - 虚拟模块（dummies）
  - 函数（functions）
  - 插值（interpolation）
  - 限制器（limiters）
  - 三元运算符（ternary operators）
  - 一元运算符（unary operators）
- 网格（mesh）：虚拟模块、生成器、导入器、粒子系统（particle system）`segmesh`模块、实体纹理（solid texture）、顶点（vertices）、`Xtra`修改位图（modification of bitmaps）
- 修改器（modifiers）：颜色转换（color converts）、变形器（deformers）、辅助器（helpers）、拾取器（pickers）、变换（transforms）
- `particlesystems`：分形（fractals）、生成器、修改器、渲染器
- 基础网格（basic mesh）：`OpenGL`修改器、示波器（oscilloscopes）、粒子系统（particlesystem）、着色器（shaders）、文本（text）、`Xtra`
- 屏幕选择器（screen selectors）
- `ThMAD Player`：`GUI`操作、键盘操作、启动和停止
- `GUI`
- 时间-伸长表示（Time-elongation representation）
- 工具链（Toolchain）：从录音制作 DVD、录制视频
- `ThMAD`：连接`PulseAudio`声音、安装
- 变换矩阵（Transformation matrix）
- 平移（Translation）

## V
- `Ubuntu` Linux 系统
- 一元运算符（Unary Operators）

## W, X, Y, Z
- 线框模型（Wireframe models）：大规模和动态缩放、锚点、球体锚点、平移锚点、三重值滑块（triple value slider）