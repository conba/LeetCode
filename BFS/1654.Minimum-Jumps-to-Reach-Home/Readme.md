### 1654.Minimum-Jumps-to-Reach-Home

本题看上去是一个常规的BFS求最短路径问题：每到一处新的位置，我们有两种选择，向左走或者向右走。已经走过的地方就标记visited以后不再访问。直至访问到x位置。

但是在写代码的过程中，我们会发现一个问题，那就是如果无限制使用朝右走的权利的话，永远不会停止，这样这个队列不会收敛到空。所以我们似乎应该有一个limit，往右边超越这个limit的时候就不该继续走下去。

这个limit如何设计呢？我们假设整个路径是通过m次右移和n次左移实现从0到x的。我们可以知道这m次右移和n次左移，我们任意打乱先后顺序的话（但这个乱序要避免碰到forbidden），并不影响最终到达x的结论。既然如此，我们就没有必要让人一路狂飙到右极限再一路狂飙回来。只要出现```cur-b > x```的状态，那么就意味着我们之后至少需要一次左移（目的是拉回x附近），那么我们索性就规定这种情况下只能左移，不允许右移。如果需要右移，可以安排在这一步之后再走，并且这不会影响到达x的结论。

于是我们就找到了这样的一个limit = x + b，如果当前位置cur超过了这个limit的话就只可以选择左移。

但是这个limit有一个bug，那就是如果cur>limit，但是选择左移的时候碰到的是一个forbidden位置，岂不是意味着这个状态就没有任何后继节点了？这就涉及到了上面提到的那个问题，理论上m次右移和n次左移都可以实现从0到x，但是条件是该行走顺序中间不能碰到任何forbidden。为了解决这个问题，我们松弛这个limit的要求，如果能保证 cur-b 大于x、并且也大于任何可能的forbidden位置，那么我们就可以放心地只选择左移。因此这个limit的边界定义为 max(x, max_forbidden) + b。

综上，本题的BFS策略是：
1. 如果```cur <= limit```，允许往右移动（前提是不触碰forbidden）
2. 如果到达cur时是右移的，那么允许往左移动（前提是不触碰forbidden且不越过0点）
