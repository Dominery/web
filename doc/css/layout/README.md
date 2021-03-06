# 布局

## Flexbox与网格布局

flex布局与网格布局功能有重叠的地方，它们各自的使用场景不同。

### 区别

*  Flexbox本质上是一维的，而网格是二维的。

  flex换行后，没法让上一行元素跟下一行元素对齐。

*  Flexbox是以内容为切入点由内向外工作的，而网格是以布局为切入点从外向内工作的。

  Flexbox让你在一行或一列中安排一系列元素，但是它们的大小不需要明确指定，每个元素占据的大小根据自身的内容决定。

  而在网格中，首先要描述布局，然后将元素放在布局结构中去。虽然每个网格元素的内容都能影响其网格轨道的大小，但是这同时也会影响整个轨道的大小，进而影响这个轨道里的其他网格元素的大小。

#### 使用取舍

当设计要求元素在两个维度上都对齐时，使用网格。当只关心一维的元素排列时，使用Flexbox。在实践中，这通常（并非总是）意味着网格更适合用于整体的网页布局，而Flexbox更适合对网格区域内的特定元素布局。