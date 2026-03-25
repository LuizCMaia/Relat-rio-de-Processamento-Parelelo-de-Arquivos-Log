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

| Item                        | Descrição |
| --------------------------- | --------- |
| Processador                 | [PREENCHER COM O MODELO DO SEU PROCESSADOR, Ex: Intel Core i5-11400H] |
| Número de núcleos           | [PREENCHER COM O NÚMERO DE NÚCLEOS, Ex: 6 físicos / 12 lógicos] |
| Memória RAM                 | [PREENCHER COM SUA MEMÓRIA, Ex: 16 GB] |
| Sistema Operacional         | Windows 11 |
| Linguagem utilizada         | Python 3.13.2 |
| Biblioteca de paralelização | `multiprocessing` (módulo nativo do Python) |
| Compilador / Versão         | CPython 3.13.2 (64-bit) |

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
| 1                    | 115.9621              |
| 2                    | 87.1972               |
| 4                    | 44.6254               |
| 8                    | 40.0523               |
| 12                   | 37.5177               |

---

# 5. Cálculo de Speedup e Eficiência

## Fórmulas Utilizadas

### Speedup