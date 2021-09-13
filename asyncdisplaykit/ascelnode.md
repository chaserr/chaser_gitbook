# ASCelNode

Q： 添加一个自定义的View到ASCellNode中并不是一件容易的事情，和添加一个原生的ASDisplayNode不一样的是，你需要自行处理Cell被重新渲染时的状态

ASCellNode的生命周期

init -&gt; didLoad -&gt; calculateSizeThatFits -&gt; layout -&gt; clearViews/ showViews -&gt; deinit

一个 ASDisplayNode 在 init 至 layout 的过程中，都只是属性的操作，这个操作并不会生成任何实际的 UIView，这也是为什么 AsyncDisplayKit 高效的原因之一，将 UIView 实例化的过程推迟至最终使用时。一个 ASDisplayNode 中的 UIView 生成、清除的时机由 ASTableView决定， ASTableView 总是保证只保留用户所看到的 Node 存在 UIView 实例。 因此， ASCellNode 中的 subnode 也同时遵循上图的生命周期

