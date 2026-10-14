# 场的实验室：动画与公式

配套动画：[打开交互页面](field-lab.html)。在图中拖动探针 \(P\)，可以改变观察位置。以下 \(k\) 是“强度”，\(w\) 是山丘宽度，\(t\) 是**动画中的模拟时间**（不是实际经过的秒数）。

## 1. 梯度

山丘／山谷使用同一个标量场，只改变正负号：

$$
u(x,y)=\pm k\exp\!\left(-\frac{x^2+y^2}{2w^2}\right),\qquad
\nabla u=\left(-\frac{x}{w^2}u,-\frac{y}{w^2}u\right).
$$

红箭头是 \(\nabla u\)。当梯度不为零，沿单位方向 \(\hat{\mathbf{n}}=\nabla u/|\nabla u|\) 走一小步 \(\Delta s\)，高度的一阶变化为

$$
\Delta u\approx\nabla u\cdot\hat{\mathbf{n}}\,\Delta s
=|\nabla u|\,\Delta s.
$$

右侧展示同一个函数的**三维曲面**：半透明红片是探针处的局部切平面，红线沿曲面指向梯度方向，黄点在曲面上沿红线移动。\(|\nabla u|\) 是探针附近沿该方向的局部上升率；黄点走过有限距离后的实际高度变化不必恰好等于 \(|\nabla u|\Delta s\)。

```js
function scalar(x, y) {
  return sign * strength * Math.exp(-(x*x + y*y) / (2*width*width));
}
function grad(x, y) {
  const u = scalar(x, y);
  return {x: -x*u/(width*width), y: -y*u/(width*width)};
}
const magnitude = Math.hypot(g.x, g.y);
if (magnitude > 0.001) {
  const nx = g.x / magnitude, ny = g.y / magnitude;
  const sample = scalar(x + nx*distance, y + ny*distance);
}
```

## 2. 散度

“源”和“汇”分别取 \(\mathbf{F}=\pm k(x,y)\)。它们的散度为 \(\nabla\cdot\mathbf{F}=\pm2k\)。跟着流动的小圆半径和面积满足

$$
R(t)=R_0e^{\pm kt},\qquad
\frac{A(t)}{A_0}=e^{\pm2kt},\qquad
\frac{1}{A}\frac{dA}{dt}=\nabla\cdot\mathbf{F}=\pm2k.
$$

图中**虚线是初始边界，实线是当前边界**。无源平流 \(\mathbf{F}=(k,0)\) 与无源旋涡 \(\mathbf{F}=k(-y,x)\) 的散度都为零：前者平移、后者旋转，但小块面积都不变。右侧为便于比较，扣除了整体平移。

```js
const sign = mode === 'source' ? 1 : mode === 'sink' ? -1 : 0;
const radiusNow = radiusInitial * Math.exp(sign * strength * t);
const areaRatio = Math.exp(2 * sign * strength * t);
```

## 3. 旋度

右旋模式取 \(\mathbf{F}=k(-y,x)\)，左旋取 \(\mathbf{F}=k(y,-x)\)。在 \(xy\) 平面内：

$$
(\nabla\times\mathbf{F})_z
=\frac{\partial F_y}{\partial x}-\frac{\partial F_x}{\partial y}
=\pm2k,
\qquad \theta(t)=\pm kt,
\qquad \Omega=\frac{d\theta}{dt}=\frac{1}{2}(\nabla\times\mathbf{F})_z.
$$

虚线方块是初态，实线方块是随体转动后的状态。无旋平流 \(\mathbf{F}=(k,0)\) 不自转；**无旋发散** \(\mathbf{F}=k(x,y)\) 会变大却不自转。这说明“无旋度”和“无源”是两件不同的事。

```js
const sign = mode === 'right' ? 1 : mode === 'left' ? -1 : 0;
const angle = sign * strength * t;
const sizeNow = mode === 'radial'
  ? sizeInitial * Math.exp(strength * t) : sizeInitial;
ctx.rotate(-angle); // Canvas 的 y 轴向下，故数学正角取负号
```