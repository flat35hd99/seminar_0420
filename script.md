---
marp: true
theme: default
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

### MCの方法
担当範囲のMC法の説明はわかりにくかったため、（たぶん一番簡単な）$NVT$アンサンブルの場合をとても簡単に考えてみる。

on MC method: from n step to n + 1 step
1. ランダムに次の座標$r_{trial}$を決定する
2. 1で決定した座標$r_{trial}$が許容できるか（ありえそうか）判別する
   1. if (isExist == true) その座標であるとして移動して、次のstep(座標)を計算する
   2. else 移動しなかったとしてその場にとどまり、次のstep(座標)を計算する

---

### コードで表す

```python
const isExist = getIsExistTrialStep()

"""
V_n: Function   ポテンシャルエネルギー関数
r_n: Vector     全座標の配置(位置座標だけの場合は3N次元)
r_trial: Vector ランダムに決定した座標を含む全座標の配置
Returns: Boolean
"""
fun getIsExistTrialStep(V, r_n, r_trial):
    # possibilityは1を超えることもある。
    possibility = getBoltzmannFactor(V_n(r_trial))/getBoltzmannFactor(V_n(r_n))
    if (possibility >= Math.random()):
        return True
    else:
        return False

"""
r: Vector 全座標の配置
Returns: Scolor
"""
fun V_n(r):
    # some processes
    return hoge

"""
V: Scolor ポテンシャル
Returns: Scolor
"""
fun getBoltzmannFactor(V):
    # some processes
    return fuga
```

--- 
### MD, MCの違い

|                  | Molecular Dynamics | Monte Carlo | Molecular Mechanics | 
| ---------------- | :----------------: | :---------: | :-----------------: | 
| 静的性質（構造, 熱力学量） | 〇                 | 〇          | △                  | 
| 動的性質         | 〇                 | ✕          | ✕                  | 
| 自由エネルギー   | △                 | 〇          | ✕                  | 

> 教科書20P, 表1.2

? MM methodにCURP論文のAMBERが出てる。あれれ。
? 分子動力学に内包されてる？

---

## MD法のための準備

---

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