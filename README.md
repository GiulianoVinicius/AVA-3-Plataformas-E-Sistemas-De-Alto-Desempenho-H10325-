# AVA 3 - Plataformas e Sistemas de Alto Desempenho (H10325)

## Descrição

Este repositório contém uma simulação didática de consenso distribuído em pagamentos, desenvolvida em Python. O objetivo é demonstrar como diferentes políticas de confirmação afetam a aceitação de transações em um cluster de 5 nós, sem usar comunicação real de rede nem processar pagamentos reais.

O arquivo principal é o script `CÓDIGO`, que simula cenários como:

- operação normal;
- falha de servidor;
- timeout de heartbeat;
- partição de rede 2 + 3;
- partição 2 + 2 + 1;
- recuperação;
- duplicidade e idempotência;
- falha do líder;
- validação final das réplicas.

## Objetivo da prática

A prática compara dois critérios de confirmação:

- `unanimidade`: todos os 5 nós precisam responder;
- `maioria`: pelo menos 3 dos 5 nós precisam responder (quórum).

A ideia é mostrar como a política de consenso influencia a disponibilidade, a tolerância a falhas e a consistência do sistema.

## Como o código funciona

### 1. Estruturas principais

#### `Transacao`
Representa uma transação confirmada. Ela guarda:

- identificador;
- conta;
- valor em centavos.

Essa estrutura é imutável (`@dataclass(frozen=True)`), o que ajuda a evitar alterações acidentais durante a simulação.

#### `No`
Cada nó do cluster representa um servidor com:

- número identificador;
- estado online/offline;
- histórico local (`log`) de transações confirmadas.

Além disso, o atributo `total_centavos` calcula o total de valores confirmados pelo nó.

#### `Cluster`
É a classe central, responsável por:

- definir a política de confirmação;
- controlar os 5 nós;
- simular falhas e partições;
- eleger um líder;
- sincronizar os logs;
- processar transações;
- validar a consistência final.

### 2. Política de confirmação

A regra de quorum é definida por:

- `unanimidade`: necessidade de 5 confirmadores;
- `maioria`: necessidade de 3 confirmadores.

A propriedade `necessario` calcula esse mínimo automaticamente.

### 3. Falhas e heartbeat

A simulação permite:

- desligar um servidor;
- recuperar um servidor;
- particionar a rede em grupos distintos;
- simular heartbeats para detectar nós suspeitos de falha ou isolamento.

O método `heartbeat()` não prova que o nó caiu de fato; apenas indica suspeita, uma vez que o problema pode ser de rede ou isolamento.

### 4. Liderança

O líder é escolhido entre os nós ativos e aptos a formar um grupo com quorum. A lógica é simplificada e didática:

- considera grupos acessíveis;
- escolhe o grupo com maior quantidade de nós ativos;
- entre esses, escolhe o nó com log mais atualizado;
- se o líder anterior não estiver mais no melhor grupo, ocorre nova eleição simplificada.

### 5. Processamento de pagamentos

Ao tentar confirmar uma transação:

- o sistema verifica se existe quórum;
- sincroniza os nós do grupo elegível;
- rejeita transações com mesmo ID mas valores diferentes;
- evita cobrança duplicada para o mesmo identificador;
- confirma a transação somente se o quórum for atendido.

Essa checagem é importante porque simula idempotência, um conceito crítico em sistemas distribuídos.

### 6. Validação final

Ao fim da simulação, a função `validar()` garante que:

- não existam IDs duplicados nos logs;
- os logs convergem após recuperação;
- todas as réplicas terminam consistentes.

## Executando o projeto

No terminal, na raiz do projeto, execute:

```bash
python "CÓDIGO" --politica ambas
```

Também é possível rodar uma política específica:

```bash
python "CÓDIGO" --politica unanimidade
python "CÓDIGO" --politica maioria
```

O script gera um diretório de saída com o arquivo CSV de resultados:

```bash
resultados/resultados_pratica3.csv
```

## Saída esperada

O programa imprime um relatório de cada cenário, mostrando:

- status da transação;
- líder atual;
- quantidade de confirmações;
- mínimo exigido;
- resultado do cenário.

Além disso, ao final, compara os resultados das duas políticas em uma tabela.

## Observação importante

Este código é uma simulação didática para fins acadêmicos. Ele não implementa:

- Raft;
- Paxos;
- comunicação real em rede;
- processamento de pagamentos reais.

Ele serve para estudar conceitos e comportamento de consenso distribuído de forma visual e simplificada.

## Autor

Trabalho acadêmico de AVA 3 - Plataformas e Sistemas de Alto Desempenho (H10325).

## Licença

Sem licença específica definida no repositório.

---

Se quiser, também posso transformar este README em uma versão mais institucional, com capa, objetivos, metodologia, resultados e conclusão do relatório acadêmico.

