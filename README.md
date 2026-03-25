# Relatório de Processamento Paralelo de Arquivos de Log

**Disciplina: Sistemas de Informação** 
**Aluno(s): Luiz Maia**
**Turma: SI-Noturno**
**Professor: Rafael**
**Data: 25/03/2026**

---

# 1. Descrição do Problema

O problema computacional resolvido consiste no processamento de um grande volume de arquivos de texto (logs operacionais) para extração de métricas. O sistema realiza a leitura de múltiplos arquivos para contabilizar o número total de linhas, palavras, caracteres e a ocorrência de palavras-chave específicas ("erro", "warning", "info").

* **Problema implementado:** Leitura intensiva de arquivos (I/O bound) combinada com processamento de strings e contagem (CPU bound), onde a execução sequencial se torna um gargalo.
* **Algoritmo utilizado:** Busca sequencial em texto com simulação de carga de processamento pesado (loop de 1000 iterações vazio por linha). A complexidade aproximada é de $O(N * M)$, onde $N$ é o número de arquivos e $M$ é a quantidade de palavras por arquivo.
* **Tamanho da entrada:** Pasta `log2` contendo 1000 arquivos, totalizando 10.000.000 de linhas, 200.000.000 de palavras e 1.366.663.305 caracteres (aprox. 1.36 GB).
* **Objetivo da paralelização:** Reduzir o tempo de execução através da distribuição da carga de trabalho em múltiplos processos, utilizando o padrão produtor-consumidor (abstraído pelo `multiprocessing.Pool`), permitindo a análise concorrente de diferentes arquivos de log.

---

# 2. Ambiente Experimental

Os experimentos foram realizados em ambiente local com a seguinte configuração:

| Item                        | Descrição                                   |
| --------------------------- | ---------                                   |
| Processador                 | Intel Core i7-12700                         |
| Número de núcleos           | 12 físicos / 20 lógicos                     |
| Memória RAM                 | 16 GB 3200Mhz                               |
| Sistema Operacional         | Windows 11                                  |
| Linguagem utilizada         | Python 3.13.2                               |
| Biblioteca de paralelização | `multiprocessing` (módulo nativo do Python) |
| Compilador / Versão         | CPython 3.13.2 (64-bit)                     |

---

# 3. Metodologia de Testes

Os testes foram conduzidos executando o script de avaliação de logs variando a quantidade de processos (workers) no pool de paralelização. 

* **Medição de tempo:** Foi utilizada a função `time.time()` da biblioteca padrão do Python, capturando o timestamp (em segundos) imediatamente antes da distribuição dos arquivos e logo após a consolidação final dos resultados.
* **Execuções:** Foi realizada uma execução completa para cada configuração de processos.
* **Tamanho da entrada:** Fixado em 1000 arquivos do diretório `log2` para todas as baterias de teste.
* **Condições de execução:** Execução local na máquina do aluno, sujeita à concorrência normal de processos do sistema operacional Windows em segundo plano.

### Configurações testadas

Os experimentos foram realizados nas seguintes configurações:

* 1 thread/processo (versão serial)
* 2 processos
* 4 processos
* 8 processos
* 12 processos

---

# 4. Resultados Experimentais

Abaixo estão os tempos de execução totais obtidos para o processamento integral da carga de dados:

| Nº Threads/Processos | Tempo de Execução (s) |
| -------------------- | --------------------- |
| 1                    | 99.7823               |
| 2                    | 49.5721               |
| 4                    | 27.7637               |
| 8                    | 14.3304               |
| 12                   | 12.7076               |

---

# 5. Cálculo de Speedup e Eficiência

## Fórmulas Utilizadas

### Speedup

```
Speedup(p) = T(1) / T(p)
```

Onde:

* **T(1)** = tempo da execução serial
* **T(p)** = tempo com p threads/processos

### Eficiência

```
Eficiência(p) = Speedup(p) / p
```

Onde:

* **p** = número de threads ou processos

---

# 6. Tabela de Resultados

| Threads/Processos | Tempo (s) | Speedup | Eficiência |
| ----------------- | --------- | ------- | ---------- |
| 1                 | 99.7823   | 1.00    | 1.00       |
| 2                 | 49.5721   | 2.01    | 1.00       |
| 4                 | 27.7637   | 3.59    | 0.89       |
| 8                 | 14.3304   | 6.96    | 0.87       |
| 12                | 12.7076   | 7.85    | 0.65       |

---

# 7. Gráfico de Tempo de Execução

![Gráfico Tempo Execução](graficos/tempo_execucao.png)

---

# 8. Gráfico de Speedup

![Gráfico Speedup](graficos/speedup.png)

---

# 9. Gráfico de Eficiência

![Gráfico Eficiência](graficos/eficiencia.png)

---

# 10. Análise dos Resultados

**O speedup obtido foi próximo do ideal?**
Sim, muito próximo do ideal até 8 processos. O speedup com 2 processos foi de 2.01x (perfeito), com 4 foi de 3.59x e com 8 processos atingiu quase 7x. 

**A aplicação apresentou escalabilidade?**
A aplicação apresentou uma escalabilidade excelente e praticamente linear do início até o marco de 8 processos, com reduções de tempo extremamente significativas em cada etapa.

**Em qual ponto a eficiência começou a cair?**
A eficiência manteve-se incrivelmente alta (acima de 87%) do 1º ao 8º processo. A queda de eficiência só ficou perceptível ao utilizar 12 processos, onde desceu para 0.65.

**Houve overhead de paralelização?**
Houve pouquíssimo overhead até 8 processos. O overhead de comunicação Inter-Processos (IPC) e de leitura de disco só se tornou evidente ao tentar forçar a concorrência para 12 processos. Neste ponto (12), o ganho de tempo foi marginal (caiu apenas 1,6 segundos em relação a 8 processos), indicando que os processos começaram a competir por recursos físicos da máquina (concorrência de leitura no disco ou saturação de CPU).

# 11. Conclusão

O experimento demonstra que a paralelização trouxe um ganho de desempenho espetacular para esta aplicação, derrubando o tempo total de aproximadamente 1 minuto e 40 segundos (99.7s) para apenas 12 segundos.

Para o ambiente testado, o número de processos ideal foi o de **8 processos**, pois entregou um tempo de execução extremamente baixo (14.3s) mantendo uma eficiência de quase 90% dos recursos computacionais. A aplicação escala de forma muito próxima à ideal, demonstrando que o algoritmo produtor-consumidor no Python soube isolar a carga corretamente.
