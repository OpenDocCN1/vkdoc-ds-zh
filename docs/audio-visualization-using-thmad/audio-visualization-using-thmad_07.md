# 6. 故事：高级篇

在本章中，我们将通过更详细的演练，深入探讨音频可视化的艺术，涵盖各个方面。我们绝不可能描述所有可能的草图类型，但本章的目标是为你提供一些技巧，这些技巧最终将帮助你实现自己的想法。各节之间没有特定顺序，因此你可以按顺序逐一阅读，或者交叉阅读——选择最适合你的方式即可。

## 回馈纹理

纹理可以描述为存储在图形硬件上的二维有序颜色集合。它们可以自由裁剪、拉伸、旋转、平移或以其他方式变换，最后一步渲染到输出屏幕上任意形状的多边形集合中。

你甚至可以更进一步，将纹理颜色误用于你能想到的任何用途，因为在图形硬件上运行的着色器程序内部，我们可以利用每个纹理坐标上的这四个颜色值做很多事情。考虑到这一点，聪明的人们很快意识到，纹理的使用场景远不止将图像覆盖到 3D 对象上。

ThMAD 提供了一种极其强大且通用的能力，可以将渲染数据回馈到渲染管线的更早阶段。这是可行的，因为图像数据可以被复制到纹理中，而该纹理可用于需要纹理作为输入的模块。实现这种回馈的模块叫做 `Texture → Buffers → render_surface_single`。关于此模块的技术描述，请参见第 8 章。

### 二维模糊

模糊使可视化效果看起来更华丽，也更有趣。这里的目标不是让对象变得不清晰，而是为它们引入一些光泽或创建特殊效果。

**注意**
此处显示的各种状态，其源文件位于 `TheArtOfAudioVisualization` 文件夹中的 `A-6.1.1_Blurring_*` 下。

目前，我们仅将纹理颜色值视为颜色，并希望利用这种回馈技术来实现模糊效果。

一个提醒：回馈以帧速率发生，即每秒 60 次。这使得使用回馈的状态对参数极其敏感。其中一些参数的微小变化可能会产生巨大的影响，也可能导致屏幕变得无聊、全黑或全白，让你成为失望的用户，需要花费数小时重新调整才能再次看到有意义的内容。因此，我会尽量详细解释所需的一切，以帮助你获得令人满意、甚至良好或极佳的体验。当然，尝试修改参数最终会让你体会到这种脆弱性，但一个经验法则是，你可以将这些修改的幅度控制在至少比你之前在 ThMAD 中习惯的数值小十倍以上。

我们从在屏幕上渲染一个旋转的矩形开始。为此，请打开一个干净的画布。如果画布不是偶然为空的，请右键单击空白处，然后选择 `New → Empty Project`。然后选择 `Save As...`。后者是为了确保你不会意外覆盖之前的工作。然后在屏幕上选择 `Renderers → Basic → colored_rectangle`。参数见表 6-1。

**表 6-1.**

| `renderers → basic → colored_rectangle` |
| :--- |
| spatial/ size | 0.77, 0.11, 0.0 |
| color | 0.2, 0.4, 0.9, 1 | 第四个值是 `ALPHA` 值，即不透明度。此处为 1.0。当 `ALPHA` 小于 1.0 时，需要调整其他一些值才能达到显著的模糊效果。除此之外，可自由选择。 |

在其旁边放置一个模块，选择 `Renderers → opengl_modifiers → gl_rotate`。将其连接到 `colored_rectangle` 模块。

在其旁边放置一个模块，选择 `Maths → oscillators → oscillator`。将振荡器的 `float` 锚点连接到 `gl_rotate` 的 `angle` 锚点。正确参数见表 6-2。

**表 6-2.**

| `maths → oscillators → oscillator` |
| :--- |
| osc/ freq | 0.10 |

在其旁边放置一个模块 `Renderers → opengl_modifiers → blend_mode`。将 `blend_mode` 模块连接到 `gl_rotate` 模块。参数见表 6-3。

**表 6-3.**

| `renderers → opengl_modifiers → blend_mode` |
| :--- |
| source_blend | `SRC_ALPHA` |
| dest_blend | `ONE` |

振荡器和混合模式模块在本书中是首次出现。虽然你可以在第 8 章找到技术细节，但这里值得一提几点。振荡器产生一个振荡的浮点值，我们用它来改变所绘制矩形的角度。这对于实际观察模糊效果的完整作用很重要。虽然模糊对静态对象也有效，但对于移动对象，效果会明显得多。然而，更重要的是混合模式模块。我们需要它来混合或融合来自矩形模块的原始像素数据，以及我们即将描述和添加的模糊子流水线的回馈数据。如果选择了错误的混合模式，你将看到错误的结果，或者可能什么也看不到。至于为什么我们选择 `SRC_ALPHA` 和 `ONE`，稍后会解释。现在我们继续描述状态。

将 `blend_mode` 模块连接到屏幕模块。现在你应该看到旋转的矩形，如图 6-1 所示。

![A457467_1_En_6_Fig1_HTML.jpg](img/A457467_1_En_6_Fig1_HTML.jpg)

**图 6-1.** 模糊矩形（第一部分）

接下来，我们开始构建反馈渲染器。为此，将模块 `Mesh → solid → mesh_grid` 放置在画布上。稍后，这将允许你将反馈纹理投射到一个平面矩形网格上。我们在这里使用网格而不是简单矩形，原因在于网格能产生更有趣的扭曲效果。更多细节稍后介绍。参数请参见表 [6-4]。

1. 在旁边放置模块 `Mesh → texture → mesh_tex_sequ_distort` 并将它们连接起来。稍后，这将允许我们添加扭曲效果。目前，保持参数不变。
2. 在旁边放置模块 `Renderers → mesh → mesh_basic_render`，并将 `mesh_tex_sequ_distort` 的输出锚点连接到其输入锚点 `mesh_in`。此模块负责渲染 `mesh_grid` 网格。将其所有参数保持为默认值。
3. 在旁边放置模块 `Renderers → opengl_modifiers → gl_scale_one`，并将其连接到 `mesh_basic_render` 的输出。这会引入缩放效果，实际上非常重要。为了实现模糊，需要渲染图形的大小与反馈图形的大小紧密匹配。这里我们需要一个 2.0 的缩放比例，设置在 `scale` 锚点处。请参见表 [6-5]。缩放因子 2 源于 `colored_rectangle` 模块（它在 2 x 2 的矩形内绘制）与纹理坐标空间范围（读取 1 x 1）之间的差异。

**表 6-5.**

| `renderers → opengl_modifiers → gl_scale_one` |
| :--- |
| `scale` | 2.0 |

4. 将 `gl_scale_one` 的输出锚点连接到 `blend_mode` 模块的输入锚点 `render_in`。请参见图 [6-2]。

![A457467_1_En_6_Fig2_HTML.jpg](img/A457467_1_En_6_Fig2_HTML.jpg)

**图 6-2.** 模糊矩形（第二部分）

5. 这是引入反馈模块的关键点。该模块名为 `texture → buffers → render_surface_single`，它将允许将图形数据发送到纹理，而不是屏幕输出。在内部，会使用两个纹理来模拟 OpenGL 的双缓冲行为。后者意味着 OpenGL 渲染首先在隐藏的像素数据缓冲区中进行，只有在所有内容渲染完毕后，才会快速复制到物理屏幕上。这是为了避免闪烁。将模块放置在 `blend_mode` 附近，并从 `blend_mode` 模块的输出端向 `render_surface_single` 模块的输入端 `render_in` 绘制第二条连接。
6. 许多模块允许其输出被复用——在此例中，我们将输出从 `blend_mode` 同时渲染到屏幕和纹理缓冲区。确保 `options` 复合锚点下的 `support_feedback` 子锚点设置为“yes”。请参见表 [6-6]。

**表 6-6.**

| `texture → buffers → render_surface_single` |
| :--- |
| `options / support_feedback` | yes |
| `options / texture_size` | VIEWPORT_SIZE |
| `options / support_feedback` | yes （对于反馈功能正常工作非常重要。） |
| `options / clear_color` | 0, 0, 0, 0 （如果不全为零，背景将不会保持黑色） |

7. 在 `render_surface_single` 之后放置模块 `texture → effects → highblur` 并将它们连接起来。按照表 [6-7] 设置 `highblur` 的锚点。

