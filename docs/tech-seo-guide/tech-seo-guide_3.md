## 第二部分

### 额外内容：面向开发者的搜索引擎优化友好型分页指南：技巧、窍门与陷阱

嘿，我可信赖的开发者朋友们！在额外内容部分，我们将深入探讨引人入胜的分页世界。

你可能想知道为什么这个话题对我们在开发领域如此重要，那么让我们分解开来讨论：

- 分页是什么，

- 它对搜索引擎优化的影响，以及

- 正确实现它为何至关重要

## 7

## 那么，究竟什么是分页？

分页是指将网站内容划分到多个页面的做法。你在新闻网站、博客、论坛以及电商领域的几乎任何地方都见过它。

![图片](img/image-PLLT4UBU.png)

分页就像是网站内容分发的基础，但并非总是完美无缺。正确实施分页对搜索引擎优化（SEO）至关重要。

关键在于：分页设置的质量决定了搜索引擎如何索引你的页面及其包含的内容——链接、文章、产品等等。

我们来看一个分页示例。在这个例子中：该系列标题为“精彩食谱系列”，并关联了 URL 和图片。它包含“第 1 页”和“第 2 页”两个页面，每个页面都有自己的 URL 和名称。你可以通过复制结构并递增元标签中的`position`和`content`属性来添加更多页面。分页元数据指定共有 10 页，从第 1 页开始，到第 10 页结束，并且当前页面是第 1 页。

```
<div itemscope itemtype="http://schema.org/Series">
  <span itemprop="name">精彩食谱系列</span>
  <link itemprop="url" href="https://example.com/awesome-recipes">
  <link itemprop="image" href="https://example.com/awesome-recipes-thumbnail.jpg">

  <!-- 第 1 页 -->
  <div itemprop="hasPart" itemscope itemtype="http://schema.org/WebPage">
    <a itemprop="url" href="https://example.com/awesome-recipes/page/1">
      <span itemprop="name">精彩食谱 - 第 1 页</span>
    </a>
    <meta itemprop="position" content="1">
  </div>

  <!-- 第 2 页 -->
  <div itemprop="hasPart" itemscope itemtype="http://schema.org/WebPage">
    <a itemprop="url" href="https://example.com/awesome-recipes/page/2">
      <span itemprop="name">精彩食谱 - 第 2 页</span>
    </a>
    <meta itemprop="position" content="2">
  </div>

  <!-- 在此处添加更多页面 -->

  <!-- 分页元数据 -->
  <meta itemprop="numberOfPages" content="10">
  <meta itemprop="pageStart" content="1">
  <meta itemprop="pageEnd" content="10">
  <meta itemprop="currentPage" content="1">
</div>
```

## 8

### 无限滚动是怎么回事？

它恰恰与分页相反。无限滚动不是将内容切分成单独的页面，而是将所有内容塞进一个页面，让用户可以无限滚动下去。

![图片](img/image-14WEIAUL.png)

有时，你会看到一个“加载更多”按钮来显示下一批内容，但从技术上讲，它仍然是一个页面。搜索引擎会将分页和无限滚动都视为单个页面。

无限滚动有其优势，比如外观流畅且对移动端友好，但从搜索引擎优化的角度来看，它并非万能灵药。问题在于：谷歌爬虫无法模仿滚动行为，更不能点击“加载更多”。因此，搜索引擎难以有效评估和索引无限滚动页面上的所有内容。分页则没有这些问题，因为爬虫会将每个页面视为一个独立的实体。

# 开发者分页最佳实践指南

现在，我们来谈谈谷歌手册中提到的分页最佳实践：

1.  **页面链接序列** – 始终在每个页面上使用`<a href>`标签包含指向下一页的链接。同时，确保每个页面的分页中都包含指向第一页的链接，以便搜索引擎在搜索结果页面（SERP）中优先展示你的目标结果。

2.  **创建正确的 URL** – 使用带有`?page=n`参数的唯一 URL 来在地址栏中显示当前页码。避免将第一页作为权威页面（canonical）；为每个页面使用带有独立链接的规范标签。

3.  **数据结构** – 使用微标记向搜索引擎表明你的网站使用了分页。`Schema.org`在这里是你的好帮手。

4.  **避免重复文本** – 如果你在分页过程中为分类添加了介绍性文字，请将其放在第一页上。

5.  **对 URL 片段标识符说不** – 谷歌会忽略 URL 中的片段标识符（`#`）。确保你的链接是可爬取的。

6.  **`rel=”next”`和`rel=”prev”`标签** – 早在 2011 年引入的这些标签，谷歌已不再要求使用。微标记就足够了。

### 额外提示

想要给你的分页功能加速吗？考虑这些额外配置：

-   使用`preload`、`reconnect`或`prefetch`标签来加快用户的页面切换速度。

-   有两种视觉分页类型：“上一页/下一页”和“页码编号”。选择最适合你内容量的那一种。

### 开发者应对无限滚动加载指南

如果你倾向采用无限滚动方案，请记住它通常基于 AJAX 技术实现。

搜索引擎可能会遗漏部分内容，因为这些内容需待用户滚动或点击后才能加载。

以下 JavaScript 代码片段供你参考：

```javascript
window.onscroll = function() {
  // 检查用户是否已滚动至页面底部
  if (window.innerHeight + window.scrollY >= document.body.offsetHeight) {
    // 当前已到达页面底部，在此处加载更多内容
    // 这里应发起 AJAX 请求以获取额外内容
    // 新内容加载完成后，可将其追加到页面现有内容中
    // 别忘了按需实现错误处理和加载指示器
  }
};
```

代码说明：

