>如果你英语不错，可以直接观看 t3.gg的视频 [CSS in 2023 - Tailwind vs MUI vs Bootstrap vs Chakra vs...](https://www.youtube.com/watch?v=CQuTF-bkOgc)   
## 前言
   
本来我以为 `Headless UI` 是一个大多数人熟悉的概念，但是从上篇文章的反馈来看，大多数人还是不太了解，甚至还有很多评论类似 `没有样式也能叫组件库？` , 所以我决定再写一篇文章谈谈 `CSS`, `UI`，以及 `UI Library` .

## style system 
![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/de7989d42f784394843d1bea6a930e26~tplv-k3u1fbpfcp-watermark.image?)

首先，从上图可以很清晰的看出，我们所构建的 `UI` 分为 3 个部分。一部分是 `CSS` 扩展，一部分是 `行为库` ，还有一部分是 `设计规范` 。

我们所熟知的 `antd` 就是由他的行为库 `react-component` 加上设计规范 `Antd Design` 构成。在我们使用 `antd` 来构建 `UI` 的时候，通常我们会加上自己的样式扩展，三者交汇在一起，构成了我们的 `UI` 。

这在纯应对业务上没有什么问题，可以说算是比较不错的选择。但是很多时候我们不仅仅需要满足功能上的需求，更重要的是我们需要足够的个性化以及差异化。

这个时候， `MUI` , `antd` 这类自带 `style system` 的组件库就不是一个很好的选择，毕竟做的再好也只是在他们这个`style system` 内的个性化。

而很多时候个性化所需要的是自己能完全掌控的`style system` ，而不是从 `npm` 中去下载。

> don't npm install your style system,your website deserves better
## Tailwind

 `Tailwind` 是近些年来非常火的 `CSS` 框架，它的特点是提供了一套非常丰富的 `utility class` ，通过这些 `utility class` 可以快速的构建出我们想要的样式。对于 `Tailwind` , 最常见的一个误解就是

 >`Tailwind` 不就是一个好看点的 `Bootstrap` 吗?

 其实这是一个非常大的误解，让我们首先来看一下 `Bootstrap` 官网的 `Button`
 
 ![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/162df7b2af3a41cf84126653ec42d67c~tplv-k3u1fbpfcp-watermark.image?)

 有没有很熟悉的感觉，这一堆东西是不是我们经常在 `antd` 的 `Button` 上看到。其实 `BootStrap` 在我们上图的分类中属于 `style system`, 而 `Tailwind` 只不过是一个 `CSS` 扩展让我们更加方便快速的书写 `CSS` 而已。
 
 与 `Bootstrap` 一样同属于 ``style system`` 应该是 [daisy ui](https://daisyui.com/)，不同的是 `daisy ui` 是用 `Tailwind` 书写的一套 `style system`。

 ![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f01b63cb170f47ca815d3753a027efef~tplv-k3u1fbpfcp-watermark.image?)

 为什么 `Tailwind` 在国外非常非常火，但是在国内他的热度却非常一般呢？

 第一个原因就是国外建站十分简单，几乎没有门槛，所以在这种氛围下追求差异化以及个性化不再是大公司独有的需求。
 
 第二个原因我认为是很多人都是习惯了使用 `antd` 这类自带 `style system` 的组件库，当你在这类库中想要 `Tailwind` 的时候你会发现十分难受。
 
 举一个很简单的例子，现在 `antd` 很多组件比如 `Tree`, `Select`,`TreeSelect`,`Cascader` , `Menu`, `Dropdown`,`AnChor` ... 都是推荐你传 `data`, `options` ， `items` 来渲染，而不是直接传 `children` ，如果不用 `children` 来渲染，那么你就无法用 `Tailwind` 快速构建你想要的样式。

 这类组件库大多自带的都是 `CSS In JS`，而 `cjs` 确实是构建组件库时的一个非常好的选择
## styled-components
![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b207da0d371a4d07a8e4270c409fb035~tplv-k3u1fbpfcp-watermark.image?)

说到 `cjs` 那就不能不提到 `styled-components`。上面提到,在构建组件库的时候，`cjs` 是一个非常好的选择，使用 `styled-components` 可以很方便解决我们上面说到的问题

```jsx
import styled from 'styled-components'
import {Select} from 'antd'

const StyledSelect = styled(Select)`
  .ant-select-selector {
    // write your style
  }
`
```

可以看到，不管用什么方式渲染，我们都可以通过 `styled-components` 来快速的覆盖样式。同时 `styled-components` 还支持传 `props` 来动态的改变样式，这一点在构建组件库的时候非常有用。

```jsx
import styled, {css} from 'styled-components'

const Button = styled.a<{ $primary?: boolean; }>`
  --accent-color: white;
  &>button {
    background-color: ${props => props.$primary ? 'var(--accent-color)' : css`....`};
  }
  &:hover {
    filter: brightness(0.85);
  }
```

`styled-components` 唯一的问题就是不如 `Tailwind` 那样随心所欲。当我写了三遍

```css 
 display:flex;
 justify-content: center;
 align-items: center;
```
我就要考虑是不是应该提出一个公共的 styled-components 来解决复用的问题 比如
```jsx
const FlexCenter = styled.div`
  display:flex;
  justify-content: center;
  align-items: center;
`
```
对于组件库来说，这样的做法非常普遍，因为这能显著的减少 `css` 的体积。但是在业务上使用的时候，这无疑增加了心智负担。

毕竟，很多人在写 `css` 时，都是想直接实现自己想要的样式，而不是去找一个公共的 `styled-components` 来实现。这也是 `Tailwind` 能爆火的一个很大的原因，你只管写 `css` 实现你要的样式，而不用去考虑这个样式是不是已经有了，是不是应该提出来复用。

## Behavior Libraries

聊完两个我认为不错的 `CSS ++` 以后，我们再来聊聊 `Behavior Libraries`。

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6e6f9bc397bc4662a2568edb10298325~tplv-k3u1fbpfcp-watermark.image?)

以 `Radix-UI` 为代表，`Behavior Libraries` 不会像 `MUI` 这种组件库一样，提供一套完整的组件。而是提供一套行为，让你可以在任何组件上使用。

因为不用关注样式，所以 `Behavior Libraries` 一般的 `Accessibility` 是十分完善的，而且这类组件库的体积也非常小，对于现在轻量化的趋势来说，这类组件库是非常不错的选择。

我们以 `Popover` 为例，来看看 `Radix-UI`的触发方式。（ 采用 floating-ui 计算浮动位置 ）

```jsx  
// index.jsx
import * as Popover from '@radix-ui/react-popover';
import './styles.css';

export default () => (
  <Popover.Root>
    <Popover.Trigger asChild>…</Popover.Trigger>
    <Popover.Portal>
      <Popover.Content className="PopoverContent" sideOffset={5}>
        …
      </Popover.Content>
    </Popover.Portal>
  </Popover.Root>
);
```
上面例子可以非常清晰的看到，`Radix-UI` 的触发方式是通过 `asChild` 来触发的。`asChild` 会把 `Popover.Trigger` 的 `children` 作为触发器，这样就可以在任何组件上使用 `Popover` 了。这也是 `Behavior Libraries` 的一个特点，可以在任何组件上使用,我们也能拿到组件的任何一个节点

## 构建组件库的选择

聊完上面三个方面，你可以很明显的看出来我的倾向。我认为如果想要构建自己的组件库的话，
拥有一套自己的而不是从 `npm install` 的 `style system` 是一个非常关键的地方。这也是 `Tailwind UI` 能够商业化成功的一个重要原因。毕竟，对于很多人来说，构建 `style system` 是一个非常难的事情。但是只要你肯付费 ，`Tailwind UI` 会直接把源码给你，你就可以直接使用了。

如果你像更加个性化一点，那么使用一套好的 `behavior libraries` ( [Radix-ui](https://www.radix-ui.com/) ),加上使用 `Tailwind` 或者 `styled-components` 来构建你自己的 `style system`, 最后形成一套属于你自己的组件库，这是一个非常不错的选择。