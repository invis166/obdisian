# SGD
Самый простой оптимизатор:
$$\theta_{t+1} = \theta_t - \alpha g_t$$ 
Можно добавить Nesterov Momentum:
$$
\begin{aligned}
m_{t} &= \gamma m_{t-1} + g_t \\
\theta_{t + 1} &= \theta_t - \alpha m_{t}
\end{aligned}
$$
Суть моментума в том, чтобы ускоряться в те моменты, когда новый градиент сонаправлен с предыдущими усредненными
# AdaGrad
Идея в том, чтобы аккумулировать квадраты градиентов
$$
\begin{aligned}
G_t &= G_{t-1} + g_t^2\\
\theta_{t+1} &= \theta_t - \frac{\alpha}{\sqrt{G_t} + \epsilon} g_t
\end{aligned}
$$
Параметр быстро обновляется -> делаем на нем меньший шаг
# RMSProp
У AdaGrad есть недостаток -- градиент может затухать при больших значениях аккумулирующей переменной. Чтобы его исправить, будем делать экспоненциальное сглаживание по квадратам градиента (смещенная оценка вторых моментов):
$$
\begin{aligned}
G_t &= \gamma G_{t - 1} + (1- \gamma)g_t^2 \\
\theta_{t+1} &= \theta_{t} - \frac{\alpha}{\sqrt{G_t} + \epsilon} g_t
\end{aligned}
$$
# AdaDelta
Модификация RMSProp. Будем дополнительно учитывать RMS (root mean square) дельты параметров.
$$
\begin{aligned}
\Delta \theta_k &= - \frac{RMS[\Delta \theta]_{k - 1}}{RMS[g]_k}g_k \\
\theta_{t+1} &= \theta_{t} - \frac{RMS[\Delta \theta]_{t-1}}{RMS[g]_t} g_t \\
\mathbb{E}[\Delta \theta]_t &= \gamma \mathbb{E}[\Delta \theta]_{t - 1} + (1 - \gamma) \Delta \theta_t \\
RMS[\Delta \theta]_{t} &= \sqrt{\mathbb{E}[\Delta \theta]_t}
\end{aligned}
$$
Плюс в том, что не нужно выбирать LR.
# Adam
Модификация RMSProp. Будем дополнительно считать несмещенные оценки на первые и вторые моменты.
$$
\begin{aligned}
m_t &= \beta m_{t - 1} + (1- \beta)g_t \\
G_t &= \gamma G_{t - 1} + (1- \gamma)g_t^2 \\
m_t &= \frac{m_t}{1 - \beta^t} \\
G_t &= \frac{G_t}{1 - \gamma^t} \\
\theta_{t+1} &= \theta_{t} - \alpha\frac{m_t}{\sqrt{G_t} + \epsilon}
\end{aligned}
$$
Суть нормирования на $1 - \beta^t$  и $1 - \gamma^t$ в том, что раскрыв скобки, можно убедиться, что без нормировки оценка получается смещенной. Чем дальше, тем меньше смещение, однако в начале оно довольно большое.