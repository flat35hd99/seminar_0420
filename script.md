---
marp: true
theme: gaia_gd
---

# コンピュータシミュレーションの基礎（p17 ~ p25）

---

## Contents
 - コンピュータシミュレーションの適用範囲
 - Difference between MD and MC
   - MC法の簡単な説明
 - MD法の準備
   - 実験室との違い
   - 解析力学

---

## コンピュータシミュレーションの適用範囲

---

### 性質
数値解析的：式を離散化（discretization）して、小さいstepを積み重ねる。
Pros
 - 空間・時間分解能はどれだけでも小さい値を任意に設定できる。
 - 観察による誤差がない

Cons
 - 大きな系や長時間の運動を記述できない。
 - 数値計算特有の誤差がある

適用する対象は多岐にわたる。（P18 表1.1参照）

---

### Monte Carlo method
$NVT$アンサンブルの場合を簡単に紹介

from $\nu$ - 1 step to $\nu$ step
1. ランダム(1)に次の座標$r_{trial}$を決定する
2. 1で決定した座標$r_{trial}$が許容できるか（ありえそうか）判別する(2)
   1. if (isExist == true) その座標であるとして移動して、次のstep(座標)を計算する
   2. else 移動しなかったとしてその場にとどまり、次のstep(座標)を計算する

---

(1) 確率$\prod$は
$$
\prod \propto \exp(- \beta V_N \{ x^N(\nu) \})
$$ 

(2) 判別式のは系によって異なるが
$$
\frac{\prod_{trial}}{\prod_{\nu}} = \frac{\exp(- \beta V_N \{ x^N_{trial} \})}{\exp(- \beta V_N \{ x^N(\nu) \})}
$$

---

$\frac{\prod_{trial}}{\prod_{\nu}}$は
1. $\frac{\prod_{trial}}{\prod_{\nu}} \geq 1$のとき、採用
2. $\frac{\prod_{trial}}{\prod_{\nu}} < 1$のとき
   1. $\frac{\prod_{trial}}{\prod_{\nu}}$の確率で採用
   2. $1 - \frac{\prod_{trial}}{\prod_{\nu}}$の確率で棄却

---
### MD, MCの違い

|                  | Molecular Dynamics | Monte Carlo | Molecular Mechanics | 
| ---------------- | :----------------: | :---------: | :-----------------: | 
| 静的性質（構造, 熱力学量） | 〇                 | 〇          | △                  | 
| 動的性質         | 〇                 | ✕          | ✕                  | 
| 自由エネルギー   | △                 | 〇          | ✕                  | 

> 教科書20P, 表1.2

---

## MD法のための準備

### 実験室とシミュレーションの違い

- 実験室：温度$T$, 圧力$P$一定
- MD計算：？？

---

### ニュートンの第二法則（$F = ma$）の困難
温度や圧力は結果として得られる。

#### 分子の初期速度や系の体積を適切に設定すれば制御できるのでは？
No. シミュレーションする系は小さく、これを指定した値に制御するには熱浴に接したりピストンを自動調節しないといけない。

**--> より一般化した$L$や$H$を用いて熱浴やピストンを含めた運動方程式を記述する。**
これは、
> 多原子分子に対してはるかにエレガントな取り扱いができる

---

### 一般化座標とラグランジュ形式
一般化座標
$$
q = (q_1, q_2, ... , q_n)
$$

$$
L(q, \dot{q}) = T(q, \dot{q}) - V(q)
$$

 - $L$はq, $\dot{q}$の関数、Vはqのみの関数
 - qと$\dot{q}$は互いに独立であるとして扱う

---

### 一般化座標とラグランジュ形式
$$
S[q(t)] = \int_{t_1}^{t_2} L[q, \dot{q}] dt
$$

#### ハミルトンの原理
> 作用Sの停留値を与える軌跡q(t)が古典運動に対応する

> つまり、系の軌跡$q(t)$に対して微小変位$\delta q$を加えても S は変化しない

---

$$
\begin{aligned}
    S[q(t)] &= \int_{t_1}^{t_2} L[q, \dot{q}] dt \\
\end{aligned}
$$

$$
\begin{aligned}
    \delta S &= S[q(t) + \delta q(t)] - S[q(t)] \\
    &= \int_{t_1}^{t_2} {L(t, q + \delta q, \dot{q} + \delta \dot{q}) - L(t, q, \dot{q})} dt
\end{aligned}
$$

Lに対する一次のテイラー展開を考えると

$$
L(t, q + \delta q, \dot{q} + \delta \dot{q}) = L(t, q, \dot{q}) + \frac{\partial L}{\partial q} \delta q + \frac{\partial L}{\partial \dot{q}} \delta \dot{q}
$$

$$
\delta S = \int_{t_1}^{t_2} \left(\frac{\partial L}{\partial q} \delta q + \frac{\partial L}{\partial \dot{q}} \delta \dot{q} \right) dt
$$

---
$$
\begin{aligned}
 \int_{t_1}^{t_2} \frac{\partial L}{\partial \dot{q}} \delta \dot{q} dt &=  \int_{t_1}^{t_2} \frac{\partial L}{\partial \dot{q}} \frac{d\delta q}{dt} dt \\
 &= \left[\frac{\partial L}{\partial \dot{q}} \delta q  \right]^{t_2}_{t_1} - \int_{t_1}^{t_2} \frac{d}{dt} \left( \frac{\partial L}{\partial \dot{q}} \right) \delta q dt
\end{aligned}
$$

> $t = t_1, t_2$におけるq(t)の値は境界条件として与えられているので、軌跡の両端は初めから固定されているとみなされる。つまり、$t = t_1, t_2$においては$\delta q(t) = 0$　... 第一項の定積分は0となる。

$$
\delta S = \int^{t_2}_{t_1} \left( \frac{\partial L}{\partial q} - \frac{d}{dt} \left( \frac{\partial L}{\partial \dot{q}} \right) \right) \delta q dt
$$

---

$\delta S = 0$となるためには、$t_1$と$t_2$の間での全ての時刻tで被積分関数が0である必要あり。

$$
\frac{d}{dt} \left( \frac{\partial L}{\partial \dot{q}} \right) - \frac{\partial L}{\partial q} = 0
$$

ラグランジュ(Lagrange)の運動方程式が導出された。

---

n次元系に対しても
$$
\frac{d}{dt} \left( \frac{\partial L}{\partial \dot{q_i}} \right) - \frac{\partial L}{\partial q_i} = 0
$$
とn組の連立微分方程式として記述される。

ちなみに
$$
p_i = \frac{\partial L}{\partial \dot{q}_i}, F_i = - \frac{\partial V}{\partial q_i}
$$