**表 6-7.**

| `texture → effects → highblur` |
| :--- |
| `translation` | 0.43, -0.65, 0 （引入平移模糊效果，类似风吹） |
| `blowup_center` | 0.5, 0.5, 0 （爆破中心点） |
| `blowup_rate` | 80.0 （爆破速率） |
| `attenuation` | 200.0 （模糊衰减速率） |
| `texture_size` | VIEWPORT_SIZE |

关于此模块的更多细节可在第 [8] 章中找到。

**表 6-4.**

| `mesh → solid → mesh_grid` |
| :--- |
| `plane` | xy |

从 `highblur` 的 `texture_out` 锚点绘制一条连接到 `mesh_basic_render` 的 `tex_a` 输入锚点。反馈连接在此阶段实际上已经完成，但有一点很重要值得提及：数据进入 `blend_mode` 模块的顺序。绝对有必要让已渲染矩形的数据首先到达混合模式模块，然后反馈数据到达。否则，将不会产生模糊效果。为确保顺序正确，请双击 `blend_mode` 的输入锚点，如果顺序错误，可通过单击并拖动其中一个锚点来更改。请参见图 [6-3]。

![A457467_1_En_6_Fig3_HTML.jpg](img/A457467_1_En_6_Fig3_HTML.jpg)

**图 6-3.** 模糊矩形（第三部分）—— 检查并修正混合顺序。`gl_rotate` 来自矩形的渲染器，`gl_scale_one` 来自反馈渲染器。`blend_mode` 的输入锚点已通过双击打开，可以通过单击并拖动其中一个锚点来更改连接的顺序。

**警告**
当存在多个传入图形（渲染器）连接时，输入顺序可能至关重要，因为该顺序只有在双击锚点后才会可见。因此，当你的流水线无法按预期工作时，请牢记这一点。

![A457467_1_En_6_Fig4_HTML.jpg](img/A457467_1_En_6_Fig4_HTML.jpg)

**图 6-4.** 模糊矩形（第四部分）—— 最终输出

如果一切设置正确，旋转的矩形将被模糊，如图 [6-4] 所示。完整的状态如图 [6-5] 所示。

![A457467_1_En_6_Fig5_HTML.jpg](img/A457467_1_En_6_Fig5_HTML.jpg)

**图 6-5.** 模糊矩形完整状态

我之前说过，在 `blend_mode` 模块中设置的混合模式很重要。关于此模块（包括所有锚点）的简明描述在第 [8] 章的参考资料中给出，但此处先给出解释。`dest_blend` 锚点描述的是像素缓冲区中当前可见的内容，由于矩形的主渲染器是输入顺序中的第一个，因此 `dest_blend` 描述的是传入的完整且未模糊的矩形。此处设置的 `ONE` 值仅表示它不经改变地进入混合过程。第二个锚点 `source_blend` 连接到绘制在已有内容之上的内容，我们在此将其设置为 `ONE`，意味着我们只是将它们叠加。由于根据定义，源数据来自模糊子流水线，我们将首先绘制未模糊的数据，然后将模糊的数据叠加在其上。请注意，在某些情况下，涉及的颜色值和混合模式（包括顺序）可能会根据你的实际草图而变化。

为了展示一个比矩形或许更有趣的模糊效果，并了解如何通过纹理扭曲进行调整，请执行以下操作：

1. 断开与 `colored_rectangle` 的连接：右键单击连接线并选择“断开连接”。或者删除 `colored_rectangle`，右键单击选择它，然后按键盘上的 DEL 键。
2. 添加模块 `Texture → particles → blob` 并按照表 [6-8] 设置其参数。

**表 6-8.**

| `texture → particles → blob` |
| :--- |
| `settings / arms` | 5 |
| `settings / attenuation` | 7.0 |
| `settings / star_flower` | 0.4 |
| `settings / color` | 0.4, 0.7, 0.1, 1.0 （任意你喜欢的颜色，但不要太亮，以免过饱和） |
| `size` | VIEWPORT_SIZE |

3. 添加模块 `Renderers → basic → textured_rectangle` 并将其连接到 blob。其参数请参见表 [6-9]。

**表 6-9.**

| `renderers → basic → textured_rectangle` |
| :--- |
| spatial / size | 0.6 |
| color / global_alpha | 0.95 |

4. 将 `textured_rectangle` 连接到 `blend_mode` 的输入端。再次确保 `gl_rotate` 模块的连接位于 `gl_scale_one` 模块连接的上方；参见图 6-3。此时的输出结果将如图 6-6 所示。让我们通过为模糊方向添加一些动态效果，使其变得更有趣。为此，请遵循以下步骤：

![A457467_1_En_6_Fig6_HTML.jpg](img/A457467_1_En_6_Fig6_HTML.jpg)

**图 6-6.** 模糊星形 — 第一部分

5. 将 `Maths → converters → 3float_to_float3` 模块放置在 `highblur` 附近。将其输出连接到 `translation` 锚点。
6. 在其旁边放置另一个 `Maths → oscillators → oscillator`。记住，我们已有一个用于旋转模块的振荡器。在第二个振荡器内部，按表 6-10 所示设置参数。

**表 6-10.**

| `maths → oscillators → oscillator` |
| :--- |
| osc / freq | 1.0 |
| osc / amp | 0.25 |

将其连接到 `3float_to_float3` 的 `floata` 和 `floatb` 两个输入锚点。此时，模糊效果应能周期性地改变方向，如图 6-7 所示。最后一步，我们为模糊效果添加扭曲：

![A457467_1_En_6_Fig7_HTML.jpg](img/A457467_1_En_6_Fig7_HTML.jpg)

**图 6-7.** 模糊星形 — 第二部分

7. 将 `gl_rotate` 附近振荡器的 `osc_type` 子锚点更改为“SAW”。由于振荡器的内部时间值从 0.0 到 1.0，且 `gl_rotate` 的“angle”输入锚点将 1.0 映射为完整旋转，因此锯齿形状有效地实现了持续旋转效果。参见表 6-11。

**表 6-11.**

| `maths → oscillators → oscillator` (位于 `gl_rotate` 附近) |
| :--- |
| osc / freq | 0.10 |
| osc / type | SAW |

8. 将 `blob` 的 `arms` 输入锚点改为 6.0，以改善对称性。参见表 6-12。

**表 6-12.**

| `texture → particles → blob` |
| :--- |
| settings / arms | 6 |
| settings / attenuation | 7.0 |
| settings / star_flower | 0.4 |
| settings / color | 0.4, 0.7, 0.1, 1.0 | 任意喜欢的颜色，但不宜过亮，以免过饱和 |
| size | VIEWPORT_SIZE |

9. 打开 `mesh_sequ_distort` 模块的“distortion”复合锚点。通过双击打开 `u_shape` 和 `v_shape` 两个子锚点，并在序列编辑器中添加一些值锚点：在线上 Shift 点击，然后点击并拖动值锚点，如图 6-8 所示。另请参阅第 7 章。

![A457467_1_En_6_Fig8_HTML.jpg](img/A457467_1_En_6_Fig8_HTML.jpg)

**图 6-8.** 模糊星形 — 第三部分：模糊扭曲

所有操作完成后的结果可能如图 6-9 所示。通过更改模块的锚点值，还可以产生许多其他有趣的效果。可能性几乎是无限的，因此请随意调整这些值。当然，你也可以轻松地为所有这些添加声音输入。只需实例化 `input_visualization_listener` 并使用其输出锚点来控制你能想到的任何参数。

![A457467_1_En_6_Fig9_HTML.jpg](img/A457467_1_En_6_Fig9_HTML.jpg)

**图 6-9.** 模糊星形 — 第四部分：模糊扭曲输出

刚才展示的扭曲效果有一个漂亮的变体：将 `mesh_tex_sequ_distort` 模块替换为 `Mesh → texture → mesh_tex_bitmap_distort`，并添加一个额外的模块 `Bitmaps → loaders → png_bitm_load`，用于加载 `[HOME]/thmad/[VERSION]/data/resources` 文件夹中的任何 PNG 文件。见图 6-10 和 6-11。你还可以让声音输入控制扭曲速率。

![A457467_1_En_6_Fig11_HTML.jpg](img/A457467_1_En_6_Fig11_HTML.jpg)

