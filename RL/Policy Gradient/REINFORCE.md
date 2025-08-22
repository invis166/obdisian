# Simple policy gradient
Идея: хотим учить не Value-функцию, а сразу научиться считать градиент по реворду.

$\tau = (a_1, s_1, ..., a_T, s_T)$ - траектория,   $R_\tau = \sum_{t=1}^T r(a_t, s_t)$ - реворд за траекторию, $\pi_\theta$ - политика, $p_\theta(\tau) = p(s_1) \prod_{t=1}^{T} \pi_\theta(a_t, s_t) p(a_{t + 1} | a_t, s_t)$ - распределение на траектории.
Будем оптимизировать ожидаемый реворд $J(\theta) = \mathbb{E}_{\tau \sim p_\theta(\tau)} [R_\tau]$ напрямую.
Нам нужно посчитать $\nabla_\theta J(\theta) = \int \nabla_\theta p_\theta(\tau) R_\tau d \tau$. Проблема в том, что не понятно, что делать с $\nabla_\theta p_\theta(\tau)$ (хотя бы потому, что мы его не умеем считать, т.к. для этого нам требуются неизвестные вероятности переходов между состояниями). На помощь приходит *log derivative trick*:
$$
\begin{aligned}
	& \nabla_\theta \log p_\theta(\tau) = \frac{\nabla_\theta p_\theta(\tau)}{p_\theta(\tau)} \implies \nabla_\theta p_\theta(\tau) = p_\theta(\tau) \nabla_\theta \log p_\theta(\tau) \\

	& \nabla_\theta \log p_\theta(\tau) = \nabla_\theta \log p(s_1) + \sum_{t=1}^T \nabla_\theta \log \pi_\theta(a_t, s_t) + \nabla_\theta \log p(a_{t + 1} | a_t, s_t) = \\ 
	& = \sum_{t=1}^T \nabla_\theta \log \pi_\theta(a_t, s_t)
\end{aligned} 
$$
Тогда градиент параметров относительно реворда выражается следующим образом:
$$
\nabla_\theta J(\theta) = \int p_\theta(\tau) \sum_{t=1}^T \pi_\theta(a_t, s_t) R_\tau d\tau = \mathbb{E}_{\tau \sim p_\theta(\tau)} [\sum_{t=1}^T \nabla_\theta \log \pi_\theta(a_t, s_t) R_\tau]
$$ 
Выражение в матожидании мы может легко посчитать: обычно политика -- это сетка; с ревордом очевидно. Само матожидание мы можем аппроксимировать по Монте-Карло, играя траектории:
$$
\nabla_\theta J(\theta) \approx \frac{1}{N} \sum_{i=1}^N \left( \sum_{t=1}^{T^i} \nabla_\theta \log \pi_\theta(a_t^i, s_t^i) R_{\tau^i} \right) = \frac{1}{N} \sum_{i=1}^N \left( \sum_{t=1}^{T^i} \nabla_\theta \log \pi_\theta(a_t^i, s_t^i) \right)  \left( \sum_{t=1}^{T^I} \gamma^tr(a_t^i, s_t^i) \right)
$$
# Policy gradient with baseline

Заметим, что 
$$
\begin{aligned}
	\nabla_\theta J(\theta) &= \mathbb{E}_{\tau \sim p_\theta(\tau)}\left[
		 \left( \sum_{t=1}^{T} \nabla_\theta \log \pi_\theta(a_t, s_t) \right) 
		 \left( \sum_{t=1}^{T} \gamma^t r(a_t, s_t) \right)
	\right]= \\
	&=\sum_{t=1}^{T} \mathbb{E}_{\tau \sim p_\theta(\tau)}\left[
		 \nabla_\theta \log \pi_\theta(a_t, s_t) 
		 \left( \sum_{j \ge t}^{T} \gamma^t r(a_j, s_j) \right)
	\right]
\end{aligned}
$$

Доказательство.
Воспользуемся линейностью матожидания:
$$
\begin{aligned}
	&\mathbb{E}_{\tau \sim p_\theta(\tau)}\left[ \nabla_\theta \log \pi_\theta(a_t, s_t) \sum_{j<t} r(a_j, s_j) \right] 
	= \\
	&= \mathbb{E}\left[ \mathbb{E} \left[ \nabla_\theta \log \pi_\theta(a_t, s_t) | s_1, a_1, ..., s_t \right] \sum_{j<t} r(a_j, s_j) \right] = 0
