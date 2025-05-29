# Relative Policy Identity
Пусть имеется две политики  $\pi_1$ и $\pi_2$. Тогда можно выразить $V^{\pi_2}$, играя траектории из $\pi_2$, но в качетсве реворда взяв $A^{\pi_1}$:
$$V^{\pi_2}(s) - V^{\pi_1}(s) = \mathbb{E}_{\mathcal{T}\sim \pi_2 | s_0 = s} \sum_{t\ge0}\gamma^t A(s, a)$$
*Доказательство.*
$$
\begin{aligned}
V^{\pi_2}(s) - V^{\pi_1}(s) &= \mathbb{E}_{\mathcal{T}\sim\pi_2 | s_0 = s}\sum_{t\ge0} \gamma^t r(s, a) - V^{\pi_1}(s) = \\
& = \mathbb{E}_{\mathcal{T}\sim\pi_2 | s_0 = s}\sum_{t\ge0} \gamma^t r(s, a) - V^{\pi_1}(s) = \\
& = \mathbb{E}_{\mathcal{T}\sim\pi_2 | s_0 = s} [\sum_{t\ge0} \gamma^t r(s, a) - V^{\pi_1}(s)] \\
& = \mathbb{E}_{\mathcal{T}\sim\pi_2 | s_0 = s} [\sum_{t\ge0} \gamma^t r(s, a) - V^{\pi_1}(s_0)] \\
& = \mathbb{E}_{\mathcal{T}\sim\pi_2 | s_0 = s} [\sum_{t\ge0} \gamma^t r(s, a) + \sum_{t\ge0}(\gamma^{t+1}V(s_{t+1}) - \gamma^t V(s_t)) ] \\
& = \mathbb{E}_{\mathcal{T}\sim\pi_2 | s_0 = s} [\sum_{t\ge0} \gamma^t r(s, a) + \gamma^{t+1}V(s_{t+1}) - \gamma^t V(s_t) ] \\
& = \mathbb{E}_{\mathcal{T}\sim\pi_2 | s_0 = s} [\sum_{t\ge0} \gamma^t (r(s, a) + \gamma V(s_{t+1}) - V(s_t)) ] \\
& = \mathbb{E}_{\mathcal{T}\sim\pi_2 | s_0 = s} [\sum_{t\ge0} \gamma^t (r(s, a) + \mathbb{E}_{s_{t+1}}\gamma V(s_{t+1}) - V(s_t) ) ] \\
& = \mathbb{E}_{\mathcal{T}\sim\pi_2 | s_0 = s} [\sum_{t\ge0} \gamma^t (Q(s_t, a_t) - V(s_t) ) ] \\
& = \mathbb{E}_{\mathcal{T}\sim\pi_2 | s_0 = s} [\sum_{t\ge0} \gamma^t A(s_t, a_t ) ] \\
\end{aligned}
$$
Так как матожидание адвантаджа неотрицательное, то у него есть неотрицательные значения. А значит, если мы возьмем жадную политику $\hat \pi(s) = \arg \max_a A^\pi(s, a)$, то непременно получим $V^{\hat \pi}(s) \ge V^\pi(s)$, то есть мы смогли улучшить политику на основе имеющейся.
# Policy Improvement
**Определение.** Будем говорить, что стратегия $\pi_2$ *не хуже* стратегии $\pi_1$, если 
$$\forall s: V^{\pi_2}(s) \ge V^{\pi_1}(s)$$
Если хотя бы одно неравенство выполнено строго, то будем говорить, что стратегия $\pi_2$ *лучше* стратегии $\pi_1$.

**Теорема.**
Пусть стратегии $\pi_1$ и $\pi_2$ таковы, что 
$$\forall s: \mathbb{E}_{\pi_2(a | s)} Q^{\pi_1}(s, a) \ge V^{\pi_1}(s)$$
Или, эквивалентно, 
$$\mathbb{E}_{\pi_2(a | s)}A^{\pi_1}(s, a) \ge 0$$
Тогда $\pi_2 \ge \pi_1$.  Если же хотя бы в одном состоянии неравенство обращается в строгое, то $\pi_2 > \pi_1$.
*Доказательство.*
$$
\begin{aligned}
V^{\pi_1}(s) & = \mathbb{E}_{a\sim\pi_1}Q^{\pi_1}(s, a) \le \\
& \le \mathbb{E}_{a\sim\pi_2} Q^{\pi_1}(s, a) = \\
& = \mathbb{E}_{a\sim\pi_2} [r(s, a) + \gamma \mathbb{E}_{s' \sim p(s)}V^{\pi_1}(s')] \le \\
& = \mathbb{E}_{a\sim\pi_2} [r(s, a) + \gamma \mathbb{E}_{s' \sim p(s)}\mathbb{E}_{a '\sim \pi_2} [r(s', a') + \gamma \mathbb{E}_{s''}V(s'')]] \le \\
& \le ... \le \\
&\le \mathbb{E}_{\mathcal{T} \sim \pi_2 |s_0 = s}\sum_{t\ge0} \gamma^t r(s_t, a_t)
\end{aligned}
$$

Выражение  $\mathbb{E}_{\pi_2(a | s)} Q^{\pi_1}(s, a)$ является нижней оценкой для $V^{\pi_2}(s)$ (для того, чтобы это понять, можно обратиться к доказательству этой теоремы). Таким образом, мы можем попробовать максимально улучшить эту нижнюю границу играя жадной политикой $\pi_2(a|s) = \arg \max_a Q^{\pi_1}(s, a) = \arg \max_a A^{\pi_1}(s, a)$, то мы непременно получим политику, которая будет не хуже (а может и лучше) исходной политики $\pi_1$. Если же происходит такая ситуация, что $\arg \max_a Q^{\pi_1}(s, a) = V^{\pi_1}(s, a)$, что равносильно $\arg \max_a A^{\pi_1} = 0$, то стратегия $\pi_1$ оптимальная (критерий оптимальности Беллмана)