图 6-11. 通过位图实现的模糊扭曲 — 输出

![A457467_1_En_6_Fig10_HTML.jpg](img/A457467_1_En_6_Fig10_HTML.jpg)

图 6-10. 通过位图实现的模糊扭曲 — 状态变化

注意：位于 `TheArtOfAudioVisualization` 文件夹内的 `A-6.1.1_Blurring_Star_E`。

## 自相似性

`自相似性`在自然界和艺术中都是极其重要的特征。就自然界而言，化学和生物过程都受其影响；而在艺术领域，你会在绘画、建筑、音乐、诗歌、摄影和设计中随处可见它的身影。

`自相似性`究竟是什么意思？它指的是在分析的不同层面上涌现出相同或相似种类与形状的结构。以一棵树为例。从远处看，你能看到树干和模糊的树冠。再靠近一点，或者深入一个层次观察，就能看到从树干分出的主要枝干。再近一些，再深入一个层次，就能看到从主要枝干分出的较小枝干。继续这个过程，最终能看到从最小枝干分出的树叶。因此，树木本质上展示了`自相似性`；另见图 6-12。或者观察一片雪花，它在不同层级上同样存在分形。

![A457467_1_En_6_Fig12_HTML.jpg](img/A457467_1_En_6_Fig12_HTML.jpg)

图 6-12. 树木中的自相似性

这两种自然现象都有其人工对应物。树木的历史悠久，毕达哥拉斯通过一个简单的构造规则构建了一棵分形树——只需在一个给定的方形上以固定角度添加两个较小的方形，并重复这一过程即可；见图 6-13。

![A457467_1_En_6_Fig13_HTML.jpg](img/A457467_1_En_6_Fig13_HTML.jpg)

图 6-13. 一棵毕达哥拉斯树。每个线段分叉成两个大小为原线段 75%的线段，每个分叉角度为 50 度。

人造雪花由`科赫曲线`给出，它简单地将一条直线切分为三等份，并用一个三角形的两条边替换中间的那一段。这一过程对生成的新线段不断重复。见图 6-14。

对于`ThMAD`，我们有两种应用`自相似性`概念的选择。首先，我们可以获取帧缓冲区的内容，即屏幕上显示的内容，并将其反馈到渲染管线的更早阶段。其次，我们可以让某个渲染器使用分形算法来执行其工作。作为正确的后续步骤——在我们之前通过反馈来实现模糊效果的基础上——我们现在将专注于通过反馈来生成`自相似性`。另一种方式，即算法上的`自相似性`，将不在本书中讨论。

![A457467_1_En_6_Fig14_HTML.jpg](img/A457467_1_En_6_Fig14_HTML.jpg)

图 6-14. `科赫曲线`，一种人造雪花

我们从基础的`自相似性`管线开始，并在此基础上逐步使其更精美。保存之前所有的工作后，通过右键单击选择`新建 → 空白项目`来清理画布。然后立即用新名称保存，以确保它不会弄乱你已有的保存文件。现在将以下模块放置在画布上：

- `Bitmaps → generators → blob`
- `Texture → loaders → bitmap2texture`
- `Renderers → basic → textured_rectangle`
- `Renderers → opengl_modifiers → blend_mode`

将它们全部连接起来，并将`blend_mode`连接到屏幕。见图 6-15。在模块内部，按照表 6-13 至 6-16 所述设置参数。

**表 6-16. `output → screens → screen0`**
| 参数 | 值 | 说明 |
| :--- | :--- | :--- |
| `clear_color` | `0, 0, 0, 0` | 避免任何清除 |

**表 6-15. `renderers → opengl_modifiers → blend_mode`**
| 参数 | 值 | 说明 |
| :--- | :--- | :--- |
| `source_blend` | `SRC_ALPHA` | 传入的像素根据其 Alpha 值显示 |
| `dest_blend` | `ONE_MINUS_ SRC_ALPHA` | 已有的像素按 (1-传入像素的 Alpha 值) 的比例减弱 |

**表 6-14. `renderers → basic → textured_rectangle`**
| 参数 | 值 | 说明 |
| :--- | :--- | :--- |
| `spatial / position` | `-0.4; 0.27; 0.0` | 如果你还不习惯使用滑块控制器，一开始设置负数可能会有点棘手。解决办法是先降低第二排旋钮中的旋钮值，然后更改滑块。第一个旋钮是范围，第二个旋钮是偏移量。 |
| `spatial / size` | `0.6` | |

**表 6-13. `bitmap → generators → blob`**
| 参数 | 值 |
| :--- | :--- |
| `settings / arms` | `5` |
| `settings / attenuation` | `2.0` |
| `settings / alpha` | `yes` |
| `size` | `512x512` |

![A457467_1_En_6_Fig15_HTML.jpg](img/A457467_1_En_6_Fig15_HTML.jpg)

图 6-15. `自相似性`主生成管线、混合器与输出

你现在应该在输出屏幕的左上部分看到一颗星；见图 6-16。现在对于反馈子管线，将模块放置在画布上并绘制它们之间的连接；见图 6-17。

![A457467_1_En_6_Fig17_HTML.jpg](img/A457467_1_En_6_Fig17_HTML.jpg)

图 6-17. `自相似性`反馈管线

![A457467_1_En_6_Fig16_HTML.jpg](img/A457467_1_En_6_Fig16_HTML.jpg)

图 6-16. `自相似性`主生成管线，输出

- `Mesh → solid → mesh_grid`
- `Renderers → mesh → mesh_basic_render`
- `Texture → buffers → render_surface_single`

按照表 6-17 和 6-18 所示设置参数。

**表 6-18. `texture → buffers → render_surface_single`**
| 参数 | 值 | 说明 |
| :--- | :--- | :--- |
| `options / texture_size` | `512x512` | |
| `options / support_feedback` | `yes` | 如果没有设置为 `"yes"`，反馈将无法工作。 |
| `options / alpha_channel` | `yes` | |
| `options / clear_color` | `0, 0, 0, 1` | 纯黑色。使反馈的纹理可见，以便清晰观察。稍后我们可以将 `ALPHA` 改为 0 以模糊输出效果，见下文。 |

**表 6-17. `mesh → solid → mesh_grid`**
| 参数 | 值 | 说明 |
| :--- | :--- | :--- |
| `plane` | `xy` | 对于 2D 状态我们需要设置为 `"xy"`。 |

为了组合这两个子管线，从`blend_mode`的输出端画一条连接到`render_surface_single`的`render_in`锚点，再从`mesh_basic_render`的输出端画另一条连接到`blend_mode`的`render_in`锚点。双击后一个锚点，确保生成管线位于反馈管线的上方。如果不是这样，请垂直拖拽其中任何一个到正确位置。见图 6-18。

![A457467_1_En_6_Fig18_HTML.jpg](img/A457467_1_En_6_Fig18_HTML.jpg)

图 6-18. 组合主渲染管线与反馈管线

现在您应该会看到如图 6-19 所示的输出。这一结果明显展示了自相似性：使用½、½ ∙ ½、½ ∙ ½ ∙ ½等缩放因子放大图片，总能得到与原图一致的效果。接下来我将详细解释其原理。主渲染管线会绘制一颗星并将其置于屏幕左上角。其位置和大小由`textured_rectangle`模块的设置决定。`blend_mode`模块将其与反馈子管线的输出（目前为空）混合，并将整体结果同时发送至屏幕和`render_- surface_single`模块。后者将屏幕内容存储到图形硬件上的纹理存储器中。在下一帧中，`mesh_basic_render`模块将此存储纹理投影到由`mesh_grid`模块定义的平面网格上，该网格此时为`blend_mode`提供第二个输入。由于`mesh_grid`模块定义的网格坐标范围为(-0.5; -0.5)到(+0.5; +0.5)，而屏幕输出范围为(-1; -1)到(+1; +1)，因此反馈图像的尺寸仅为输入图像的一半。此时`blend_mode`拥有两个非空输入，输出原始图像与反馈图像的叠加结果。下一帧中，此过程将产生一个尺寸为 1/4 的克隆，再下一帧产生 1/16 尺寸的克隆，以此类推。

![A457467_1_En_6_Fig19_HTML.jpg](img/A457467_1_En_6_Fig19_HTML.jpg)

图 6-19. 自相似性基础状态输出

