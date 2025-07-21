## Пререквизиты.
1. Функция переходов обладает Марковским свойством: $p(s_{t+1} | s_t, a_t, s_{t - 1}, ..., s_0, a_0) = p(s_{t + 1} | a_t, s_t)$ 
2. Функция переходов $p$ является стационарной, т.е $\forall t: p(s_{t+1}|a_t, s_t) = p(s_1 | a_0, s_0)$ (иначе: $\forall t,\hat t: p(s_{t+1} = \hat s | s_t = s, a_t=a) = p(s_{\hat t+1} = \hat s | s_{\hat t} = s, a_{\hat t}=a)$)
3. Политика тоже стационарная: $\forall t: \pi(a_{t+1} | s_t) = \pi(a_1 | s_0)$ (иначе: $\forall t, \hat t: \pi(a_{t+1} = a| s_t = s) = \pi(a_{\hat t + 1} = a| s_{\hat t} = s)$)
Следствие (будущее и прошлое независимы при фиксированном настоящем):
$$p(\mathcal{T}_{:t},\mathcal{T}_{:t} | s_t) = p(\mathcal{T}_{t:} | s_t, \mathcal{T}_{:t})p(\mathcal{T}_{:t} | s_t) = p(\mathcal{T}_{t:} | s_t) p(\mathcal{T}_{:t} | s_t)$$ Следствие (для подсчета награды в текущем состоянии не важно, что было до)
$$\mathbb{E}_{\mathcal{T}|s_t=s}\mathcal{R}(\mathcal{T}_{t:}) = \mathbb{E}_{\mathcal{T}_{t:}|s_t=s}\mathcal{R}(\mathcal{T}_{t:})$$
Следствие (будущее определено только текущим состоянием)
$$p(\mathcal{T}_{t:} | s_t=s) = p(\mathcal{T} | s_0 = s)$$
Следствие (комбинация предыдущих пунктов)
$$\mathbb{E}_{\mathcal{T} | s_0 = s}f(\mathcal{T}) = \mathbb{E}_{\mathcal{T}_{t:} | s_t = s}f(\mathcal{T}_{t:}) = \mathbb{E}_{\mathcal{T}|s_t=s}f(\mathcal{T}_{t:})$$
