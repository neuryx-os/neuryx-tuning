# Benchmark de ajustes

Nenhum ajuste deste repositório é adotado sem medição. Este documento descreve
o procedimento; os detalhes finais (modelos de referência, número de repetições,
critério de ganho) serão definidos pelo
[tópico 02](https://github.com/neuryx-os/research/blob/main/topics/02-ganhos-reais-de-tuning.md).

## Ferramenta

`llama-bench`, incluído no pacote `llama-cpp`. Ele mede a velocidade de
processamento do prompt (`pp`) e de geração (`tg`) em tokens por segundo.

## Procedimento

1. **Registre o ambiente:** CPU, memória, GPU, versão do driver, versão do
   kernel, versão do llama.cpp (`llama-bench --version`) e o modelo com sua
   quantização e hash SHA-256.
2. **Fixe as condições:** máquina sem outras cargas, mesma temperatura inicial
   aproximada, mesmo modelo e mesmos parâmetros em todas as rodadas.
3. **Meça a linha de base,** sem o ajuste:

   ```bash
   llama-bench -m modelo.gguf -p 512 -n 128 -r 10 -o json > base.json
   ```

   Para GPU, acrescente `-ngl 99`.
4. **Aplique um único ajuste** por vez.
5. **Meça novamente** com exatamente os mesmos parâmetros:

   ```bash
   llama-bench -m modelo.gguf -p 512 -n 128 -r 10 -o json > ajuste.json
   ```
6. **Compare** média e desvio-padrão de `pp` e `tg`. Considere ganho apenas
   quando a diferença for maior que a variação entre repetições.
7. **Reverta** o ajuste antes de testar o próximo.

## Registro dos resultados

Os resultados (JSON do `llama-bench` e descrição do ambiente) devem ser
anexados à issue do tópico 02. O diretório `bench/results/` é ignorado pelo Git.