若想去除星形周围的黑色矩形（此处保留是为了更清晰地展示），只需将`render_surface_single`模块中`clear_color`锚点的`ALPHA`值设为 0.0；参见表 6-19。

**表 6-19. `texture → buffers → render_surface_single`**
| 参数 | 值 |
| :--- | :--- |
| `options / clear_color` | `0, 0, 0, 0` |

请记住，在`ThMAD`中`ALPHA`值始终是第四个颜色值。将其归零能有效阻止模块在每次绘制内部纹理缓冲区内容前清除其区域。

要改变此缩放因子并允许反馈图像平移，我们在`mesh_grid`模块与`mesh_basic_render`模块之间额外添加两个模块：`Mesh → modifiers → transformers → mesh_scale`与`Mesh → modifiers → transformers → mesh_translate`。后续可用它们调整自相似舞台的整体特性。

到目前为止的状态如图 6-20 所示。请将其保存为自己的状态，因为您可以把它作为多种不同实验的基础。

![A457467_1_En_6_Fig20_HTML.jpg](img/A457467_1_En_6_Fig20_HTML.jpg)

图 6-20. 自相似性基础状态注释

该反馈基础状态也可在`TheArtOfAudioVisualization`文件夹下的`A-6.1.2_Backfeeding_Basic`中找到。

为使效果更有趣，我们现在将使用类似于`科赫曲线`（见图 6-14）的算法构建一个状态。该方法与刚刚介绍的反馈原理相同，但存在一些差异，若从头开始构建则能更清晰地展现逻辑。最终我们将拥有三个子管线，先从第一个开始。

注意：以下状态也可在`TheArtOfAudioVisualization`文件夹下的`A-6.1.2_Backfeeding`中找到。

对于第一个子管线，请将以下模块放置在空白画布上：
* `Mesh → generators → ribbon`
* `Renderers → mesh → mesh_basic_render`
* `Renderers → opengl_modifiers → gl_color`
* `Renderers → opengl_modifiers → blend_mode`
* `Renderers → opengl_modifiers → gl_translate`

按此顺序连接所有模块，并将`gl_translate`连接至屏幕；参见图 6-21。请注意，`mesh_basic_render`有两个网格输入锚点——请使用名称为`mesh_in`的锚点。

![A457467_1_En_6_Fig21_HTML.jpg](img/A457467_1_En_6_Fig21_HTML.jpg)

图 6-21.

# 精美的科赫曲线，管线 I

我们以`ribbon`（色带）模块作为绘制基础，因为它引入了一些形状修饰，并且会随时间移动。参数见表 6-20 至 6-24。

表 6-24.

| `renderers` → `opengl_modifiers` → `gl_translate` |
| --- |
|   | `translation` | 0, 0.3, 0.0 | 将混合后的输出移至屏幕中央。 |

表 6-23.

| `renderers` → `opengl_modifiers` → `blend_mode` |
| --- |
|   | `source_blend` | `SRC_ALPHA` |
|   | `dest_blend` | `ONE` |

表 6-22.

| `renderers` → `opengl_modifiers` → `gl_color` |
| --- |
|   | `color` | any | 任意你喜欢的颜色。 |

表 6-21.

| `renderers` → `mesh` → `mesh_basic_render` |
| --- |
|   | `options` / `vertex_colors` | no | 我们提供了自己的颜色，因此此处设为“no” |
|   | `options` / `use_display_list` | no |   |
|   | `options` / `use_vertex_colors` | no | 我们提供了自己的颜色，因此此处设为“no” |
|   | `options` / `particles_size_center` | no |   |
|   | `options` / `particles_size_from_color` | no |   |
|   | `options` / `ignore_uvs_in_vbo_updates` | no |   |

表 6-20.

| `mesh` → `generators` → `ribbon` |
| --- |
|   | `spatial` / `start_point` | -0.7, -0.5, 0 |
|   | `spatial` / `end_point` | 0.7, -0.5, 0 |
|   | `spatial` / `up_vector` | 0.8, 0.8, 0.9 |
|   | `shape` / `width` | 0.1 |
|   | `shape` / `skew_amp` | 4.0 |
|   | `shape` / `time_amp` | 1.5 |
|   | `shape` / `segm_count` | 60 |

这应能生成基本的飘动色带；见图 6-22。

![A457467_1_En_6_Fig22_HTML.jpg](img/A457467_1_En_6_Fig22_HTML.jpg)

图 6-22. 精美的科赫曲线，管线 I 输出

`gl_color` 模块定义了色带的颜色。`ribbon` 模块本身不处理颜色，而且我们也告知 `mesh_basic_render` 不要处理颜色。

子管线二由以下模块组成：

* `Texture` → `buffers` → `render_surface_single`
* `Texture` → `modifiers` → `scale_one`
* `Texture` → `modifiers` → `rotate`
* `Renderers` → `basic` → `textured_rectangle`

`render_surface_single` 的状态与反馈循环前的基础状态相同。连接方式见图 6-23。

![A457467_1_En_6_Fig23_HTML.jpg](img/A457467_1_En_6_Fig23_HTML.jpg)

图 6-23. 精美的科赫曲线，管线 II

这些模块的参数见表 6-25 至 6-28。

表 6-28.

| `renderers` → `basic` → `textured_rectangle` |
| --- |
|   | 将所有锚点保留为默认值 |

表 6-27.

| `texture` → `modifiers` → `rotate` |
| --- |
|   | `rotation_angle` | 0.15 | 大约完整旋转的 1/6，即 60° |
|   | `rotation_axis` | 0, 0, 1 | 在 x-y 平面上旋转 |
|   | `center` / `use_rotate_center` | yes | 使用下方的旋转中心 |
|   | `center` / `rotate_center` | 0.66, 0.25, 0 | 用于形成图示![A457467_1_En_6_Figa_HTML.jpg](img/A457467_1_En_6_Figa_HTML.jpg) |

表 6-26.

| `texture` → `modifiers` → `scale_one` |
| --- |
|   | `scale` | 3.0 | 缩小至 1/3，与原始科赫曲线相同。 |
|   | `center` / `use_scale_center` | yes | 使用下方的缩放中心 |
|   | `center` / `scale_center` | 0.5, 0.25, 0 | 在色带中心进行缩放 |

表 6-25.

| `texture` → `buffers` → `render_surface_single` |
| --- |
|   | `options` / `texture_size` | 1024x1024 |   |
|   | `options` / `support_feedback` | yes | 反馈循环正常工作所必需 |
|   | `options` / `alpha_channel` | yes |   |
|   | `options` / `clear_color` | 0, 0, 0, 0 |   |

至于子管线三，克隆管线二中的所有模块——对每个模块，在按住 `Ctrl+Alt` 的同时点击并拖动。按照子管线二的连接方式，连接子管线三的模块。见图 6-24。

![A457467_1_En_6_Fig24_HTML.jpg](img/A457467_1_En_6_Fig24_HTML.jpg)

图 6-24. 精美的科赫曲线，管线 II + III

仅更改管线三的旋转模块，使其内容如表 6-29 所示。

表 6-29.

| `texture` → `modifiers` → `rotate` |
| --- |
|   | `rotation_angle` | 0.15 | 大约完整旋转的 1/6，即 60°。注意与管线 II 相比符号的差异 |
|   | `rotation_axis` | 0, 0, 1 | 在 x-y 平面上旋转 |
|   | `center` / `use_rotate_center` | yes | 使用下方的旋转中心 |
|   | `center` / `rotate_center` | 0.33, 0.25, 0 | 用于形成图示![A457467_1_En_6_Figb_HTML.jpg](img/A457467_1_En_6_Figb_HTML.jpg) |

将 `blend_mode` 的输出连接到管线 II 和管线 III 的 `render_surface_single` 模块。连接管线 II 和管线 III 中 `textured_rectangle` 模块的输出锚点。更改 `blend_mode` 的输入连接顺序，使管线 I 优先；见图 6-25。

![A457467_1_En_6_Fig25_HTML.jpg](img/A457467_1_En_6_Fig25_HTML.jpg)

图 6-25. 精美的科赫曲线，`blend_mode` 输入顺序

此时输出应如图 6-26 所示。这是一个使用原始映射算法的正确科赫曲线，只是将线条换成了色带。目前看起来还不够引人入胜，但我们可以轻松地让它变得更有吸引力。只需更改表 6-30 至 6-33 中显示的锚点值。

