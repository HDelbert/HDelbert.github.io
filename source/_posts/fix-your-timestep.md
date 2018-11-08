---
title: "修复你的时间步长"
date: 2018-04-25 21:45:40 +08:00
tags: game
---

翻译[原文地址](https://gafferongames.com/post/fix_your_timestep/)

在[前面的文章](https://gafferongames.com/post/integration_basics/)中，我们讨论了如何使用数值积分器来对运动方程积分。积分听起来很复杂，但它只是一种通过称为“时间增量”（或简称为 dt ）的一小段时间向前推进物理模拟的方法。

但是如何选择这个时间增量值呢？这可能看起来像一个微不足道的主题，但实际上有很多不同的方法来做到这一点，每个方法都有自己的优势和弱点 - 所以请继续阅读！

### 固定时间增量（Fixed delta time）

向前迈出的最简单的方法是固定时间增量，如1/60秒：

``` cpp
double t  = 0.0;
double dt = 1.0 / 60.0;

while ( !quit )
{
    integrate( state, t, dt );

    t += dt;

    render( state );
}
```

在许多方面，这个代码是理想的。如果您足够幸运，您的时间增量与显示器刷新率相匹配，并且您可以确保您的更新循环的实际时间少于一帧，那么您已经拥有了更新物理模拟器的完美解决方案，您可以停止阅读本文。

但在现实世界中，您可能不会提前知道显示器的刷新率。VSYNC 可以被关闭，或者你可能在一台速度很慢的电脑上运行，这台电脑无法以 60fps 的速度更新并且渲染。

在这些情况下，您的模拟器运行速度会比您想要的更快或更慢。

### 可变时间增量（Variable delta time）

解决这个问题 *似乎* 很简单。只需测量前一帧所需时间，然后将该值作为下一帧的时间增量反馈回来。这当然是有道理的，因为如果电脑速度太慢而无法在 60HZ 下更新并且必须降低到 30fps，那么您将自动以 1/30 的时间传递。同样的，显示器刷新率为 75HZ 而不是 60HZ 时，甚至在快速计算机上关闭 VSYNC 的情况下也是如此：

``` cpp
double t           = 0.0;
double currentTime = hires_time_in_seconds();

while ( !quit )
{
    double newTime   = hires_time_in_seconds();
    double frameTime = newTime - currentTime;
    currentTime      = newTime;

    integrate( state, t, frameTime );

    t += frameTime;

    render( state );
}
```

但是现在我将解释一下这个方法存在的一个巨大问题。问题在于你物理模拟器的行为取决于你传入的时间增量。这个效果可能很微小，因为你的游戏根据帧率有一个稍微不同的“感觉”，或者它可能和你的弹簧模拟爆炸到无穷大一样极端，快速移动的物体穿过墙壁和穿过地板的玩家！

但有一点是肯定的，那就是期望你的模拟器能够正确处理传递给它的任何时间增量，是完全不切实际的。要理解为什么，考虑，如果你用 1/10 秒作为时间增量会发生什么？10 秒？100 秒？最终你会发现一个突破点。

### 半固定时间增量（Semi-fixed timestep）

更确实的说，只有当时间增量小于或等于某个最大值时，您的模拟器才能正常运行。在实践中，这通常比试图在广泛的时间增量值上进行仿真更加容易。

有了这些知识，下面是一个简单的技巧，可以确保您不会在大于最大值的时间增量内传递数据，同时仍能在不同的机器上以正确的速度运行：

``` cpp
double t  = 0.0;
double dt = 1 / 60.0;

double currentTime = hires_time_in_seconds();

while ( !quit )
{
    double newTime   = hires_time_in_seconds();
    double frameTime = newTime - currentTime;
    currentTime      = newTime;

    while ( frameTime > 0.0 )
    {
        float deltaTime = min( frameTime, dt );

        integrate( state, t, deltaTime );

        frameTime -= deltaTime;
        t         += deltaTime;
    }

    render( state );
}
```

这种方法的好处是我们现在有一个时间增量的上限。如果它是我们细分时间步长，它将永远不会大于这个最大值。缺点是我们现在每次显示更新需要多个步骤，包括一个额外的步骤来消耗 dt 不能被 div 整除的剩余帧时间。如果渲染绑定，这不成问题，但如果仿真是框架中最昂贵的部分，则可能会遇到所谓的“死亡螺旋”。

什么是死亡螺旋？当你的物理模拟无法跟上要求采取的步骤时，会发生什么情况。例如，如果你的仿真被告知：“好的，请模拟 X 秒的物理学价值”，如果 Y > X 时需要Y秒的实时时间，那么爱因斯坦不会意识到随着时间的推移，你的仿真会落后。这就是所谓的死亡螺旋，因为落后导致你的更新需要模拟更多的步骤才能赶上，这会导致你落后，导致你模拟更多的步骤......

那么我们如何避免这种情况呢？为了确保稳定的更新，我建议留下一些空间。您确实需要确保实时更新 X 秒的物理模拟所需的时间 *显著* 少于 X 秒。如果你可以做到这一点，那么你的物理引擎可以通过模拟更多帧来“赶上”任何临时峰值。或者，您可以在每帧最大步数处进行钳位，并且在重负载下模拟似乎会减慢。可以说这比螺旋式死亡要好，尤其是如果重负荷只是暂时的高峰。

### 自由的物理模拟

现在让我们再进一步。在给定相同输入的情况下，如果您想从一次运行到下一次运行需要准确的重现性，该怎么办？这在尝试使用确定性固定步长时，联网物理模拟非常方便，但通常也是一件很好的事情，要知道从一次运行到下一次运行，您的模拟行为完全一样，而根据渲染帧率不同，不会有任何不同行为的可能性。

但是你问为什么需要完全固定的时间增量来做到这一点？当然，小余数阶段的半固定时间增量“足够好”？是的，你是对的。在大多数情况下它已经足够好了，但由于浮点运算的精度有限，它不完全相同。

我们现在想要的是两全其美：模拟的固定时间增量值以及以不同帧速率渲染的能力。这两件事似乎完全不一致，它们是 - 除非我们能找到一种方法来解耦模拟和渲染帧率。

以下是如何做到这一点。在固定 dt 时间步骤中提前进行物理仿真，同时确保它跟上来自渲染器的定时器值，以便仿真以正确的速率前进。例如，如果显示帧率为 50fps，模拟运行速度为 100fps，那么我们需要在每次显示更新时采取两个物理步骤。简单吧。

如果显示帧率是 200fps 呢？那么在这种情况下，我们需要在每次显示更新时采取一半的物理步骤，但是我们不能这样做，我们必须用固定 dt 来提前。所以我们每两次更新显示一个物理步骤。

更棘手的是，如果显示帧率是 60fps，但我们希望我们的模拟以 100fps 运行？没有简单的倍数。如果 VSYNC 被禁用并且显示帧速率在帧之间波动，该怎么办？

如果你头脑发生爆炸，不用担心，解决这个问题所需的一切就是改变你的观点。与其认为自己具有一定的帧时间，则必须在渲染之前进行模拟，颠倒视角并认为它是这样的：渲染器产生时间，并且仿真在离散的 dt 步骤中消耗它。

例如：

``` cpp
double       t = 0.0;
const double dt = 0.01;

double currentTime = hires_time_in_seconds();
double accumulator = 0.0;

while ( !quit )
{
    double newTime   = hires_time_in_seconds();
    double frameTime = newTime - currentTime;
    currentTime      = newTime;

    accumulator += frameTime;

    while ( accumulator >= dt )
    {
        integrate( state, t, dt );
        accumulator -= dt;
        t += dt;
    }

    render( state );
}
```

注意，与半固定时间步不同的是，我们只能将步长与 dt 集成在一起，因此在通常情况下，我们在每个帧的末尾都剩下一些未模拟的时间。随着时间的推移，它通过累加器变量传递到下一帧，不会被丢弃。

### 最终效果

但是剩下的时间呢？这似乎不正确，不是吗？

要了解正在发生的事情，请考虑显示帧率为60fps并且物理运行速度为 50fps 的情况。没有很好的倍数，所以累加器会导致模拟之间交替进行，大多数情况下，当剩余部分“积累”到 dt 之上时，每帧只需要一个和偶尔两个物理步骤。

现在考虑大多数渲染帧会在累加器中剩余一些剩余帧时间，因为它小于 dt，所以无法模拟。这意味着我们在与渲染时间略有不同的时间显示物理模拟的状态，导致屏幕上物理模拟的微妙但在视觉上令人不愉快的口吃。

解决这个问题的一个方法是基于累加器剩余的时间在先前和当前的物理状态之间进行插值：

``` cpp
double t  = 0.0;
double dt = 0.01;

double currentTime = hires_time_in_seconds();
double accumulator = 0.0;

State previous;
State current;

while ( !quit )
{
    double newTime   = time();
    double frameTime = newTime - currentTime;
    if ( frameTime > 0.25 )
        frameTime = 0.25;
    currentTime = newTime;

    accumulator += frameTime;

    while ( accumulator >= dt )
    {
        previousState = currentState;
        integrate( currentState, t, dt );
        t += dt;
        accumulator -= dt;
    }

    const double alpha = accumulator / dt;

    State state = currentState * alpha + previousState * ( 1.0 - alpha );

    render( state );
}
```

这看起来很复杂，但这是一个简单的思考方式。累加器中的任何余数实际上是在另一个整个物理步骤可以进行之前需要多少时间的度量。例如，dt / 2 的其余部分意味着我们正处于当前物理步骤和下一步物理步骤之间的一半。dt * 0.1 的其余部分意味着更新是当前状态和下一状态之间的十分之一。

我们可以使用这个余数值来简单地通过除以 dt 来获得之前和当前物理状态之间的混合因子。这给出了一个在 [0,1] 范围内的 alpha 值，用于在两个物理状态之间执行线性插值以获取当前状态。这种插值对于单值和矢量状态值很容易实现。如果将方向作为四元数存储并使用球面线性插值（slerp）在前一个方向和当前方向之间进行混合，则甚至可以将其用于完整的 3D 刚体动力学。