\end{aligned}
$$
(т.к. $\mathbb{E} \left[ \nabla_\theta \log \pi_\theta(a_t, s_t) | s_1, a_1, ..., s_t \right] = 0$)
Воспользовались законом полной вероятности и тем, что награда детерминированная. Внешнее матожидание берется по $s_0, a_0, ..., s_t$, внутреннее по $a_t$.

Более того, аналогичным образом можно показать, что 
$$
\mathbb{E}_{\tau \sim p_\theta(\tau)}\left[ \nabla_\theta \log \pi_\theta(a_t, s_t) B(s_t) \right] = 0
$$
Где $B(s_t)$ -- некоторая функция от $s_t$.
Таким образом, алгоритм можно представить в следующем виде:
$$
\nabla_\theta J(\theta) = \sum_{t=1}^{T} \mathbb{E}_{\tau \sim p_\theta(\tau)}\left[
		 \nabla_\theta \log \pi_\theta(a_t, s_t) 
		 \left( \sum_{j \ge t}^{T} \gamma^j r(a_j, s_j) - \gamma^t B(s_t) \right)
\right]
$$
Данный подход помогает снизить дисперсию. $B(s_t)$ будем называть бейзлайном.

Также исходный функционал можно эквивалентно записать в терминах $Q$-функции:
$$
\begin{aligned}
	\nabla_\theta J(\theta) 
	&=\mathbb{E}_{\tau \sim p_\theta(\tau)} \sum_{t=1}^{T} \left[
		 \nabla_\theta \log \pi_\theta(a_t, s_t) \gamma^t Q(a_t, s_t)
	\right]
\end{aligned}
$$
Доказательство:
$$
\begin{aligned}
&\sum_{t=1}^{T} \mathbb{E}_{\tau \sim p_\theta(\tau)}\left[
		 \nabla_\theta \log \pi_\theta(a_t, s_t) 
		 \left( \sum_{j \ge t}^{T} \gamma^j r(a_j, s_j)\right)
\right]
= \\
&= \sum_{t=1}^{T} \mathbb{E}_{s_0,a_0,...,s_t,a_t}\left[
		 \nabla_\theta \log \pi_\theta(a_t, s_t) 
		 \mathbb{E}_{s_{t+1}, a_{t+1}...}\left[ \sum_{j \ge t}^{T} \gamma^j r(a_j, s_j)\right]
\right] \\
&= \sum_{t=1}^{T} \mathbb{E}_{s_0,a_0,...,s_t,a_t}\left[
		 \nabla_\theta \log \pi_\theta(a_t, s_t) 
		 \gamma^t Q(s_t, a_t)
\right] \\
\end{aligned}
$$

Прикольный факт, что мы по сути никак явным образом не вкладываем exploration в нашу политику. Косвенно эта проблема решается дополнительной регуляризацией на распределение вероятностей, например, при помощи энтропии.
# Другие трюки
Во-первых, давайте воспользуемся [[Расщепление Стохастики|расщиплением стохастики]], и приведем градиент к виду
$$
\nabla_\theta J(\pi) = 
\frac{1}{1-\gamma}\mathbb{E}_{s\sim d{_\pi}}\mathbb{E}_{a\sim\pi_\theta(*|s)} \nabla_\theta\log\pi_\theta(a|s)Q(s, a)
$$
Теперь другой хак: Если рассмотреть формулу исходного градиента, то слагаемые в ней после 100 шага уже почти не вносят никакого вклада в оптимизацию. Давайте просто выкинем дисконтирование. В там случае, состояния будут семплироваться уже не из $d_\pi$, а из другого распределения $\mu_\pi(s):$
$$
\mu_\pi(s) = \lim_{T\to\infty}\frac{1}{T}\sum_{t = 0}^T p(s_t=s|\pi)
$$
И формула примет вид 
$$
\nabla_\theta J(\pi) \approx \frac{1}{1-\gamma}\mathbb{E}_{s\sim \mu{_\pi}}\mathbb{E}_{a\sim\pi_\theta(*|s)} \nabla_\theta\log\pi_\theta(a|s)Q(s, a)
$$
# Полезные ссылки
1. https://cs229.stanford.edu/notes2020fall/notes2020fall/cs229-notes14.pdf