表 6-32.

| `texture` → `modifiers` → `scale_one` |
| --- |
| 管线 III |
|   | `scale` | 1.2 | 放大。 |

表 6-31.

| `texture` → `modifiers` → `rotate` |
| --- |
| 管线 II |
|   | 无需更改，但你可以随意调整。 |

表 6-30.

| `texture` → `modifiers` → `scale_one` |
| --- |
| 管线 II |
|   | `scale` | 2.2 | 放大 |

![A457467_1_En_6_Fig26_HTML.jpg](img/A457467_1_En_6_Fig26_HTML.jpg)

图 6-26. 精美的科赫曲线，输出

`ribbon` 模块的 `skew_amp` 临时减小到 0.0，以使显示更清晰。

表 6-33.

| `texture` → `modifiers` → `rotate` |
| --- |
| 管线 III |
|   | `rotation_angle` | -0.12 | 稍微改变角度 |
|   | `center` / `rotate_center` | 0.37, 0.25, 0 | 稍微改变 |

修改后的输出现在看起来精美多了；见图 6-27。从鸟瞰视角看，完整的状态如图 6-28 所示。

![A457467_1_En_6_Fig28_HTML.jpg](img/A457467_1_En_6_Fig28_HTML.jpg)

图 6-28. 精美的科赫曲线，完整状态

![A457467_1_En_6_Fig27_HTML.jpg](img/A457467_1_En_6_Fig27_HTML.jpg)

图 6-27. 修改后的精美科赫曲线，输出

这只是一个快照；所有色带都在飘动。

请随意调整锚点值，并尝试将某个锚点与声音输入连接。

### 注意

由于反馈循环的存在，输出屏幕很容易完全被白色填充。通常当反馈的图像数据缩小时不会发生这种情况，但如果你在调整值时遇到了，可能需要撤销更改、保存状态并重启 ThMAD，才能再次获得有意义的输出。或者，临时将 `blend_mode` 模块内的混合模式改为 `ZERO`/`ZERO`，然后再改回来。

## 粒子系统

粒子系统主要涉及两方面的内容：

- 对象的多样性
- 物理运动规律与碰撞规则

由于 ThMAD 不支持类似循环结构的功能，粒子系统的多样性属性是生成大量对象的唯一途径。此外，粒子系统可以遵循重力、风力或刚体墙壁反弹等物理规则。ThMAD 通过图 6-29 所示的方法，实现了具备这些特性的粒子系统。

一个 `发射器` 持续创建粒子——我们有以下发射器类型：

![A457467_1_En_6_Fig29_HTML.gif](img/A457467_1_En_6_Fig29_HTML.gif)

图 6-29. 粒子系统方法

- 喷雾发射器。在空间中的某个单点持续创建粒子。
- 位图发射器。将位图的每个像素（无论定义为前景色还是背景色）转换为一个与位图像素颜色相同的单一粒子。
- 网格发射器。类似于喷雾发射器，但使用网格的每个顶点作为潜在的源点。

一个 `修改器` 对现有粒子应用规则。这包括以下操作：

- 应用重力。
- 应用风力。
- 放置一堵墙，粒子在墙处停止运动或反弹。
- 应用粒子间的类流体相互作用。
- 应用粒子尺寸调整。

一个 `渲染器` 将粒子系统转换为可渲染的对象。只有在经过渲染器之后，粒子才成为图形对象——在此之前，它们只是数值的集合。ThMAD 通过以下方式之一进行渲染：

- 简单渲染。使用纹理将粒子转换为图形对象。通过在粒子的生命周期内定义尺寸如何演变、颜色如何演变，增强了修改步骤中可用的可能性。尽管名称中带有“简单”，但它实际上非常强大，你可以使用着色器程序来控制渲染操作。
- 中心渲染。这是一种特效变体，其中粒子团的一侧被固定到一个中心点。
- 带状渲染。另一种特效变体，粒子被转换为在 3D 空间中移动的带状物。
- 扩展渲染器。一种使用纹理、尺寸和颜色生命周期定义的渲染器，如果你想要自定义着色器语言控制的话。
- 火花渲染器。允许粒子与相互靠近的粒子发生交互，产生火花。

在本节的其余部分，我们将创建几个粒子系统示例。

# 瀑布

瀑布是粒子系统的绝佳应用场景。大量对象下落，遵循重力和风力等物理规律，最终落在瀑布的地面上。

> **注意：** 本小节的示例资源位于 `TheArtOfAudioVisualization` 文件夹下的 `A-6.2.1_Particlesystems_Waterfall*` 中。

为了模拟瀑布，我们在空白画布上放置以下模块。要创建画布，请右键点击并选择 “新建 → 空项目”：

- `渲染器` → `opengl_modifiers` → `摄像头` → `环绕摄像头`
- `渲染器` → `opengl_modifiers` → `方向光`
- `渲染器` → `opengl_modifiers` → `材质参数`
- `渲染器` → `opengl_modifiers` → `深度缓冲`
- `渲染器` → `opengl_modifiers` → `背面剔除`
- `渲染器` → `opengl_modifiers` → `混合模式`

将它们全部连接起来，并将摄像头连接到屏幕模块；见图 6-30。为这些模块调整以下参数。参见表 6-34 至 6-37。

表 6-37.

| `渲染器 → opengl_modifiers → 背面剔除` |
| --- |
| `状态` | `启用` |

表 6-36.

| `渲染器 → opengl_modifiers → 深度缓冲` |
| --- |
| `深度测试` | `启用` |
| `深度遮罩` | `启用` |

表 6-35.

| `渲染器 → opengl_modifiers → 材质参数` |
| --- |
| `环境反射率` | `0.2; 0.2; 0.2; 1.0` |
| `漫反射率` | `0; 0; 1; 1` |
| `镜面反射率` | `0.96; 0.82; 0.82; 1.0` |
| `自发光强度` | `0; 0; 0; 1` |
| `镜面反射指数` | `5.0` |

表 6-34.

| `渲染器 → opengl_modifiers → 方向光` |
| --- |
| `启用` | `是` |
| `位置` | `0.29; 0.29; 0.12` |
| `环境光颜色` | `0; 0; 0; 1` |
| `漫反射颜色` | `0.67; 0.69; 0.95; 1.0` |
| `镜面反射颜色` | `0.16; 0.79; 0.92; 1.0` |

![A457467_1_En_6_Fig30_HTML.jpg](img/A457467_1_En_6_Fig30_HTML.jpg)

图 6-30. 瀑布，基础 3D 设置

`深度测试` 和 `背面剔除` 这两个模块在这里不太重要，因为我们只处理微小粒子。为了后续的特效或试验，我们还是会添加它们。

现在对于粒子系统子流程，添加以下模块：

- `纹理` → `粒子` → `斑点`
- `渲染器` → `粒子系统` → `简单`
- `粒子系统` → `修改器` → `地板`
- `粒子系统` → `修改器` → `基本风力变形器`
- `粒子系统` → `修改器` → `尺寸缩放`
- `粒子系统` → `修改器` → `基本重力`
- `粒子系统` → `生成器` → `基本喷雾发射器`

按照图 6-31 所示进行连接。

![A457467_1_En_6_Fig31_HTML.jpg](img/A457467_1_En_6_Fig31_HTML.jpg)

图 6-31. 瀑布，粒子系统子流程

对于这些参数，请按表 6-38 至 6-42 所示进行调整。

表 6-42.

