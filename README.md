# asksuite-athena-bq

Pipeline simples em Flask para executar queries no Amazon Athena e carregar os resultados em tabelas do Google BigQuery.

## Visão geral

Ao receber um `GET /`, a aplicação:

1. Executa uma query no Athena.
2. Aguarda a conclusão da execução.
3. Lê o CSV de resultado gravado no S3.
4. Carrega o DataFrame no BigQuery com `WRITE_TRUNCATE`.

Esse fluxo é repetido para 3 queries fixas, cada uma com destino em uma tabela BigQuery diferente.

## Estrutura do repositório

- `main.py`: aplicação Flask, função de execução Athena→BQ e queries SQL.
- `requirements.txt`: dependências Python.
- `Dockerfile`: imagem para deploy containerizado.

## Variáveis de ambiente importantes

- `AWS_DEFAULT_REGION` (opcional, padrão `us-east-1`)
- `GCP_PROJECT` (opcional, padrão `asksuite-salesops`)
- `PORT` (opcional, padrão `8080`)

## Pontos de atenção

- **Carga destrutiva no destino**: o uso de `WRITE_TRUNCATE` substitui toda a tabela a cada execução.
- **Sem timeout explícito no polling**: em falhas intermitentes, a espera no Athena pode se prolongar.
- **Sem autenticação no endpoint**: a rota `/` dispara o pipeline diretamente.
- **Queries acopladas ao código**: SQLs inline facilitam começar, mas dificultam manutenção com o tempo.

## Execução local

```bash
pip install -r requirements.txt
python main.py
```

Depois, acione:

```bash
curl http://localhost:8080/
```
