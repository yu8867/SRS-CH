# バンディット問題
複数のアームと呼ばれる候補から最も良いものを逐次的に探す問題である。報酬を最大化するという点で、バンディット問題は強化学習のカテゴリに属する。バンディット問題の予測者は、限られた試行回数において得られる総報酬を最大化したい。 このとき、強化学習の普遍的なテーマである探索と活用のトレードオフが発生する。 短期的には、現時点での経験期待報酬が高いアームを引くのが良い（情報の活用）。 しかし、真の期待報酬が高いアームが、これまでたまたま運悪く少ない期待報酬しか得られていなかったということが低確率で発生する。 これを防ぐためには、すべてのアームを全体的に引く必要がある（情報の探索）。 予測者は、良いアルゴリズムに従って情報の活用と探索をバランスする必要がある。

# 期待値の更新
## 定常環境
定常環境では、各腕の報酬期待値は固定である。したがって以下の手法を用いて各腕の期待値を更新する。

![\begin{align*}
a &= \frac{1}{n_i} \\
E &= (1 - a)E + aE
\end{align*}
](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+%5Cbegin%7Balign%2A%7D%0Aa+%26%3D+%5Cfrac%7B1%7D%7Bn_i%7D+%5C%5C%0AE+%26%3D+%281+-+a%29E+%2B+aE%0A%5Cend%7Balign%2A%7D%0A)

# 方策
## SRS
SRSとは既存の方策RSにおいて大きく異なるのが、決定論的な行動選択から確率分布を生成して確率論的な行動選択を行えるように拡張した手法である。
確率論的であることのメリットでは、ある程度環境がわかった状態でも探索を行うことでより良い選択肢を探す行動を行えることである。

![\begin{align*}
 b  &= \frac{n_i}{\rho_i^z} -  N +\epsilon \\
SRS_i &=  (N + b)\rho_i^z - n_i  \\
\pi_i &=  \frac{SRS_i}{SRS_1 + SRS_2 + \cdots +SRS_k}
\end{align*}
](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+%5Cbegin%7Balign%2A%7D%0A+b++%26%3D+%5Cfrac%7Bn_i%7D%7B%5Crho_i%5Ez%7D+-++N+%2B%5Cepsilon+%5C%5C%0ASRS_i+%26%3D++%28N+%2B+b%29%5Crho_i%5Ez+-+n_i++%5C%5C%0A%5Cpi_i+%26%3D++%5Cfrac%7BSRS_i%7D%7BSRS_1+%2B+SRS_2+%2B+%5Ccdots+%2BSRS_k%7D%0A%5Cend%7Balign%2A%7D%0A)

## RS-CH
通常の方策RSは、過去の経験から満足化基準値ℵを導出しなければならず事前に環境の設定を把握しなければならない。方策RS-CHでは、動的更新より環境を知らずとも最適な値である満足化基準値ℵを求めることが可能である。

![\begin{align*}
V_G &=  max(V_i) \\
\mu_{j}^{CH} &= exp(-nD_{KL}(V_j  || V_G)) \\
\aleph^{CH} &= max \bigr\lbrace V_G\frac{1-\frac{V_j}{V_G}\mu_{j}^{CH}}{1-\mu_{j}^{CH}} \bigr\rbrace \\
\end{align*}
](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+%5Cbegin%7Balign%2A%7D%0AV_G+%26%3D++max%28V_i%29+%5C%5C%0A%5Cmu_%7Bj%7D%5E%7BCH%7D+%26%3D+exp%28-nD_%7BKL%7D%28V_j++%7C%7C+V_G%29%29+%5C%5C%0A%5Caleph%5E%7BCH%7D+%26%3D+max+%5Cbigr%5Clbrace+V_G%5Cfrac%7B1-%5Cfrac%7BV_j%7D%7BV_G%7D%5Cmu_%7Bj%7D%5E%7BCH%7D%7D%7B1-%5Cmu_%7Bj%7D%5E%7BCH%7D%7D+%5Cbigr%5Crbrace+%5C%5C%0A%5Cend%7Balign%2A%7D%0A)

## SRS-CH
今回提案する方策SRS-CHは動的更新により求めた満足化基準値ℵを用いて確率的な方策SRSに適応できるか研究した。

![\begin{align*}
if \quad V_i &> \aleph^{CH}: \\
\qquad V_i &= V_i - max(V_i) + \aleph^{CH} - \epsilon  \\
Z &= \frac{1}{\sum_{i=1}^{K}\frac{1}{\aleph^{CH} - V_i}} \\
\rho_{i}^{Z} &= \frac{Z}{\aleph - V_i}
\end{align*}
](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+%5Cbegin%7Balign%2A%7D%0Aif+%5Cquad+V_i+%26%3E+%5Caleph%5E%7BCH%7D%3A+%5C%5C%0A%5Cqquad+V_i+%26%3D+V_i+-+max%28V_i%29+%2B+%5Caleph%5E%7BCH%7D+-+%5Cepsilon++%5C%5C%0AZ+%26%3D+%5Cfrac%7B1%7D%7B%5Csum_%7Bi%3D1%7D%5E%7BK%7D%5Cfrac%7B1%7D%7B%5Caleph%5E%7BCH%7D+-+V_i%7D%7D+%5C%5C%0A%5Crho_%7Bi%7D%5E%7BZ%7D+%26%3D+%5Cfrac%7BZ%7D%7B%5Caleph+-+V_i%7D%0A%5Cend%7Balign%2A%7D%0A)

# 結果
結果として定常環境において方策SRSに対して動的更新を適用することが可能であることがわかった。また他の方策と同等の成績が得られたことから、方策SRSと方策RS-CHの両方の性質を持った方策であると考えられる。<br><br>
定常環境におけるregretの推移

<img width="654" alt="スクリーンショット 2022-03-12 13 31 50" src="https://user-images.githubusercontent.com/95354321/158003729-2199c846-fada-4280-89ab-405caa7e7145.png">

<img width="540" alt="スクリーンショット 2022-03-12 13 32 36" src="https://user-images.githubusercontent.com/95354321/158003751-a38dd1c5-fa70-440d-90c0-2359bd5c3eff.png">

# 引用文献
* [加藤 21] 加藤暦雄，甲野祐，高橋達二：満足化方策における
非満足均衡を用いた確率的方策の検証，2021 年度 人工知
能学会全国大会 (第 35 回) 論文集，(2021).
* [甲野 18] 甲野祐，高橋達二：満足化を通じた最適な自律探
索，2018 年度 人工知能学会全国大会 (第 32 回) 論文集，
(2018)．
* [高橋 16] 高橋達二，甲野祐，浦上大輔：認知的満足化 限定合理
性の強化学習における効用，人工知能学会論文誌，31(6)，
AI30-M-1-11，(2016).
