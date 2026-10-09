# 构建、修复与验收清单

## 编译命令（在报告工程目录内执行）

```bash
cd <report目录> && latexmk -xelatex -outdir=build -jobname=<jobname> -interaction=nonstopmode -file-line-error <main.tex>
```

- 中文文档必须 XeLaTeX；
- 用 `-jobname` 区分多个 PDF（如汇报 / 导读）；
- latexmk 自动多轮编译处理交叉引用。

## 验收清单

- [ ] `pdfinfo build/<jobname>.pdf` 页数、标题、大小正常；
- [ ] `rg -n "Overfull" build/<jobname>.log` 无可见超宽（0 或极小）；
- [ ] `rg -n "Missing character|Undefined control sequence|Missing \$" build/<jobname>.log` 无错误；
- [ ] `pdftoppm -png -r 130 build/<jobname>.pdf preview/page` 渲染全部预览；
- [ ] 逐页视觉检查：封面、每张图的带读页、代码页、表格页、结尾页；
- [ ] 正文连续、无空白页、无强制分页（检查是否误留 `\clearpage`）；
- [ ] 每个图下方紧跟逐项带读，不跨页分离；
- [ ] 表格在页边距内（固定列宽 + center + \small）；
- [ ] `pdftotext -enc UTF-8 build/<jobname>.pdf -` 抽取文本，校验所有章节标题存在、无内容丢失；
- [ ] 论文数字逐格核对（区分 up to 措辞与精确值；任务—条件—数值一一对应）；
- [ ] 复制最终 PDF 到稳定交付名；
- [ ] `zip -r <name>_source.zip main.tex preamble.tex references.bib latexmkrc README.md sections assets scripts` 打包源码；
- [ ] `mcp__cherry-tools__report_artifacts` 声明最终交付。

## 常见错误与修复速查

| 报错/现象 | 原因 | 修复 |
|---|---|---|
| `\accent already defined` | 自定义命令与内置冲突 | 改名 `\highlight` |
| `Missing $ inserted` + 乱码 | Markdown 反引号里的 `_` 进数学模式 | 改用 `\texttt{...}`，下划线写 `\_` |
| `Undefined control sequence \无法` | TikZ 节点用单个 `\` | 用 `\\` 换行 |
| `macro parameter character #` | 裸 `#` | 写 `\#` |
| `①...` Missing character | 带圈数字字体缺失 | 用 `(1)(2)(3)` |
| 表格超出页边（如 348pt） | `tabularx` 混用 `p{}+Y` | 固定两列 `tabular` + `center` + `\small`，列宽和 < 1.0 |
| 代码路径 `\texttt` 不换行 | 无断点 | 收窄第一列 / `\small` / 逐行 itemize |
| 空白页 / 半空白页 | `\clearpage` 过多 | 正文章节之间移除 `\clearpage` |
| 图重复出现两份 | 中英双语并排页面 | 只裁英文半页 |
| PDF 读不了（密码） | 受保护 | `pdftotext` + `pdftoppm` 渲染 + 视觉读取 |
| 命令"找不到文件" | 后台 cd 不回传 | `cd ... && cmd` 同一条命令，或用绝对路径 |
| `Label 'LastPage' multiply defined` | 手写 `\label{LastPage}` | 删除，交给 lastpage 宏包 |
| 重排后章节号错 | 正文写死"第 N 章" | 用描述性名称或 `\ref` |

## 内容核对要点

- 论文中的每个结果数字：任务/条件/样本量/统计对象（全部样本 / 失败子集 / 成功子集）逐一对应，避免张冠李戴；
- 论文正文的关键参数（阈值、频率、速度/超参、几何角度等）与论文正文/附录核对；
- 摘要 "up to / 最高达" 等宣传性措辞与正文表格精确值区分；
- 若论文开源代码，正文描述与当前代码版本差异单独成框说明；
- 不把实验结果等同于现实/临床应用；不重新训练、不伪造数据、不臆造论文没说的结论。
