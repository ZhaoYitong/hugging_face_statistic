# hugging_face_statistic

可视化 Hugging Face 模型统计：参数规模随发布时间的演化，纯静态单文件工具（`index.html`），无需构建、无需后端。

## 使用

直接用浏览器打开 `index.html` 即可（或任意静态服务器，如 `python -m http.server`）。

## 功能

- **热门模型时间线**：从 HF API 实时拉取（支持 trending/likes/downloads 排序、Inference Provider 过滤），叠加人工整理的里程碑模型，散点图（时间 × 参数量对数轴）。
- **按作者查看**：选择/搜索任意 HF 组织或用户（输入自动联想，逗号分隔可多作者对比），查看其发布时间线；支持 `Link` 响应头自动翻页拉取全量数据，并用 `X-Total-Count` 校验完整性。
- **Tasks 下钻**：任务筛选选项由响应中的 `pipeline_tag` 动态生成，纯前端过滤。
- **完整列表**：弹窗表格展示全部拉取结果，任意列可排序（默认按时间降序）。
- **参数量精度**：优先使用 `safetensors.total` 精确值，其次从模型名解析，均无法确定的模型被过滤。

## 本地存储

- **IndexedDB**（`hf_stat` 库）：缓存每个作者的全量模型数据。再次打开时先秒出缓存，后台自动刷新（stale-while-revalidate）；刷新失败则保留缓存展示。
- **localStorage**：
  - `hf_author_index`：作者索引。点击「🌐 构建作者索引」会翻页扫描下载量最高的 1 万个模型，把遇到的作者全部持久化，供搜索联想使用，越用越全。
  - `hf_timeline_author_history`：成功加载过的作者历史。

## 数据来源

- `https://huggingface.co/api/models`（官方 API，CORS 开放，翻页 + `expand[]` 字段）
- `https://huggingface.co/models-json`（站内接口，作为热门列表首选，失败自动降级到官方 API）
- `https://huggingface.co/api/quicksearch`（作者搜索联想）

注意：`createdAt` 是 HF 仓库创建时间，不一定等于模型官宣发布时间。