| `particlesystems` → `generators` → `basic_spray_emitter` |
| --- |
| | `num_particles` | 10000 | 要使用的粒子数量。即使修改器或渲染器指示某个粒子必须消亡，该数值也保持不变。在这种情况下，粒子只会被重新初始化。 |
| | `particles_per_second` | 2000 | 后续将由声音控制。限制每秒初始化或重新初始化的粒子数量。如果该数值足够小，部分已消亡的粒子会在一段时间内保持未重新初始化状态。设置为 `-1` 可禁用此限制。 |
| | `spatial` / `emitter_position` | 0; 1; 0 | 粒子生成或重新初始化的位置。 |
| | `spatial` / `speed` / `speed_x` | 1.0 | 后续由声音输入控制。粒子生成或重新初始化时速度的 X 轴散布范围。 |
| | `spatial` / `speed` / `speed_y` | 0.45 | |
| | `spatial` / `speed` / `speed_z` | 0.07 | |
| | `spatial` / `speed_type` | `random_balanced` | 速度的分布方式。此处的值为 X 轴从 `[-speed_x; +speed_x]` 中选取一个随机数，其他轴同理。 |
| | `spatial` / `size` / `particle_size_base` | 0.03 | 粒子的基础大小 |
| | `spatial` / `size` / `particle_size_random_weight` | 0.01 | 大小随机化程度 |
| | `appearance` / `color` | 1;1;1;1 | 未使用 |
| | `appearance` / `time` / `particle_lifetime_base` | 3.48 | 粒子的基础生命周期（秒）。超过此时间，粒子将保持未定义状态，直到被重新初始化。 |
| | `appearance` / `time` / `particle_lifetime_random_weight` | 1.0 | 生命周期随机化程度 |

表 6-41

| `particlesystems` → `modifiers` → `basic_gravity` |
| --- |
| | `basic_parameters` / `center` | 0; -1.5; 0 | 重力中心 |
| | `basic_parameters` / `amount` | 0; 0.079; 0 | 仅沿 Y 轴的重力 |
| | `basic_parameters` / `friction` | 0.6; 0.6; 1.8 | 若该值非空，粒子将不会无限加速，而是受摩擦力影响。 |

表 6-40

| `particlesystems` → `modifiers` → `size_mult` |
| --- |
| | `strength` | 0.71 | 使粒子稍微变小 |

表 6-39

| `particlesystems` → `modifiers` → `basic_wind_deformer` |
| --- |
| | `wind` | 0.22; 0.0; 0.0 | 朝+X 方向吹的风 |

表 6-38

| `particlesystems` → `modifiers` → `floor` |
| --- |
| | `axis` / `y` / `y_floor` | yes | 启用 XZ 平面作为地面 |
| | `axis` / `y` / `y_bounce` | yes | 让粒子在地面上弹跳 |
| | `axis` / `y` / `y_loss` | 85.0 | 弹跳前损失的粒子百分比。若此数值太小，粒子行为将更像橡胶球而非水。 |
| | `axis` / `refraction` | yes | 水的行为不像刚性球体，撞击地面后会向各个方向扩散 |
| | `axis` / `refraction_amount` | 100; 10; 0 | 折射程度——水主要沿地面平行方向扩散 |
| | `floor` | 0; -0.97; 0 | 地面的位置 |

将此子流水线连接到基础 3D 渲染流水线：使用`simple`模块的`render_out`锚点和`blend_mode`模块的`render_in`锚点。这将产生一个瀑布效果，如图 6-32 所示。

![A457467_1_En_6_Fig32_HTML.jpg](img/A457467_1_En_6_Fig32_HTML.jpg)

图 6-32. 瀑布，无声音控制

我们希望添加一些声音控制功能。为此，请添加以下模块：

* `Maths` → `limiters` → `float_clamp`
* `Maths` → `arithmetics` → `ternary` → `float` → `mult_add`，共两个
* `Sound` → `input_visualization_listener`

按照图 6-33 所示连接它们。并按表 6-43 至 6-45 所示应用参数。

表 6-44

| `maths mult_add` | → | `arithmetics` | → | `ternary` | → | `float` | → |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 连接到`float_clamp`的那个。 | | | | | | | |
| | `first_mult` | 1000 |
| | `then_add` | 1000 |

表 6-43

| `maths` → `limiters` → `float_clamp` |
| --- |
| 确保每秒生成/重新初始化的粒子数在 1000 到 5000 之间 |
| | `low` | 1000 |
| | `high` | 5000 |

![A457467_1_En_6_Fig33_HTML.jpg](img/A457467_1_En_6_Fig33_HTML.jpg)

图 6-33. 瀑布，带声音控制。下方的`mult_add`模块连接到`basic_spray_emitter`的`speed_x`锚点。两个`mult_add`模块都连接到`sound_visualization_listener`的任意`vu`子锚点。

表 6-45

| `maths` → `arithmetics` → `ternary` → `float` → `mult_add` |
| --- |
| 另一个模块，连接到`basic_spray_emitter`的`x_speed`锚点。 |
| | `first_mult` | 0.4 |
| | `then_add` | 0.13 |

添加声音输入后，结果将是根据音量大小拥有更多质量和更宽幅度的瀑布。见图 6-34。

![A457467_1_En_6_Fig34_HTML.jpg](img/A457467_1_En_6_Fig34_HTML.jpg)

图 6-34. 瀑布，受声音控制，输出效果

到目前为止，瀑布看起来可能更像是由颗粒而非水流构成。为了增强其与自然水体的相似度，我们在刚才生成的基础上添加一个模糊效果作为变体。

模糊是关于混合渲染画面的较新和较旧实例。我们在本章前面部分已经学习了如何在二维空间中实现这一点。为了采用比之前更简单的方法，这里选择了`Texture` → `effects` → `blur`模块。我们仅在相机完成工作后应用模糊，这感觉就像是在 3D 中进行模糊操作。这是因为在 3D 中无法进行真正的模糊，因为我们的纹理只能处理二维。但在相机之后，我们在内部拥有了一个二维投影，因此可以利用二维模糊。我们需要三个模块来实现模糊效果：

* `Renderers` → `basic` → `textured_rectangle`
* `Texture` → `effects` → `blur`
* `Texture` → `buffers` → `render_surface_single`

将它们插入相机和屏幕之间，如图 6-35 所示。按表 6-46 至 6-48 所示应用参数。

表 6-48

| `texture` → `buffers` → `render_surface_single` |
| --- |
| | `texture_size` | 1024x1024 | 可以使用更小的尺寸来增强模糊效果 |

表 6-47

| `texture` → `effects` → `blur` |
| --- |
| | `start_value` | 2.2 | 在混合原始图像和旧图像时，应用于纹理坐标的偏移量 |
| | `attenuation` | 1.22 | 混合强度 |
| | `texture_size` | 1024x1024 | 可以使用更小的尺寸来增强模糊效果 |
| | `passes` | `TWO` | 与`ONE`相比，增强模糊效果 |

表 6-46

| `renderers` → `basic` → `textured_rectangle` |
| --- |
| | 所有参数保持默认值 |

![A457467_1_En_6_Fig35_HTML.jpg](img/A457467_1_En_6_Fig35_HTML.jpg)

图 6-35. 瀑布，添加模糊效果

添加模糊后的瀑布将如图 6-36 所示。

![A457467_1_En_6_Fig36_HTML.jpg](img/A457467_1_En_6_Fig36_HTML.jpg)

图 6-36. 瀑布，模糊后输出效果

作为附带效果，这个模糊的瀑布在不增加粒子数量的情况下，看起来水量更充沛一些。

### 位图粒子

`ThMAD` 包含一个模块 `Particlesystem → generators → bitmap2particlesystem`，你可以用它来让粒子系统根据位图的彩色像素生成。这里我们将提供一个示例。

**注**：本小节的示例作为源文件存放在 `TheArtOfAudioVisualization` 文件夹下的 `A-6.2.2_*` 路径中。

像往常一样，从一个空状态开始，你可以通过右键单击并选择 `New → Empty Project` 来创建一个。首先放置本章开头描述的基础 3D 模块，如图 6-30 所示，但请使用表 6-49 到 6-54 中所示的参数。

**表 6-54.**
| `renderers → opengl_modifiers → blend_mode` | |
| :--- | :--- |
| `source_blend` | `ONE` |
| `dest_blend` | `ONE_MINUS_SRC_ALPHA` |

**表 6-53.**
| `renderers → opengl_modifiers → backface_culling` | |
| :--- | :--- |
| `status` | `ENABLED` |

**表 6-52.**
| `renderers → opengl_modifiers → depth_buffer` | |
| :--- | :--- |
| `depth_test` | `ENABLED` |
| `depth_mask` | `ENABLED` |

**表 6-51.**
| `renderers → opengl_modifiers → material_param` | |
| :--- | :--- |
| 此处的值无关紧要，着色由着色器代码完成，见下文 | |

**表 6-50.**
| `renderers → opengl_modifiers → light_directional` | |
| :--- | :--- |
| 此处的值无关紧要，着色由着色器代码完成，见下文 | |

