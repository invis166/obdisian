Как было сказано в [[REINFORCE]], Baseline позволяет сбить дисперсию. Алгоритмы с бейзлайном обычно называют Actor Critic. 
# Advantage Actor Critic
Существует теоретическое оптимальное значение для бейзлайна, однако на практике оно неприменимо, поэтому используют $V$-функцию, которая довольно хорошо сбивает дисперсию. Вспомним одну из форм записи градиента по реворду:
$$
\begin{aligned}
	\nabla_\theta J(\theta) 
	&=\mathbb{E}_{\tau \sim p_\theta(\tau)} \sum_{t=1}^{T} \left[
		 \nabla_\theta \log \pi_\theta(a_t, s_t) \gamma^t Q(a_t, s_t)
	\right]
\end{aligned}
$$
Если мы в качестве бейзлайна возьмем $V$-функцию, то получим в точности адвандадж:
$$
\begin{aligned}
	\nabla_\theta J(\theta) 
	&=\mathbb{E}_{\tau \sim p_\theta(\tau)} \sum_{t=1}^{T} \left[
		 \nabla_\theta \log \pi_\theta(a_t, s_t) \gamma^t (Q(a_t, s_t) - V(s_t))
	\right] \\
	&=\mathbb{E}_{\tau \sim p_\theta(\tau)} \sum_{t=1}^{T} \left[
		 \nabla_\theta \log \pi_\theta(a_t, s_t) \gamma^t A(s_t, a_t)
	\right] \\
\end{aligned}
$$