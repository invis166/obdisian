Суть в следующем:
* Реплецируем модель на несколько карточек
* На каждую карточку посылаем свой батч данных
* На каждой карточке независимо делаем forward + backward
* Усредняем градиенты на всех карточках при помощи [[all_reduce]] 
Как итог получаем процесс, эквивалентный обучению с большим батчем. Легко заметить, что работает только если модель целиком влезает на карточку.

Варианты того, как готовить:
* torch.DistributedDataParallel
* torch.DataParallel
* Accelerate
* Transformers trainer

[1. torch.distributed doc](https://pytorch.org/docs/stable/distributed.html)
[2. DistributedDataParallel notes](https://pytorch.org/docs/master/notes/ddp.html)
[3. pytorch distributed overview](https://pytorch.org/tutorials/beginner/dist_overview.html)
[4. writing distributed applications with pytorch](https://pytorch.org/tutorials/intermediate/dist_tuto.html)


## torch.DistibutedDataParallel vs torch.DataParallel
Разница в том, что первый работает в мультипроцессорном режиме и поддерживает обучение на нескольких хостах, а второй работает в многопоточном режиме только на одном хосте. Предпочтительнее всегда использовать первый, потому что он быстрее.

[Объяснение из доки](https://pytorch.org/tutorials/intermediate/ddp_tutorial.html#comparison-between-dataparallel-and-distributeddataparallel)

## torch.DistributedDataParallel vs Accelerate
Accelerate - это обертка над DistributedDataParallel, которая позволяет не писать boilerplate код.

1. [Cравнение DDP vs Accelerate vs Transformers Trainer](https://huggingface.co/blog/pytorch-ddp-accelerate-transformers) 
2. [Pytohrch paper with implementation details](https://arxiv.org/pdf/2006.15704)