**表 6-49.**
| `renderers → opengl_modifiers → cameras → orbit_camera` | |
| :--- | :--- |
| `rotation` | `-0.03; 0.99; -0.11` |
| `distance` | `0.72` |
| `perspective_correct` | `yes` |

现在，为粒子系统子流水线添加以下模块：
*   `Renderers → particlesystems → render_particle_shader`
*   `Texture → particles → blob`
*   `Particlesystems → modifiers → basic_wind_deformer`
*   `Particlesystems → generators → bitmap_to_particlesystem`
*   `Bitmaps → loaders → png_bitm_load`

按照图 6-37 所示进行连接。这将从位图像素创建粒子，然后对其施加风效果。将参数设置为表 6-55 到 6-59 中的值。

**表 6-59.**
| `bitmaps → loaders → png_bitm_load` | | |
| :--- | :--- | :--- |
| `filename` | 任意 | 选择一个尺寸不超过 256x256 的 PNG 文件，以免给硬件带来过大负担。 |

**表 6-58.**
| `particlesystems → generators → bitmap_to_particlesystem` | | |
| :--- | :--- | :--- |
| `particles_per_second` | `10000` | 每秒可由位图产生的粒子数量 |
| `spatial / bitmap_size` | `1.0` | |
| `spatial / bitmap_normal` | `0; 1; 0` | 定义空间中的方向。此处我们定义位图位于 x-z 平面内。 |
| `spatial / bitmap_upvector` | `1; 0; 0` | 定义与法向量（作为轴）的角度。 |
| `spatial / bitmap_position` | `0; 0; -0.25` | 位图在空间中的位置。 |
| `spatial / speed / speed_x` | `0` | 我们让粒子在创建后立即静止。因此只有风模块会移动它们。 |
| `spatial / speed / speed_y` | `0` | |
| `spatial / speed / speed_z` | `0` | |
| `spatial / size / particle_size_base` | `0.1` | 粒子的大小。 |
| `spatial / size / particle_size_random_weight` | `0.01` | 粒子大小的随机贡献量。 |
| `appearance / time / particle_lifetime_base` | `1.16` | 粒子在消亡和/或需要重新初始化之前存活的秒数。 |
| `appearance / time / particle_lifetime_random_weight` | `1.0` | 粒子生命周期的随机贡献量。 |

**表 6-57.**
| `particlesystems → modifiers → basic_wind_deformer` | | |
| :--- | :--- | :--- |
| `wind` | `0; 0; 0` | 稍后我们将把它连接到音频输入，见下文 |

**表 6-56.**
| `texture → particles → blob` | | |
| :--- | :--- | :--- |
| `settings / alpha` | `yes` | |
| `size` | `8x8` | 由于粒子很小，我们可以使用小纹理 |

**表 6-55.**
| `renderers → particlesystems → render_particle_shader` | | |
| :--- | :--- | :--- |
| 通过编写自定义着色器程序，你可以实现极其有趣的效果。就我们当前目的而言，只需使用默认提供的纹理程序。 | | |
| `shader_params / vertex_program` | 保持默认 | 着色器管线的顶点着色器部分。默认程序将处理空间定位和获取粒子颜色。 |
| `shader_params / fragment_program` | 保持默认 | 着色器管线的片段着色器部分。默认程序将使用粒子颜色与纹理 `ALPHA` 混合，并丢弃纹理颜色。 |

![A457467_1_En_6_Fig37_HTML.jpg](img/A457467_1_En_6_Fig37_HTML.jpg)
**图 6-37.** 位图粒子系统

PNG 文件必须存放在 `/home/[USER]/thmad/[VERSION]/data/resources` 目录下。

现在你已经可以看到粒子系统了——由于粒子尚未移动，你的图像似乎在模糊地飘动。这是由粒子的产生和消亡机制以及期间自动的大小变化造成的。见图 6-39。

为了引入声音控制，在画布上放置以下附加模块：
*   `Maths → converters → 3float_to_float3`
*   `Maths → interpolation → float_smoother`
*   `Audio → input_visualization_listener`

按照图 6-38 所示进行连接。应用以下参数，如表 6-60 到 6-62 所示。

![A457467_1_En_6_Fig38_HTML.jpg](img/A457467_1_En_6_Fig38_HTML.jpg)
**图 6-38.** 位图粒子系统，声音控制。`float_smoother` 连接到监听器的 `vu / vu_l` 锚点。

**表 6-60.**
| `maths → converters → 3float_to_float3` | | |
| :--- | :--- | :--- |
| `floata` | `0` | 风的 x 分量 |
| `floatb` | `0` | 风的 y 分量 |
| `floatc` | `-` | 连接到声音监听器 |

**表 6-61.**
| `maths → interpolation → float_smoother` | | |
| :--- | :--- | :--- |
| `value_in` | `-` | 连接到声音监听器 |
| `speed` | `5.0` | 值越低，平滑程度越高 |

**表 6-62.**
| `sound → input_visualization_listener` | | |
| :--- | :--- | :--- |
| `multiplier` | `0.38` | 音量倍增器，根据你的需要更改。 |
| `vu / vu_l` | `-` | 作为输出连接到上述平滑器 |

播放一些音乐，你会看到位图粒子被吹散，如图 6-39 所示。

![A457467_1_En_6_Fig39_HTML.jpg](img/A457467_1_En_6_Fig39_HTML.jpg)
**图 6-39.** 位图粒子系统，无音频输入时的输出

### 中心钳制粒子系统

使用 `renderers → particlesystems → render_particle_center` 模块，我们能得到一个虽然不太现实但效果令人印象深刻的粒子系统变体。

其基本原理是将纹理定义的两个点钳制到一个中心点上。这里我们会提供一个示例，但与其一步步解释，不如请您参考安装时附带的源文件。本节仅从宏观角度进行说明，并指出一些重要的设置。

**注意**
本小节的示例作为源文件，位于 `TheArtOfAudioVisualization` 文件夹下的 `A-6.2.3_Particlesystems_Center*` 目录中。

将 `A-5.5.3_Particlesystems_Center_Blur` 状态加载到 Artiste 中，您将会看到如图 6-40 所示的画面。

![A457467_1_En_6_Fig40_HTML.jpg](img/A457467_1_En_6_Fig40_HTML.jpg)
图 6-40. 中心钳制粒子系统，完整状态

标记为 (1) 的部分是常规的 3D 渲染管线。不过，`backface_culling`（背面剔除）和 `depth_buffer`（深度缓冲）都被禁用了；否则，纹理背景会与我们不希望看到的其他纹理重叠。灯光是开启的，且为全白，材质参数也均为全白，此外，`ALPHA`（透明通道）连接到了音频输入，这样图像会随着音量的增大而变得更加鲜明。

标记 (2) 的部分指定了我们将会看到的一组对象。其源是同心圆，在此例中，我们添加了一个由音频输入控制的绕 Y 轴旋转。

标记 (3) 的部分是另一组不同颜色的同心钳制圆，这次我们通过音频输入来控制它们的速度。

标记 (4) 的部分负责实现模糊效果。如果不使用模糊，粒子被钳制到原点且生命周期有限这一特性会带来一些令人不悦的躁动感。该效果通过 `highblur` 模块实现，该模块使用了反馈技术，本章的另一处也会对此进行介绍。您可以调整这个子管线的参数，其中一个非常重要的参数就是所选的混合模式。此处设定的值是 `ONE`/`ONE`，这意味着原始图像和反馈图像会相互叠加绘制。为了达到预期效果，锚点 `color/global_alpha`（此处为 0.89）至关重要，这个值的微小变化都会导致输出结果发生巨大的改变。

最终结果如图 6-41 所示，其中紫罗兰色的射线组根据音频的低频部分旋转，而黄色的射线组则会随着音量的增大而增强其贡献。

![A457467_1_En_6_Fig41_HTML.jpg](img/A457467_1_En_6_Fig41_HTML.jpg)
图 6-41. 中心钳制粒子系统，输出效果

### 带状粒子

`Renderers → particlesystems → render_particle_ribbon` 模块会沿着一条在空间中移动的、受类似重力运动法则驱动的带状路径发射粒子。

它可用于创造有趣的视觉效果，我们在此提供一个示例。与其提供逐步的操作指南，不如直接查看安装 ThMAD 后的最终状态，并解释其中的重要构建模块。

