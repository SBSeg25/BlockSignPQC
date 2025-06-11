# BlockSignPQC

[Vídeo de demonstração](https://youtu.be/CNKmvOyZqm0)

**BlockSignPQC** é um benchmark modular e extensível para avaliação de 
algoritmos de assinatura digital pós-quântica (PQC) em sistemas blockchain.
Ele permite medições diretas de desempenho criptográfico e simulações realistas
de redes blockchain por meio da integração com o simulador BlockSim.

## Índice

- [Objetivos](#objetivos)
- [Estrutura da Ferramenta](#estrutura-da-ferramenta)
- [Diretórios](#diretórios)
- [Pré-requisitos](#pré-requisitos)
- [Ambiente Virtual](#ambiente-virtual)
- [Lista de Parâmetros](#lista-de-parâmetros)
- [Execução](#execução)
- [Simulação](#simulação)
- [Adicionando Novos Algoritmos](#adicionando-novos-algoritmos)
- [Reprodução dos Experimentos](#reprodução-dos-experimentos-descritos-no-artigo)
- [Publicação](#publicação)
- [Licença](#licença)


## Objetivos

- Comparar algoritmos clássicos (*e.g.* *ECDSA*) e pós-quânticos (*e.g.* *Dilithium*, *Falcon*, *SPHINCS+*).
- Integrar novos algoritmos de forma contínua e modular.
- Simular o impacto sistêmico dos algoritmos em ambientes blockchain.

## Estrutura da Ferramenta

A ferramenta é dividida em três módulos principais:

1. **`sign_python`**: Executa os algoritmos e mede tempo de assinatura, verificação e geração de chaves.
2. **`blocksim`**: Simula redes blockchain usando os tempos coletados.
3. **`visualization`**: Gera gráficos a partir dos dados dos dois módulos anteriores.

### Diretórios e Arquivos
```bash
BlockSignPQC/
├── algorithms/           # Implementações dos algoritmos PQC (com ALGORITHMS e time_evaluation)
├── BlockSim/             # Código-fonte do simulador de blockchain (BlockSim)
├── resultados-artigo/    # Resultados completos utilizados no artigo
├── results/              # Resultados de execução em CSV e gráficos (não versionado)
├── visualization/        # Geração de gráficos a partir das execuções
├── venv/                 # Ambiente virtual Python (não versionado)
├── graph.py              # Script auxiliar de geração de gráficos
├── install.sh            # Script de instalação principal
├── LICENSE/              # Licença
├── main.py               # Script principal que orquestra todas as etapas
├── README.md             # Este arquivo de documentação
├── requirements.txt      # Dependências Python necessárias
├── run_experiment.sh     # Executa todos os experimentos descritos no artigo
├── save.py               # Funções de salvamento de resultados
├── sign_python.py        # Módulo de benchmark de algoritmos de assinatura
├── simulator.py          # Interface e execução do BlockSim com os dados obtidos
├── utils.py              # Funções utilitárias auxiliares
```

## Pré-requisitos

- [Python 3.11.2](https://www.python.org/downloads/release/python-3112/)
- [liboqs](https://github.com/open-quantum-safe/liboqs)
- [liboqs-python](https://github.com/open-quantum-safe/liboqs-python)

### Instalando Pré-requisitos:

Conceda permissão de execução ao script de instalação usando o comando.
```bash
chmod +x install.sh
```

Execute o comando abaixo para instalar os pré-requisitos.
```bash
./install.sh
```

>Recomenda-se utilizar a mesma versão do `liboqs` e do `liboqs-python`. Por padrão, estamos utilizando a versão `0.12.0`, definida nas variáveis no início do arquivo [install.sh](./install.sh).

#### Ambiente virtual

Antes de executar o BlockSignPQC, é preciso ativar o ambiente virtual.

Ativar o ambiente virtual.
```bash
source venv/bin/activate
```

Desativar o ambiente virtual.
```bash
deactivate
```

## Lista de Parâmetros  

**Descrição dos argumentos do BlockSignPQC.**

| Parâmetro          | Descrição                                            |
| ------------------ | ---------------------------------------------------- |
| `--sign`           | Lista de algoritmos de assinatura digital a serem avaliados. Suporta múltiplos valores, incluindo algoritmos clássicos (*e.g. *ECDSA*) e pós-quânticos (*e.g.* *Dilithium*, *Falcon*, *SPHINCS+*). |
| `--runs`           | Número de execuções de cada algoritmo para coleta de métricas.                                                  |
| `--warm-up`        | Número de execuções de aquecimento (*warm-up*) antes da medição principal, para estabilização de desempenho.    |
| `--levels`         | Define os níveis de segurança do *NIST* (1 a 5) dos algoritmos a serem testados. Pode receber múltiplos valores.|
| `--runs-simulator` | Número de execuções da simulação no *BlockSim*.                                                                 |
| `--list-sign`      | Exibe todos os algoritmos de assinatura disponíveis na ferramenta.                                              |
| `--help`           |  Exibe a mensagem de ajuda com a descrição de todos os argumentos disponíveis e instruções de uso da ferramenta.|


## Execução

Consulte os argumentos disponíveis utilizando a opção `--help`.
```bash
python main.py --help
```

```text
usage: main.py [-h]
               [--sign {sphincs-sha-f,mldsa,cross-rsdpg-balanced,cross-rsdp-fast,ecdsa,cross-rsdpg-fast,sphincs-sha-s,falcon,falcon-padded,cross-rsdp-balanced,mayo,sphincs-shake-f,cross-rsdp-small,cross-rsdpg-small,dilithium,sphincs-shake-s} [{sphincs-sha-f,mldsa,cross-rsdpg-balanced,cross-rsdp-fast,ecdsa,cross-rsdpg-fast,sphincs-sha-s,falcon,falcon-padded,cross-rsdp-balanced,mayo,sphincs-shake-f,cross-rsdp-small,cross-rsdpg-small,dilithium,sphincs-shake-s} ...]]
               [--levels {1,2,3,4,5} [{1,2,3,4,5} ...]] [--runs RUNS] [--warm-up WARM_UP] [--list-sign] [--runs-simulator RUNS_SIMULATOR]

BlockSignPQC

options:
  -h, --help            show this help message and exit
  --sign {sphincs-sha-f,mldsa,cross-rsdpg-balanced,cross-rsdp-fast,ecdsa,cross-rsdpg-fast,sphincs-sha-s,falcon,falcon-padded,cross-rsdp-balanced,mayo,sphincs-shake-f,cross-rsdp-small,cross-rsdpg-small,dilithium,sphincs-shake-s} [{sphincs-sha-f,mldsa,cross-rsdpg-balanced,cross-rsdp-fast,ecdsa,cross-rsdpg-fast,sphincs-sha-s,falcon,falcon-padded,cross-rsdp-balanced,mayo,sphincs-shake-f,cross-rsdp-small,cross-rsdpg-small,dilithium,sphincs-shake-s} ...]
                        Input list of digital signature algorithms (default: None)
  --levels {1,2,3,4,5} [{1,2,3,4,5} ...], -l {1,2,3,4,5} [{1,2,3,4,5} ...]
                        Nist levels (default: range(1, 6))
  --runs RUNS, -r RUNS  Number of executions (default: 1)
  --warm-up WARM_UP, -wp WARM_UP
                        Number of executions warm up (default: 0)
  --list-sign           List of variants digital signature algorithms (default: False)
  --runs-simulator RUNS_SIMULATOR
                        Number of simulator runs (default: 0)
```

### Lista de Algoritmo e suas Variantes

O comando abaixo exibe todos os algoritmos de assinatura digital disponíveis na ferramenta, tanto clássicos quanto pós-quânticos:
```bash
python main.py --list-sign
```

Também é possível limitar a exibição às variantes de níveis específicos de segurança (NIST levels):
```bash
python main.py --list-sign --levels <nist_levels>
```

**Exemplo:**

```bash
python main.py --list-sign --levels 1 3 5
```

### Execução dos Algoritmos

Use o comando abaixo para executar os testes de desempenho (sign, verify) dos algoritmos desejados, especificando:
```bash
python main.py --sign <algorithms> --runs <n> --warm-up <n> --levels <nist_levels>
```

**Exemplo**
```bash
python main.py --sign ecdsa mldsa falcon sphincs-sha-s sphincs-shake-f --runs 5 --warm-up 5 --levels 3 5
```

### Simulação no BlockSim

Use o argumento `--runs-simulator` para informar quantas vezes cada variante será executada no simulador.
```bash
python main.py --sign ecdsa mldsa falcon sphincs-sha-s sphincs-shake-f --runs 5 --warm-up 5 --levels 1 3 5 --runs-simulator 5
```

## Adicionando Novos Algoritmos

Para adicionar um novo algoritmo, crie um arquivo `.py` dentro de `algorithms/` com a estrutura abaixo:
```python
import pandas as pd

ALGORITHMS = {
    # Os níveis (1 a 5) podem ser definidos conforme a disponibilidade do algoritmo.
    # Não é obrigatório preencher todos os níveis.
    "algorithm_name": {
        <level_1>: "variant_name",
        <level_2>: "variant_name",
        <level_3>: "variant_name",
        <level_4>: "variant_name",
        <level_5>: "variant_name",
    }, ...
}

def time_evaluation(variant: str, runs: int):
    # Implementação do benchmark
    return pd.DataFrame({
        'variant': [variant] * runs,
        'sign': time_sign,
        'verify': time_verify
    })
```

## Reprodução dos Experimentos Descritos no Artigo

Esta seção apresenta o passo a passo para a reprodução dos experimentos descritos no artigo submetido ao **SBSeg 2025**.  
Os experimentos são automatizados e organizados para permitir validação independente das principais **reivindicações experimentais**.


> Os resultados completos utilizados no artigo estão disponíveis no diretório: [`resultados-artigo`](./resultados-artigo/)


### Ambiente de Execução

Os experimentos foram realizados em duas configurações de hardware:

- **Máquina M1**
  - AMD Ryzen 7 5800X
  - Ubuntu 22.04.5 LTS
  - 64 GB RAM

- **Máquina M2**
  - Intel(R) Core(TM) i7-9700
  - Debian GNU/Linux 12
  - 16 GB RAM


### Instalação e Preparação

1. Clone o repositório:

```bash
git clone https://github.com/PQC-PQS/BlockSignPQC.git
cd BlockSignPQC
```

2. Dê permissão de execução ao script:
```bash
chmod +x install.sh
```

3. Instale os pré-requisitos:

```bash
./install.sh
```

4. Ative o ambiente virtual:
```bash
source venv/bin/activate
```

### Reivindicação 1 - Comparação de Desempenho entre os Algoritmos

**Objetivo:** Avaliar os tempos de assinatura e verificação de diferentes algoritmos, considerando os níveis de segurança NIST 1, 3 e 5.
 
**Comando:**
```bash
python main.py --sign \
    ecdsa \
    mldsa \
    sphincs-sha-s \
    sphincs-sha-f \
    sphincs-shake-s \
    sphincs-shake-f \
    falcon \
    mayo \
    cross-rsdp-small \
    cross-rsdpg-small \
    cross-rsdp-balanced \
    cross-rsdpg-balanced \
    cross-rsdp-fast \
    cross-rsdpg-fast \
    --runs 10000 \
    --warm-up 1000 \
    --levels 1 3 5
```

**Configuração:**

- Flags utilizadas: `--sign`, `--runs`, `--warm-up`, `--levels`.

- Tempo estimado de execução: 5 a 6 horas, conforme máquinas M1 e M2.

- Resultados gerados: arquivos CSV e gráficos no diretório `./results/`.


### Reivindicação 2 - Avaliação do Impacto dos Algoritmos na Simulação Blockchain (BlockSim): 

**Objetivo:** Simular o impacto dos algoritmos nos tempos de verificação de blocos em uma rede blockchain, utilizando o simulador BlockSim, considerando os níveis de segurança NIST 1, 3 e 5.

**Comando:**
```bash
python main.py --sign \
    ecdsa \
    mldsa \
    sphincs-sha-s \
    sphincs-sha-f \
    sphincs-shake-s \
    sphincs-shake-f \
    falcon \
    mayo \
    cross-rsdp-small \
    cross-rsdpg-small \
    cross-rsdp-balanced \
    cross-rsdpg-balanced \
    cross-rsdp-fast \
    cross-rsdpg-fast \
    --runs 10000 \
    --warm-up 1000 \
    --levels 1 3 5 \
    --runs-simulator 1000
```

**Configuração:**

- Flags utilizadas: `--runs-simulator`, além das anteriores.

- Tempo estimado de execução: 9 a 10 horas, conforme máquinas M1 e M2.

- Resultados gerados: arquivos CSV e gráficos no diretório `./results`.


## Publicação

Esta ferramenta foi submetida ao **SBSeg 2025**, como parte do artigo:

> *BlockSignPQC: um benchmark para avaliação de algoritmos de assinatura digital pós-quântica em blockchains.*


## Licença

Este projeto é distribuído sob a licença MIT. Consulte o arquivo [`LICENSE`](./LICENSE) para mais detalhes.
