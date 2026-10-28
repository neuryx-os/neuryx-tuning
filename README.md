# neuryx-tuning

Perfis de ajuste do sistema para inferência de IA (tuned, sysctl, limites, GPU).

**Status:** Em pesquisa

> **Nenhum arquivo deste repositório está validado.** Todos os parâmetros
> estão comentados, como candidatos, até serem medidos conforme
> [`bench/README.md`](bench/README.md) e o
> [tópico 02](https://github.com/neuryx-os/research/blob/main/topics/02-ganhos-reais-de-tuning.md).

## Propósito

Reunir os ajustes de sistema que comprovadamente melhoram a inferência de
modelos locais e empacotá-los como `neuryx-tuning`, instalado pelo
`patterns-neuryx`.

## Estrutura

```
neuryx-tuning/
├── tuned/neuryx-inference/tuned.conf      # governor, energy_perf_bias
├── sysctl.d/90-neuryx.conf                # vm.swappiness, vm.nr_hugepages
├── security/limits.d/90-neuryx-memlock.conf  # memlock do grupo de inferência
├── udev/90-neuryx-nvidia.rules            # persistence mode da GPU
└── bench/README.md                        # como medir antes e depois
```

## Candidatos em avaliação

| Ajuste | Arquivo | Hipótese |
| --- | --- | --- |
| Governor `performance` e `energy_perf_bias` | `tuned/neuryx-inference/tuned.conf` | Menor latência de geração em CPU |
| `vm.swappiness` baixo | `sysctl.d/90-neuryx.conf` | Evita swap de páginas do modelo |
| Hugepages | `sysctl.d/90-neuryx.conf`, `tuned.conf` | Menos falhas de TLB com modelos grandes |
| `memlock` ilimitado | `security/limits.d/90-neuryx-memlock.conf` | Permite `--mlock` sem erro |
| Persistence mode | `udev/90-neuryx-nvidia.rules` | Menor latência ao iniciar execuções na GPU |

## Relação com outros repositórios

- [`research`](https://github.com/neuryx-os/research): tópico 02 decide o que entra.
- [`neuryx-packages`](https://github.com/neuryx-os/neuryx-packages): empacota este repositório.
- [`neuryxctl`](https://github.com/neuryx-os/neuryxctl): instala o driver de que a regra udev depende (ADR 0004).

## Licença

[GPL-3.0](LICENSE).