**注意**
本小节的示例状态作为源文件，位于 `TheArtOfAudioVisualization` 文件夹下的 `A-6.2.4_Particlesystems_Ribbon` 目录中。

该状态如图 6-42 所示，图中高亮显示了主要部分。
*   部分 (1) 是典型的 3D 子管线；不过，`depth_buffer`（深度缓冲）模块被禁用了。您可以尝试同时禁用 `backface_culling`（背面剔除）看看会发生什么。灯光和材质都具有全部三个光照分量：环境光、漫反射光和高光。灯光会由部分 (4) 控制绕场景旋转，其环境光强度则由音频输入控制。
*   部分 (2) 定义了粒子系统及其渲染器。渲染器本身不具备为渲染过程分配纹理的能力，但通过添加 `Texture → opengl → texture_bind` 模块，我们仍然可以实现这一点。这里我们分配了一个 `blob`（斑点）模块，这会使带状效果看起来像一个管子。此处，一些音频输入已被连接到带状宽度上。
*   部分 (3) 定义了一个恒定的主旋转，这会给场景带来深度感。振荡器设置为“saw”（锯齿波）模式，当我们希望以恒定角速度进行旋转时，经常使用这种模式。
*   部分 (4) 控制光源的旋转。它通过使用 `quat_mul` 模块组合两个旋转四元数来实现这一点。一个四元数内部由四个数字组成，完整定义了一个旋转轴和一个旋转角度。通过四元数乘法，我们可以精确实现绕两个不同轴、以两个不同角度进行两次连续旋转的效果。光源位置需要一个向量 (x, y, z)，如果使用 `float3_rotate_by_quat` 模块，从任意位置（如 (0,0,1)）开始，并利用上述组合后的四元数，输出将是 (0,0,1) 的一个旋转变体。通过将两个振荡器分别设置为“saw”（锯齿波）和“sine”（正弦波）模式，并给其中一个振荡器的角度添加一些音频输入，我们将获得一个稳定的旋转，加上一些受音频输入微弱影响的调制。后者是通过将音频音量累积相加到其中一个四元数旋转振荡器的 `phase`（相位）锚点上实现的。

![A457467_1_En_6_Fig42_HTML.jpg](img/A457467_1_En_6_Fig42_HTML.jpg)
图 6-42. 沿带状渲染的粒子系统

输出将是一个旋转的、呈溅射状的管子世界，如图 6-43 所示。

![A457467_1_En_6_Fig43_HTML.jpg](img/A457467_1_En_6_Fig43_HTML.jpg)
图 6-43. 沿带状渲染的粒子系统，输出效果

# 发光物体

通过在原始物体上叠加一个模糊版本，可以使物体获得闪亮的发光效果。作为示例，请将以下模块放置在画布上，并按图 6-44 所示进行连接。

- `Renderers → opengl_modifiers → blend_mode`
- `Renderers → opengl_modifiers → cameras → orbit_camera`
- `Renderers → opengl_modifiers → light_directional`
- `Renderers → opengl_modifiers → depth_buffer`
- `Renderers → opengl_modifiers → backface_culling`
- `Renderers → opengl_modifiers → material_param`
- `Renderers → mesh → mesh_basic_render`
- `Mesh → solid → mesh_box`

**注意：** 本小节的状态文件可在 `TheArtOfAudioVisualization` 文件夹下的 `A-6.3_Glowing_Objects` 中找到。

![A457467_1_En_6_Fig44_HTML.jpg](img/A457467_1_En_6_Fig44_HTML.jpg)

**图 6-44.** 带发光效果的物体，基本状态

## 参数设置

按表 6-63 至 6-68 设置参数。

### 表 6-68. `renderers → mesh → mesh_basic_render`

| | |
|---|---|
| | 将所有值保留为默认值 |

### 表 6-67. `renderers → opengl_modifiers → material_param`

| | |
|---|---|
| | `ambient_reflectance` | `0.2; 0.2; 0.2; 1.0` |
| | `diffuse_reflectance` | `0.95; 0.76; 0.35; 1.0` |
| | `specular_reflectance` | `0.95; 0.87; 0.87; 1.0` |
| | `emission_intensity` | `0; 0; 0; 1` |
| | `specular_exponent` | `13.0` |

### 表 6-66. `renderers → opengl_modifiers → backface_culling`

| | |
|---|---|
| | `status` | `ENABLED` |

### 表 6-65. `renderers → opengl_modifiers → depth_buffer`

| | |
|---|---|
| | `depth_test` | `ENABLED` |
| | `depth_mask` | `ENABLED` |

### 表 6-64. `renderers → opengl_modifiers → light_directional`

一个白色光源

| | |
|---|---|
| | `enabled` | `YES` |
| | `position` | `0.57; 0.25; 0.78` |
| | `ambient_color` | `0; 0; 0; 1` |
| | `diffuse_color` | `1; 1; 1; 1` |
| | `specular_color` | `1; 1; 1; 1` |

### 表 6-63. `renderers → opengl_modifiers → cameras → orbit_camera`

| | |
|---|---|
| | `rotation` | `0.52; 0.05; 0.85` |
| | `distance` | `3.5` |
| | `fov` | `30.0` |
| | `perspective_correct` | `yes` |

## 添加发光效果

输出结果将是一个如图 6-45 所示的盒子。现在向场景中添加以下模块：

- `Renderers → basic → textured_rectangle`
- `Texture → effects → blur`
- `Texture → buffers → render_surface_single`

![A457467_1_En_6_Fig45_HTML.jpg](img/A457467_1_En_6_Fig45_HTML.jpg)

**图 6-45.** 带发光效果的物体，基本状态，输出结果

按图 6-46 所示进行连接和插入。请注意 `blend_mode` 输入锚点的顺序非常重要。通常您无法直接看到它，但为了检查和修正，请双击该锚点并将其拖放到某个子锚点上以更改顺序，参见图 6-47。

![A457467_1_En_6_Fig47_HTML.jpg](img/A457467_1_En_6_Fig47_HTML.jpg)

**图 6-47.** 带发光效果的物体，混合输入顺序

![A457467_1_En_6_Fig46_HTML.jpg](img/A457467_1_En_6_Fig46_HTML.jpg)

**图 6-46.** 带发光效果的物体，发光叠加

## 新增模块参数

按表 6-69 至 6-71 设置新增模块的参数。

### 表 6-71. `texture → buffers → render_surface_single`

| | |
|---|---|
| | `texture_size` | `VIEWPORT_SIZE` | 这很重要，因为原始物体和模糊物体的位置和大小必须匹配。 |

### 表 6-70. `texture → effects → blur`

| | |
|---|---|
| | `start_value` | `12` | 控制发光的大小。尝试将其改为 `20` 或 `30` 并观察效果。 |
| | `attenuation` | `1.15` | 控制发光的衰减速率。尝试将 `start_value` 改为 `30`，并将此值改为 `2.0` 以观察效果。使用较高值会产生卡通效果。 |
| | `texture_size` | `VIEWPORT_SIZE` | 这很重要，因为原始物体和模糊物体的位置和大小必须匹配。 |
| | `passes` | `TWO` | 使用 `TWO` 代替 `ONE` 会增强发光效果。 |

### 表 6-69. `renderers → basic → textured_rectangle`

| | |
|---|---|
| | `size` | `1.007` | 设为 `1.0` 也可以，增加少量数值会增强发光效果 |

发光效果的输出结果如图 6-48 所示。如参数表所述，夸大发光参数后，输出结果将如图 6-49 所示。

![A457467_1_En_6_Fig49_HTML.jpg](img/A457467_1_En_6_Fig49_HTML.jpg)

**图 6-49.** 带发光效果的物体，使用不同参数集时的输出结果。发光的 `start_value` 参数设为 `20.0`，`attenuation` 锚点设为 `1.6`。

![A457467_1_En_6_Fig48_HTML.jpg](img/A457467_1_En_6_Fig48_HTML.jpg)

**图 6-48.** 带发光效果的物体，输出结果。左图显示未添加发光效果的原始输出。

## 总结

本章包含了一系列高级教程或案例，涵盖了 ThMAD 的更多方面并展示了其能力。第 7 章和第 8 章收录了 ThMAD Artiste 和 ThMAD Player 的 GUI 参考手册。