- `window.onscroll` 是一个事件监听器，当用户滚动时会触发相关函数。
- `window.innerHeight` 表示浏览器视口的高度。
- `window.scrollY` 指示页面垂直滚动的距离。
- `document.body.offsetHeight` 返回文档的总高度。

当 `window.innerHeight` 与 `window.scrollY` 之和大于或等于 `document.body.offsetHeight` 时，说明用户已滚动至页面底部，此时可触发额外内容的加载。

请记得将注释“// 在此处加载更多内容”替换为实际加载并追加新内容的代码。

此外，建议添加防抖或节流机制，避免对滚动事件的过度调用，从而提升性能。

同时，我们需要讨论如何将内容切分为便于管理的部分。这样能确保即使禁用 JavaScript 也能保持无障碍访问，并维护 URL 的完整性。

具体拆解如下：

#### 1. 拆分内容以实现无障碍访问

在实现无限滚动时，务必将内容拆解为更小、更易访问的部分。这能确保禁用 JavaScript 的用户仍可顺畅访问和浏览内容。避免重复创建这些部分。参考以下示例：

- 示例 1：`site.com/stickers&page=1`
  此 URL 结构表示“贴纸”分类下的第一个内容页面。

- 示例 2：`site.com/stickers?lastid=123`
  使用像 `lastid` 这样的唯一标识符有助于保持 URL 的唯一性，允许用户直接访问特定部分。

- 示例：`site.com/stickers#1`
  虽然使用 URL 片段标识符（`#`）对 SEO 不理想，但仍能提供一定的可访问性。不过，尽可能使用查询参数或简洁 URL 是更优选择。

#### 2. 确保 URL 的唯一性与功能性

你的 URL 需要既唯一又具备功能性。它们应独立于用户 cookies 或浏览历史也能正常工作，这能保证流畅的用户体验以及搜索引擎的合理索引。

这里的关键示例包括：

- 唯一且功能性的 URL：`site.com/stickers&page=2`
  此 URL 指向“贴纸”分类下的第二个内容页面，既唯一又具备功能性。

- 避免 URL 片段标识符：
  不要仅依赖像 `site.com/stickers#1` 这样的片段标识符，因为它们在功能性和索引方面可能不够可靠。

#### 3. 利用 `replaceState` 和 `pushState` 提升用户体验

根据用户在网站上的行为，考虑使用 `replaceState` 和 `pushState` 进一步改善用户体验：

- `replaceState`：在用户操作（如点击或滚动）类似于鼠标点击时使用此方法。它会替换浏览器历史中的当前 URL，但不会创建新记录。
- `pushState`：当希望用户能轻松返回之前滚动过的内容时使用此方法。它会在浏览器历史中添加一条新记录。

以下是一个快速示例：

```javascript
// 使用 pushState
history.pushState({ page: 'scroll' }, '已滚动内容', '/teams/giraffes?page=2');

// 使用 replaceState
history.replaceState({ page: 'scroll' }, '已滚动内容', 'teams/giraffes?page=3');
```

通过策略性地应用这些方法，可以在实现无限滚动时提供更无缝、更友好的用户体验。

总而言之，在优化无限滚动时，需考虑可访问性、URL 结构以及通过状态管理提升用户体验。这种方式能确保包括禁用 JavaScript 用户在内的所有人都能有效浏览内容，同时改善 SEO 和用户参与度。

### 应避免的常见分页错误

最后，请避开以下常见分页陷阱：

- 使用 `noindex` 标签而非规范标签，会阻止搜索引擎正确索引你的页面。
- 避免使用静态 URL 进行分页，应采用包含动态参数的 URL。
- 确保当前分类不存在的页面返回 404 错误。

### 简而言之

不当分页的核心问题在于限制了内容的可访问性。为避免这个陷阱，请牢记以下黄金法则：

- 为分页使用爬虫可访问的用户友好型 `href` 链接。
- 仅优化序列中的首页，并从分页 URL 中移除任何特定于 SEO 的内容。
- 请记住，Google 爬虫无法滚动或点击，因此要确保所有内容即使在没有 JavaScript 的情况下也能访问。
- 在 Google Search Console 中仔细检查页面 URL 的可访问性。

请明智地进行分页设计，你的网站 SEO 会因此受益！

![作者照片](img/image-CY5D9XQU.jpg)

### 关于作者

**Andrea M. Fuller** 是一位经验丰富的数字营销与 SEO 专家，在从初创企业到全球公关公司等多个行业拥有超过 20 年的从业经验。

Andrea 对技术平台的深刻理解以及对策略的热情，使她能够为客户制定并执行独特且有效的方案。作为第四代企业家，她热衷于利用线上空间在企业/组织与其客户/选民之间建立真实互动，助力企业成功。

她拥有 `Fuller Digital Strategy, LLC`（一家提供全方位服务的数字营销机构）和 `SEO Handy Woman`（一所在线学院，教授 DIY 网站所有者如何自行执行顶级 SEO 策略）。

- 您可以通过以下方式联系我：
  - https://fullerdigitalstrategy.com
  - https://www.seohandywoman.com

- 订阅我的通讯：
  - https://fuller-digital-strategy-llc.ck.page/3b2de93afd

### Andrea M. Fuller 的其他作品

![Shopify SEO 终极指南（无废话版）](img/image-I717MFRG.png)

#### Shopify SEO 终极指南（无废话版）

任何希望将在线商店提升至新水平的 Shopify 企业家必读之作。

本书由知名 SEO 专家兼企业家 Andrea Fuller 撰写，已在超过 5 个国家销售，揭示了真正能推动销售的 SEO 秘诀。

您将学到内部策略和技巧，帮助您优化商店以迎合搜索引擎，提升排名，从而带来更多流量和销量。